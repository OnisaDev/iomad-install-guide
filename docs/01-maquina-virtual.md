# 1. Máquina virtual, Ubuntu Server, SSH y configuración previa

## Descargar Ubuntu Server 24.04 LTS

Imagen ISO oficial: <https://ubuntu.com/download/server> (elegir la 24.04 LTS).

## Crear la máquina virtual en Windows

En el laboratorio se usó **VirtualBox** (<https://www.virtualbox.org>), que funciona en cualquier edición de Windows. Recursos usados: 4 GB de RAM, 2 CPU y 30 GB de disco.

Alternativa: **Hyper-V**, disponible solo en Windows 10 y 11 Pro, Enterprise y Education (no en Home). Hay que activar antes la virtualización por hardware en la BIOS/UEFI. Guía oficial: <https://ubuntu.com/server/docs/how-to/virtualisation/ubuntu-on-hyper-v/>.

**Reenvío de puertos de VirtualBox** (para llegar a la máquina desde el propio equipo):

| Uso | Puerto del equipo | Puerto de la máquina |
|---|---|---|
| SSH | 2222 | 22 |
| Web | 8080 | 80 |

### Valores usados en la prueba 

| Campo | Valor |
|---|---|
| Nombre | `iomad-lab` |
| Memoria | 3072 MB (suficiente con Ubuntu Server sin escritorio) |
| Procesadores | 2 |
| Disco | 30 GB, VDI, dinámico |
| Instalación desatendida | Omitida |
| Red | NAT con reenvío de puertos en `127.0.0.1` |
| Audio y USB | Desactivados |

En el instalador de Ubuntu: proxy en blanco, mirror por defecto, disco entero **sin LVM ni cifrado**, OpenSSH server marcado y ningún snap adicional.

## Instalar Ubuntu Server

- Tutorial oficial: <https://documentation.ubuntu.com/server/tutorial/>
- Guía con capturas: <https://www.thomas-krenn.com/en/wiki/Install_Ubuntu_24.04_LTS_Server>
- Al instalar, marcar **Install OpenSSH server**; hará falta para conectarse.

## Conectarse por SSH desde Windows

Desde PowerShell o Windows Terminal (el cliente OpenSSH suele venir incluido en Windows 10 y 11):

```powershell
ssh -p 2222 -l <usuario> 127.0.0.1
```

Referencia: sección "OpenSSH server" de <https://documentation.ubuntu.com/server>.

## Arranque diario del laboratorio

1. Arrancar la máquina virtual.
2. Iniciar Ubuntu e identificarse con usuario y contraseña.
3. Abrir PowerShell en el equipo.
4. Conectar por SSH (comando de arriba) e introducir la contraseña.
5. Abrir el navegador y cargar `http://localhost:8080`.
6. Entrar con el usuario correspondiente.

## Configuración previa del servidor 

Antes de instalar IOMAD conviene dejar el sistema comprobado, actualizado y protegido. Son pasos cortos que se hacen una sola vez.

### 1. Comprobar que la máquina está bien

```bash
lsb_release -a      # debe indicar Ubuntu 24.04.x LTS
df -h /             # la raíz debe ocupar casi todo el disco virtual
free -h             # memoria y swap disponibles
```

Si `df -h` muestra una raíz mucho más pequeña que el disco, hay que ampliarla antes de seguir (es habitual si el instalador usó LVM y dejó parte del disco sin asignar).

### 2. Actualizar el sistema

```bash
sudo apt update && sudo apt upgrade -y
```

Si al terminar pide reiniciar: `sudo reboot` y volver a conectar por SSH. Ignorar los avisos de "nueva versión disponible": **no ejecutar `do-release-upgrade`**, porque llevaría a una versión de Ubuntu con un PHP incompatible con IOMAD 4.5.

### 3. Zona horaria

```bash
sudo timedatectl set-timezone Europe/Madrid
timedatectl
```

Moodle usa la hora del servidor para fechas de cursos, registros y tareas programadas, así que conviene dejarla bien desde el principio.

### 4. Cortafuegos (UFW)

Un cortafuegos decide qué conexiones entrantes llegan al servidor. La idea es **abrir solo lo necesario** y bloquear el resto:

| Puerto | Para qué |
|---|---|
| 22 | SSH (administración) |
| 80 | Web (HTTP) |
| 443 | HTTPS, cuando se configure (ver [Producción](03-produccion.md)) |

```bash
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw enable
sudo ufw status verbose
```

**Importante: permitir SSH antes de activar el cortafuegos.** Si se activa sin la regla de SSH, la conexión remota se corta y no se puede volver a entrar por red (habría que usar la consola de la máquina virtual para corregirlo).

Por defecto, UFW bloquea todo el tráfico entrante que no esté permitido y deja salir el saliente. `ufw status verbose` debe mostrar `Status: active` y las reglas de 22 y 80.

En este laboratorio el acceso llega por el reenvío de puertos de VirtualBox (2222→22 y 8080→80), que sigue funcionando igual porque dentro de la máquina los puertos son el 22 y el 80.

### 5. Instantánea `servidor-base`

Apagar con `sudo poweroff` y, en VirtualBox, crear una instantánea con la máquina apagada. Así la base ya incluye las actualizaciones, la zona horaria y el cortafuegos, y si la instalación sale mal se vuelve a este punto en un minuto.

Siguiente paso: [Instalación de IOMAD](02-instalacion-iomad.md).

## Comandos básicos y mantenimiento

- Primeros pasos tras instalar: <https://linuxconfig.org/ubuntu-server-tutorial-for-beginners>
- 25 comandos esenciales (`ls`, `cd`, `cp`, `chmod`, `chown`, `nano`, `apt`...): <https://fosslinux.com/25-essential-commands-for-new-ubuntu-server-users>
- Documentación oficial: <https://ubuntu.com/server/docs>
