# indie-label-infra

Portfolio práctico de administración de sistemas, redes y ciberseguridad,
desarrollado mediante laboratorios técnicos progresivos.

El proyecto reproduce parcialmente la infraestructura IT de un sello
discográfico independiente simulado, combinando equipos físicos,
máquinas virtuales, servicios Linux y herramientas de administración.

## Objetivos técnicos

- Administración de sistemas Linux y Windows.
- Configuración y diagnóstico de redes.
- Despliegue y administración de servicios.
- Gestión de usuarios, grupos y permisos.
- Servicios de directorio y autenticación.
- Auditoría, hardening y seguridad de sistemas.
- Automatización y documentación técnica.
- Recuperación y reutilización de hardware.

## Laboratorios

| Laboratorio | Área | Entorno |
|---|---|---|
| LAB-01 | Fundamentos de infraestructura | Entorno de laboratorio |
| LAB-02 | Administración y auditoría de sistemas | Linux |
| LAB-03 | Servicio de directorio LDAP | Ubuntu Server |
| LAB-04 | Servicio DHCP | Ubuntu Desktop |
| LAB-05 | Auditoría del kernel y módulos | Debian 13 — WS-13-LNX-O1 |
| LAB-06 | Recuperación y emulación retro | Acer Aspire 5520G — Linux Mint |
| LAB-07 | Servidor de juegos y gestión de servicios | Acer Aspire 5520G |
| LAB-08 | Auditoría y hardening | Acer Aspire 5520G |

Los laboratorios se documentan de forma independiente, con sus propios
objetivos, procedimientos, configuraciones y evidencias.

## Proyecto Acer Aspire 5520G

La reutilización del Acer Aspire 5520G constituye una línea de trabajo
específica dentro del repositorio:

1. LAB-06: recuperación del equipo y configuración de una plataforma
   de emulación retro con Linux Mint, RetroArch y ES-DE.
2. LAB-07: evolución hacia un servidor de juegos y servicios de red.
3. LAB-08: auditoría de seguridad y aplicación de medidas de hardening.

## Estructura del repositorio

- `labs/`: laboratorios técnicos y sus evidencias.
- `docs/`: documentación general y bitácora del proyecto.

## Metodología

Cada laboratorio registra el entorno utilizado, las tareas realizadas,
las comprobaciones efectuadas y las evidencias relevantes.

Se priorizan los procedimientos reproducibles, la separación de entornos
y la documentación de los cambios realizados.

## Estado del proyecto

Proyecto en desarrollo. Los laboratorios se incorporan progresivamente
a medida que se completan y validan.
