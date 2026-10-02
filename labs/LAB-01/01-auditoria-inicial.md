# LAB-01 — Auditoría inicial

## 1. Objetivo

Realizar una auditoría inicial de `SRV-LNX-01` antes de aplicar medidas de hardening.

La finalidad es obtener una línea base del sistema y detectar:

* configuración del sistema operativo;
* hardware virtual;
* red;
* almacenamiento;
* usuarios y privilegios;
* servicios activos;
* paquetes instalados;
* puertos de escucha;
* mecanismos de seguridad;
* posibles incidencias.

La regla aplicada durante esta fase es:

> **Auditar primero. Modificar después.**

No se realizan cambios basándose únicamente en la presencia de un paquete o servicio.

---

# 2. Identificación del sistema

Se comprobó la versión del sistema operativo y del kernel.

```bash
lsb_release -a
uname -r
```

Sistema identificado:

```text
Ubuntu Server 26.04.1 LTS
Kernel: 7.0.0-31-generic
```

La máquina se ejecuta dentro de VirtualBox.

```bash
systemd-detect-virt
```

Resultado:

```text
oracle
```

Esto confirma que el sistema operativo se ejecuta como máquina virtual bajo Oracle VirtualBox.

---

# 3. Recursos del sistema

Se revisaron los recursos asignados a la máquina virtual:

* 2 vCPU.
* Aproximadamente 3.3 GiB de RAM.
* Disco virtual de aproximadamente 40 GiB.
* Sistema de archivos raíz ext4.

El disco principal se identificó como:

```text
/dev/sda
```

Modelo:

```text
VBOX HARDDISK
```

La partición raíz corresponde a `/dev/sda2`.

---

# 4. Red

La interfaz principal identificada fue:

```text
enp0s3
```

Dirección IPv4:

```text
10.0.2.15/24
```

La máquina utiliza red NAT de VirtualBox.

Se comprobó posteriormente la conectividad con el gateway:

```bash
ping -c 4 10.0.2.2
```

y con una dirección IP externa:

```bash
ping -c 4 1.1.1.1
```

Las pruebas posteriores al hardening obtuvieron:

```text
4 paquetes enviados
4 paquetes recibidos
0% packet loss
```

---

# 5. DNS

Se revisó la configuración del resolvedor mediante:

```bash
resolvectl status
```

Se identificó `systemd-resolved` como mecanismo de resolución.

Servidor DNS utilizado:

```text
192.168.1.1
```

También aparece una dirección DNS IPv6 proporcionada por la infraestructura de red.

No se realizaron cambios durante la auditoría inicial.

---

# 6. Usuarios y privilegios

Se identificó la cuenta administrativa:

```text
sysadmin
```

Características principales:

* UID 1000.
* GID 1000.
* Shell `/bin/bash`.
* Directorio `/home/sysadmin`.
* Pertenencia al grupo `sudo`.

La cuenta se utiliza para administración mediante `sudo`.

La cuenta `root` se conserva bloqueada para autenticación mediante contraseña.

---

# 7. Servicios

Se realizó una revisión de los servicios presentes y activos.

El objetivo no fue reducir arbitrariamente el número de servicios, sino determinar:

1. qué servicio estaba presente;
2. si estaba activo;
3. para qué servía;
4. si existían dependencias;
5. si era necesario para el entorno del laboratorio;
6. qué impacto tendría su eliminación.

Se prestó especial atención a componentes como:

* `multipathd`;
* `open-iscsi`;
* `open-vm-tools`;
* `cloud-init`;
* `fwupd`;
* VirtualBox Guest Additions;
* `sysstat`;
* `kdump`;
* `lvm2`;
* `thermald`;
* `udisks2`;
* `networkd-dispatcher`;
* `apport`;
* `gpu-manager`;
* `secureboot-db`;
* Ubuntu Pro;
* `blk-availability`.

La decisión definitiva sobre estos componentes se documenta en el documento de hardening.

---

# 8. Puertos y sockets

Se realizó una inspección mediante:

```bash
sudo ss -tulpen
```

Después del hardening y reinicio se confirmó que no existían servicios TCP escuchando externamente.

