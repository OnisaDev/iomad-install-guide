# 3. Producción 

## Requisitos previos de un servidor real

- Distribución y versión (`lsb_release -a`). Si no es Ubuntu 24.04, comprobar que dispone de PHP 8.1 a 8.3 y MariaDB 10.6.7 o superior.
- Servidor dedicado o máquina virtual con al menos 4 GB de RAM, 2 CPU y 30 GB de disco (valores del laboratorio; dimensionar según usuarios).
- Acceso `sudo` para la instalación.
- Nombre DNS y certificado HTTPS (uno por dirección si habrá varias).
- Puertos 80 y 443 abiertos hacia Internet; el 22 solo para administración.
- Política de copias de seguridad y responsable de aplicar parches.
- Servidor SMTP de salida para los correos de la plataforma.

## Cron (imprescindible)

Necesario para correos, informes y tareas programadas. Cada minuto:

```bash
echo '* * * * * www-data /usr/bin/php /var/www/iomad/admin/cli/cron.php > /dev/null' | sudo tee /etc/cron.d/iomad
sudo -u www-data /usr/bin/php /var/www/iomad/admin/cli/cron.php
```

El segundo comando lo ejecuta una vez a mano para comprobarlo.

## HTTPS

Certificado válido (propio de la organización, o Let's Encrypt si hay dominio público). Esquema:

```bash
sudo a2enmod ssl headers
```

```apache
<VirtualHost *:80>
    ServerName plataforma.ejemplo.es
    Redirect permanent / https://plataforma.ejemplo.es/
</VirtualHost>
<VirtualHost *:443>
    ServerName plataforma.ejemplo.es
    DocumentRoot /var/www/iomad
    SSLEngine on
    SSLCertificateFile /ruta/certificado.crt
    SSLCertificateKeyFile /ruta/clave.key
    <Directory /var/www/iomad>
        Options FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```

**Una dirección por empresa** (para marca propia en el acceso): un nombre DNS por empresa apuntando al mismo servidor, añadido con `ServerAlias` y cubierto por el certificado (o uno comodín). Cómo asocia IOMAD cada dirección a su empresa: consultar <https://docs.iomad.org>.

## Cortafuegos y SSH

- Permitir solo 22 (administración), 80 y 443.
- SSH con claves y sin contraseña para `root`.

## Correo saliente

Configurar el SMTP en *Administración del sitio*.

## Copias de seguridad

Base de datos, carpeta de datos y `config.php`:

```bash
sudo mariadb-dump --single-transaction --routines iomad | gzip > /copias/iomad_$(date +%F).sql.gz
sudo tar czf /copias/moodledata_$(date +%F).tar.gz -C /var moodledata
```

Probar la **restauración** en otra máquina antes de dar por buena la copia.

## Actualizaciones de seguridad

Aplicar con regularidad los parches de Ubuntu, PHP y MariaDB, y las versiones menores de IOMAD cuando salgan.

## Verificación final

- [ ] Acceso por la URL definitiva y con HTTPS.
- [ ] Entrada como administrador y panel de IOMAD visibles.
- [ ] Cron ejecutándose (ver tareas programadas en el panel).
- [ ] Copia de seguridad hecha y restauración probada.
- [ ] Permisos del código cerrados.
- [ ] Cortafuegos y SSH revisados.
- [ ] Instantánea o copia del servidor tras la instalación limpia.

## Actualizaciones futuras

- IOMAD 4.5 LTS y Moodle 4.5 LTS: soporte de seguridad hasta octubre de 2027. Planificar el salto a la versión de IOMAD basada en Moodle 5.3 LTS, tras su publicación y maduración.
- Moodle 5.3 pide MariaDB 11.4 o PostgreSQL 17 como mínimo, y PHP 8.3 a 8.4.
- Desde Moodle 5.1, la raíz del servidor web debe apuntar a la carpeta `public` y el router es obligatorio.
- Actualizar siempre con el código de IOMAD (procedimiento en <https://docs.iomad.org>; no verificado aquí).
- Probar cada actualización en un entorno de pruebas con copia de datos.
