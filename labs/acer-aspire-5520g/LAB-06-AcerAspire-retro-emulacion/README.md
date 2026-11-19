# LAB-06 — Recuperación y configuración de un equipo de emulación retro

## 1. Descripción

Recuperación de un ordenador portátil Acer Aspire 5520G para convertirlo
en una plataforma de emulación retro basada en Linux Mint.

El laboratorio comprende la preparación del equipo, la instalación del
sistema operativo, la incorporación de RetroArch y ES-DE, la organización
de la biblioteca de juegos y la integración de ambos programas.

ES-DE (EmulationStation Desktop Edition) proporciona la interfaz gráfica
para organizar y seleccionar los juegos. RetroArch los ejecuta mediante
los núcleos de emulación Libretro.

Este trabajo constituye la primera fase del proyecto de reutilización
del equipo. Las siguientes fases abordarán su conversión en servidor
de juegos y el refuerzo de su seguridad.

## 2. Objetivos

- Recuperar un equipo portátil antiguo y darle un uso funcional.
- Preparar un sistema Linux de escritorio.
- Instalar y configurar RetroArch.
- Incorporar ES-DE como interfaz de gestión de juegos.
- Organizar las ROM por plataforma.
- Asociar cada sistema con su núcleo de RetroArch.
- Verificar el lanzamiento de juegos desde ES-DE.
- Documentar la configuración y las evidencias.

## 3. Hardware y sistema operativo

| Parámetro | Configuración |
|---|---|
| Equipo | Acer Aspire 5520G |
| Procesador | AMD Turion 64 X2 Mobile TL-60 |
| Núcleos | 2 |
| Memoria RAM | 3 GiB DDR2 |
| BIOS | Acer V1.30 |
| Fecha BIOS | 31/12/2007 |
| Sistema operativo | Linux Mint 22.1 Xia |
| Arquitectura | x86_64 |
| Kernel | 6.8.0-124-generic |

## 4. Software instalado

| Aplicación | Versión | Método |
|---|---|---|
| RetroArch | 1.22.2 | Flatpak (Flathub) |
| ES-DE | 3.5.0 r52 | AppImage |
| Flatpak | 1.14.6 | Gestor de paquetes |

### Rutas principales

- ES-DE: `~/Aplicaciones/ES-DE/ES-DE.AppImage`
- Lanzador ES-DE: `~/.local/share/applications/es-de.desktop`
- Configuración ES-DE: `~/.config/es-de/es_systems.cfg`
- Configuración RetroArch: `~/.var/app/org.libretro.RetroArch/config/retroarch/retroarch.cfg`

## 5. Biblioteca de juegos

La biblioteca se encuentra en `~/juegos/` y está organizada por
plataforma:

| Sistema | Directorio |
|---|---|
| Dreamcast | `~/juegos/dreamcast` |
| Mega Drive | `~/juegos/megadrive` |
| Nintendo 64 | `~/juegos/n64` |
| Neo Geo | `~/juegos/neogeo` |
| NES | `~/juegos/nes` |
| PlayStation | `~/juegos/psx` |
| Sega Saturn | `~/juegos/saturn` |
| Super Nintendo | `~/juegos/snes` |

El tamaño aproximado de la biblioteca es de 10 GB.

## 6. Integración ES-DE y RetroArch

ES-DE está configurado para lanzar RetroArch mediante Flatpak,
utilizando el núcleo correspondiente a cada plataforma.

| Sistema | Núcleo Libretro |
|---|---|
| NES | `fceumm_libretro.so` |
| Super Nintendo | `snes9x_libretro.so` |
| Mega Drive | `genesis_plus_gx_libretro.so` |
| Neo Geo | `fbneo_libretro.so` |
| PlayStation | `mednafen_psx_libretro.so` |
| Sega Saturn | `mednafen_saturn_libretro.so` |
| Nintendo 64 | `mupen64plus_next_libretro.so` |
| Dreamcast | `flycast_libretro.so` |

Los núcleos se encuentran en:

`~/.var/app/org.libretro.RetroArch/config/retroarch/cores`

La configuración de RetroArch también contempla directorios específicos
para partidas guardadas, estados de emulación y archivos de sistema.

## 7. Mantenimiento y limpieza

Durante la revisión del entorno se retiró una instalación antigua de
ES-DE ubicada en `/usr/local`.

Se mantuvo la instalación actual mediante AppImage y su lanzador
de usuario, evitando conservar dos instalaciones simultáneas.

Se conservó una copia de seguridad de la instalación anterior en:

`~/Documentos/backup-es-de-antiguo`

Tras la limpieza se comprobó el funcionamiento de los juegos.

## 8. Validación

Se verificaron los siguientes puntos:

- Inicio correcto de Linux Mint.
- Ejecución de RetroArch.
- Instalación de RetroArch mediante Flatpak.
- Inicio de ES-DE mediante AppImage.
- Detección y visualización de los sistemas configurados.
- Acceso a la biblioteca de juegos.
- Asociación de los sistemas con sus núcleos.
- Lanzamiento y ejecución de juegos desde ES-DE.
- Funcionamiento posterior a la limpieza de la instalación antigua.

## 9. Evidencias

Las capturas se encuentran en `evidencias/`:

| Archivo | Contenido |
|---|---|
| `01-es-de-sistemas.png` | Sistemas visibles en ES-DE |
| `02-es-de-biblioteca.png` | Biblioteca de juegos |
| `03-juego-ejecutandose.png` | Juego en ejecución |
| `04-retroarch.png` | Interfaz de RetroArch |
| `05-retroarch-flatpak.png` | Instalación de RetroArch mediante Flatpak |
| `06-version-es-de.png` | Versión de ES-DE |
| `07-configuracion-es-de.png` | Configuración de sistemas de ES-DE |

## 10. Alcance y siguientes fases

Este laboratorio documenta la recuperación del equipo y su puesta
en funcionamiento como plataforma de emulación retro.

Quedan fuera de su alcance:

- LAB-07: conversión del equipo en servidor de juegos, gestión de
  servicios, usuarios, grupos y permisos.
- LAB-08: auditoría de seguridad y hardening del servidor.

El repositorio de código fuente de ES-DE presente en el equipo es
un proyecto externo y no forma parte del desarrollo propio de este
laboratorio.

## 11. Estado del laboratorio

**Estado:** funcionalidad verificada. Documentación inicial preparada
para revisión e integración en el repositorio `indie-label-infra`.
