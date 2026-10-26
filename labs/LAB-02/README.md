# LAB-02 — Administración segura de Ubuntu Server

## 1. Descripción

Segundo laboratorio práctico del proyecto `indie-label-infra`. Se centra en la administración de un servidor Ubuntu Server, el acceso remoto seguro, la configuración de red, el firewall, la revisión de servicios y la automatización mediante Bash.

El laboratorio continúa el trabajo de base realizado en LAB-01 sobre la máquina `SRV-LNX-01`.

## 2. Objetivos

- Verificar el estado del sistema y sus servicios.
- Comprobar interfaces, rutas, conectividad y resolución DNS.
- Revisar y validar el acceso remoto mediante SSH.
- Limitar el acceso entrante con UFW.
- Auditar usuarios, grupos, permisos y privilegios.
- Revisar registros del sistema e incidencias.
- Desarrollar y probar una tarea de automatización Bash.
- Documentar evidencias y validar los resultados.

## 3. Entorno

| Parámetro | Valor |
|---|---|
| Máquina virtual | `SRV-LNX-01` |
| Sistema operativo | Ubuntu Server 26.04.1 LTS |
| Kernel comprobado | `7.0.0-38-generic` |
| Hipervisor | VirtualBox 7.2.16 |
| Usuario administrativo | `sysadmin` |
| Interfaz NAT | `enp0s3` — `10.0.2.15/24` |
| Interfaz Host-Only | `enp0s8` — `192.168.56.101/24` |
| Host Windows Host-Only | `192.168.56.1/24` |

## 4. Estado inicial documentado

- El acceso SSH desde Windows a través de Host-Only funciona.
- El servicio utiliza activación por socket mediante `ssh.socket`.
- UFW está activo, con entrada denegada por defecto y salida permitida.
- La regla SSH comprobada permite el acceso desde `192.168.56.1` a través de `enp0s8`.
- La conectividad IP a `1.1.1.1` se comprobó sin pérdida de paquetes en una prueba de cuatro paquetes.
- `resolvectl query ubuntu.com` devolvió direcciones IP, por lo que la resolución DNS está operativa.

Estos resultados no sustituyen las comprobaciones pendientes de servicios, cuentas, permisos, registros y automatización.

## 5. Metodología

1. Auditar antes de modificar.
2. Registrar el resultado observado.
3. Aplicar únicamente cambios justificados.
4. Validar cada cambio.
5. Documentar incidencias y limitaciones.
6. Revisar los archivos antes de publicarlos en Git.

## 6. Estado del laboratorio

**EN CURSO.**

La configuración de red, la conectividad y el acceso SSH están parcialmente verificados. La auditoría de seguridad restante, la automatización Bash, las evidencias y la validación final están pendientes.

Consulta `CHECKLIST-CIERRE.md` para conocer el progreso.