Los principales sockets observados pertenecían a:

* `systemd-resolved`;
* `systemd-networkd`;
* `chronyd`.

SSH no estaba instalado en esta fase.

Por tanto, no existe un servicio SSH escuchando en el puerto 22.

---

# 9. Firewall

Se comprobó el estado de UFW antes de considerar las reglas definitivas.

La configuración final validada quedó:

```text
Status: active
Logging: on (low)
Default: deny (incoming)
Default: allow (outgoing)
Default: disabled (routed)
```

La política de entrada por defecto deniega conexiones no autorizadas.

---

# 10. AppArmor

Se comprobó posteriormente el estado de AppArmor mediante:

```bash
sudo aa-status
```

Resultado final:

```text
3 processes have profiles defined.
3 processes are in enforce mode.
0 processes are in complain mode.
0 processes are in prompt mode.
0 processes are in kill mode.
```

Los procesos confinados identificados fueron:

* `chronyd`;
* `rsyslogd`.

AppArmor se considera operativo.

---

# 11. Actualizaciones

Se comprobó el sistema de actualización automática.

Se identificaron los timers:

```text
apt-daily.timer
apt-daily-upgrade.timer
```

También se comprobó `unattended-upgrades`.

El mecanismo se encontraba instalado, habilitado y operativo.

No se realizaron modificaciones adicionales durante esta comprobación.

---

# 12. Logs e incidencias

La auditoría detectó dos grupos de eventos que requerían investigación.

## 12.1 Soft lockups

El kernel registró múltiples:

```text
watchdog: BUG: soft lockup
```

Los eventos afectaron a ambos vCPU y presentaron trazas recurrentes relacionadas con:

```text
ata_sff_pio_task
```

Se investigaron:

* clocksource;
* virtualización;
* flags de CPU;
* watchdog;
* almacenamiento;
* controladores SATA/AHCI;
* topología ATA;
* módulos del kernel;
* errores de E/S;
* timeouts;
* resets.

No se encontraron errores explícitos de E/S, timeouts o resets del disco.

El disco virtual `/dev/sda` se encuentra asociado al controlador AHCI.

No se estableció una causa raíz definitiva.

La incidencia queda documentada y se evita realizar cambios especulativos.

---

## 12.2 Chrony

Los logs mostraron repetidos:

```text
Could not connect to ...:4460
```

Se verificó posteriormente el estado real de sincronización mediante:

```bash
chronyc tracking
```

Resultado:

```text
Stratum         : 3
System time     : ~0.0008 seconds
Leap status     : Normal
```

Por tanto, la sincronización horaria estaba funcionando correctamente.

Los mensajes se registran como eventos observados sin impacto funcional demostrado.

---

# 13. Criterio de actuación

Durante la auditoría se aplicó el siguiente criterio:

### No eliminar por defecto

La presencia de un paquete no implica que deba eliminarse.

### Comprobar dependencias

Antes de eliminar un componente se analizaron las consecuencias sobre los metapaquetes de Ubuntu Server.

### Simular antes de purgar

Cuando una eliminación podía afectar al sistema base, se utilizó una simulación de APT antes de ejecutar el cambio.

### Validar después

Las modificaciones relevantes fueron seguidas de comprobaciones funcionales y de un reinicio controlado.

---

# 14. Resultado de la auditoría

La auditoría permitió establecer una línea base funcional del servidor antes del cierre del hardening.

Se identificaron:

* componentes innecesarios para este entorno;
* componentes que debían conservarse por dependencias o integración;
* mecanismos de seguridad activos;
* puertos y servicios expuestos;
* mecanismos de actualización;
* incidencias del kernel;
* mensajes de chrony que requerían comprobación funcional.

La información obtenida durante esta fase sirvió como base para las decisiones documentadas en:

`02-hardening.md`

y posteriormente para la validación final del sistema.

---

## Estado

**Auditoría inicial: COMPLETADA**

La fase de auditoría no buscó conseguir un sistema "mínimo" a cualquier precio, sino establecer una línea base técnicamente justificable antes de realizar cambios.
