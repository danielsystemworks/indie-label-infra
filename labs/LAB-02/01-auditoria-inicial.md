# LAB-02 — Auditoría inicial

## 1. Objetivo

Registrar el estado observado de `SRV-LNX-01` antes de completar la revisión de seguridad y administración.

## 2. Sistema

| Comprobación | Resultado |
|---|---|
| Hostname | `SRV-LNX-01` |
| Sistema operativo | Ubuntu Server 26.04.1 LTS |
| Kernel comprobado | `7.0.0-38-generic` |
| Hipervisor | VirtualBox 7.2.16 |
| Usuario de administración | `sysadmin` |
| Reinicio pendiente | No, según la comprobación realizada |

## 3. Red

| Elemento | Resultado observado |
|---|---|
| NAT | `enp0s3`, `10.0.2.15/24` |
| Host-Only | `enp0s8`, `192.168.56.101/24` |
| Host Windows | `192.168.56.1/24` |
| Prueba ICMP a `1.1.1.1` | 4 paquetes enviados, sin pérdida |
| Consulta DNS de `ubuntu.com` | Resuelta correctamente mediante `resolvectl` |

La prueba ICMP confirma conectividad IP en el momento de la prueba. No demuestra por sí sola la disponibilidad de todos los servicios de Internet.

La respuesta DNS mostró `Data is authenticated: no`. La resolución funciona, pero esa salida no confirma validación DNSSEC ni cifrado del transporte DNS.

## 4. SSH

- La conexión desde Windows mediante `ssh sysadmin@192.168.56.101` funciona.
- `ssh.socket` figura activo y escuchando en el puerto 22.
- La configuración del firewall restringe la regla SSH a la dirección Host-Only del equipo anfitrión y a la interfaz `enp0s8`.

Queda pendiente revisar la configuración efectiva de SSH, los métodos de autenticación, el acceso de `root` y las cuentas autorizadas.

## 5. Firewall

Configuración observada de UFW:

- Estado: activo.
- Entrada predeterminada: denegada.
- Salida predeterminada: permitida.
- Regla SSH: acceso desde `192.168.56.1` al puerto TCP 22 por `enp0s8`.

## 6. Comprobaciones pendientes

- Estado general de systemd y unidades fallidas.
- Servicios activos y sockets de escucha.
- Rutas y configuración completa de las interfaces.
- Usuarios, grupos, pertenencia a `sudo` y permisos relevantes.
- Configuración efectiva de SSH.
- Registros del sistema y análisis de incidencias.
- Diseño y pruebas de automatización Bash.

## 7. Evidencias

Guardar capturas o salidas de terminal saneadas en `evidencias/01-auditoria/`.

No incluir contraseñas, claves privadas, tokens ni otros secretos.

## 8. Estado

**Parcialmente completada.** Los datos anteriores corresponden a comprobaciones realizadas; no representan una auditoría integral finalizada.
