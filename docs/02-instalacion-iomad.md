# 2. Instalación de IOMAD

## Sistema y paquetes 

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y apache2 php8.3 libapache2-mod-php8.3 php8.3-mysql php8.3-curl \
  php8.3-gd php8.3-intl php8.3-mbstring php8.3-xml php8.3-zip php8.3-soap \
  php8.3-opcache git unzip
```
Comprobación:

```bash
apache2 -v
php -v
sudo systemctl status apache2 --no-pager
```

Versiones obtenidas: Apache 2.4.58 y PHP 8.3.6. Desde el navegador del equipo, `http://localhost:8080` debe mostrar la página "It works!" de Apache, lo que confirma el reenvío de puertos y el cortafuegos.

El instalador web comprueba las extensiones; si falta alguna, se instala con `apt`.

## MariaDB y base de datos

```bash
sudo apt install -y mariadb-server
sudo mysql_secure_installation
sudo mariadb
```
Comprobación de la versión y del usuario:

```bash
mariadb --version
mariadb -u iomaduser -p iomad -e "SELECT 'ok';"
```

Versión obtenida: MariaDB 10.11.14. El segundo comando pide la contraseña de `iomaduser` y debe devolver una tabla con `ok`.

En el asistente: poner contraseña de root de MariaDB y responder `Y` al resto. Dentro de `mariadb`:

```sql
CREATE DATABASE iomad DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'iomaduser'@'localhost' IDENTIFIED BY 'CLAVE_SEGURA';
GRANT ALL PRIVILEGES ON iomad.* TO 'iomaduser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

Anotar nombre de base de datos, usuario y contraseña (se piden en el instalador) y no volcarlos en documentos compartidos.

## Ajuste de PHP 

```bash
sudo sed -i 's/^;\?max_input_vars.*/max_input_vars = 5000/' \
  /etc/php/8.3/apache2/php.ini /etc/php/8.3/cli/php.ini
grep max_input_vars /etc/php/8.3/apache2/php.ini
```

Si habrá vídeos o ficheros grandes, ajustar también `upload_max_filesize`, `post_max_size` y `memory_limit`.

## Código de IOMAD 

```bash
git ls-remote --heads https://github.com/iomad/iomad | grep -i 405
sudo git clone --depth 1 --branch IOMAD_405_STABLE https://github.com/iomad/iomad.git /var/www/iomad
```

Se instala el paquete completo de IOMAD, **no** Moodle más plugins. Hay que actualizar siempre con el código de IOMAD, nunca con el de Moodle estándar.

## Carpeta de datos y permisos de instalación

```bash
sudo mkdir /var/moodledata
sudo chown -R www-data:www-data /var/moodledata
sudo chmod 770 /var/moodledata
sudo chown -R www-data:www-data /var/www/iomad
```

`/var/moodledata` debe quedar **fuera** de la carpeta web. El código se deja propiedad de `www-data` solo durante la instalación.

## Apache 

```bash
sudo nano /etc/apache2/sites-available/iomad.conf
```

```apache
<VirtualHost *:80>
    ServerName localhost
    DocumentRoot /var/www/iomad
    <Directory /var/www/iomad>
        Options FollowSymLinks
        AllowOverride None
        Require all granted
        # FallbackResource /r.php   <- ver docs/04-problemas-conocidos.md
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/iomad_error.log
    CustomLog ${APACHE_LOG_DIR}/iomad_access.log combined
</VirtualHost>
```

En un servidor real, sustituir `ServerName localhost` por el nombre DNS.

```bash
sudo a2dissite 000-default
sudo a2ensite iomad
sudo apache2ctl configtest
sudo systemctl reload apache2
```

**Sobre `FallbackResource /r.php`:** es el router de Moodle. En el laboratorio, con esta línea activa, la página principal y el área personal daban "Not found". Es opcional en la 4.5 y obligatorio desde la 5.1. Detalle y solución en [Problemas conocidos](04-problemas-conocidos.md). Si se deja activa, antes de instalar la raíz dará error 500 (el router busca `config.php`, que aún no existe): abrir directamente `/install.php`.

## Instalador web

Abrir `http://NOMBRE/install.php` (en el laboratorio, `http://localhost:8080/install.php`) y rellenar:

- **Dirección web:** la URL real y definitiva (en producción, `https://...`). Cambiarla después es delicado. En el laboratorio se usó siempre `localhost`, no `127.0.0.1`.
- **Directorio de datos:** `/var/moodledata` (el instalador propone otro por defecto: corregirlo).
- **Base de datos:** tipo MariaDB, servidor `localhost`, nombre `iomad`, usuario `iomaduser`. Puerto y socket vacíos.
- **Prefijo de tablas:** en el laboratorio, `iom_`. No cambiarlo después.
- Comprobar que los requisitos salen en verde. En el laboratorio solo avisó de que no había HTTPS.
- La instalación de tablas tarda varios minutos: no recargar ni cerrar la pestaña.
- Crear la cuenta de administrador (contraseña con mayúscula, minúscula, número y carácter especial) y guardarla de forma segura.

## Cierre tras instalar

```bash
sudo chown -R root:root /var/www/iomad
sudo chown root:www-data /var/www/iomad/config.php
sudo chmod 640 /var/www/iomad/config.php
```

Con el código en `root` no se pueden instalar plugins desde el panel web: se copian a mano.

Siguiente paso: [Producción](03-produccion.md).
