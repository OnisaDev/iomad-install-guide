# 1. Máquina virtual, Ubuntu Server y SSH

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

## Instalar Ubuntu Server

- Tutorial oficial: <https://documentation.ubuntu.com/server/tutorial/>
- Guía con capturas: <https://www.thomas-krenn.com/en/wiki/Install_Ubuntu_24.04_LTS_Server>
- Al instalar, marcar **Install OpenSSH server**; hará falta para conectarse.

## Conectarse por SSH desde Windows

Desde PowerShell o Windows Terminal:

ssh -p 2222 -l <usuario> 127.0.0.1


Referencia: sección "OpenSSH server" de <https://documentation.ubuntu.com/server>.

## Arranque diario del laboratorio

1. Arrancar la máquina virtual.
2. Iniciar Ubuntu e identificarse con usuario y contraseña.
3. Abrir PowerShell en el equipo.
4. Conectar por SSH (comando de arriba) e introducir la contraseña.
5. Abrir el navegador y cargar `http://localhost:8080`.
6. Entrar con el usuario correspondiente.

## Comandos básicos y mantenimiento

- Primeros pasos tras instalar: <https://linuxconfig.org/ubuntu-server-tutorial-for-beginners>
- 25 comandos esenciales (`ls`, `cd`, `cp`, `chmod`, `chown`, `nano`, `apt`...): <https://fosslinux.com/25-essential-commands-for-new-ubuntu-server-users>
- Documentación oficial: <https://ubuntu.com/server/docs>
