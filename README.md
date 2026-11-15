# Guía de instalación de IOMAD 4.5 LTS en Ubuntu Server 24.04

Guía práctica para montar una plataforma **IOMAD** (Moodle multi-empresa) en un servidor Linux, con las decisiones, los comandos y los problemas reales que aparecieron al hacerlo en un laboratorio con VirtualBox.

## Entorno del laboratorio

| Componente | Versión | Nota |
|---|---|---|
| Ubuntu Server | 24.04.5 LTS | No ejecutar `do-release-upgrade`: llevaría a 26.04, con una versión de PHP incompatible con 4.5 |
| Apache | 2.4.58 | Con `mod_php` en el laboratorio |
| PHP | 8.3.6 | Moodle 4.5 admite de 8.1 a 8.3 |
| MariaDB | 10.11.14 | Mínimo 10.6.7 para Moodle 4.5 |
| IOMAD | 4.5 LTS (rama `IOMAD_405_STABLE`), release 4.5.16 | No usar la rama `IOMAD_405_EXPERIMENTAL` |
| Virtualización | VirtualBox | Máquina con 4 GB de RAM, 2 CPU y 30 GB de disco |

Fechas de soporte: Moodle 4.5 LTS y IOMAD 4.5 LTS hasta octubre de 2027; PHP 8.3 con seguridad hasta el 31 de diciembre de 2027.

## Índice

1. [Máquina virtual, Ubuntu Server y SSH](docs/01-maquina-virtual.md)
2. [Instalación de IOMAD](docs/02-instalacion-iomad.md): paquetes, MariaDB, PHP, código, permisos, Apache e instalador web
3. [Producción](docs/03-produccion.md): HTTPS, cron, copias de seguridad, seguridad y actualizaciones
4. [Problemas conocidos](docs/04-problemas-conocidos.md): error "Not found" en la página principal y el área personal
5. [Demo con datos de prueba](docs/05-demo-y-pendientes.md)

## Aviso

Todos los nombres, usuarios, contraseñas y dominios de esta guía son ejemplos (`iomaduser`, `CLAVE_SEGURA`, `plataforma.ejemplo.es`). 
