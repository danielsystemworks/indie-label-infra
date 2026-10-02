# LAB-01 — Hardening y decisiones técnicas

## 1. Objetivo

Aplicar medidas de hardening sobre `SRV-LNX-01` a partir de los resultados obtenidos durante la auditoría inicial.

El objetivo no es reducir arbitrariamente el número de paquetes o servicios, sino disminuir la superficie de ataque manteniendo la estabilidad y funcionalidad del servidor.

Se aplica el principio:

> **Cambiar únicamente aquello cuya necesidad, impacto y consecuencia hayan sido previamente evaluados.**

---

# 2. H-01 — Actualización y mantenimiento inicial

Antes del hardening se actualizó el sistema y se revisó el estado de los paquetes.

Se verificó que no quedasen actualizaciones pendientes después del proceso.

También se realizó una limpieza controlada de componentes que habían quedado obsoletos o que no eran necesarios para el entorno.

### Resultado

**H-01 — COMPLETADO**

---

# 3. H-02 — Firewall UFW

Se configuró el firewall mediante UFW.

Configuración final:

```text
Status: active
Logging: on (low)
Default: deny (incoming)
Default: allow (outgoing)
Default: disabled (routed)
```

La política aplicada establece:

* denegación de conexiones entrantes por defecto;
* autorización de conexiones salientes;
* ausencia de reglas de entrada innecesarias;
* logging de firewall habilitado en nivel bajo.

No se habilitaron puertos de entrada que no fueran necesarios.

### Validación

Después del reinicio controlado:

```bash
sudo ufw status verbose
```

confirmó que UFW continuaba activo con la política esperada.

### Resultado

**H-02 — COMPLETADO Y VALIDADO**

---

# 4. H-03 — Revisión de servicios y paquetes

Esta fue la parte más extensa del hardening.

La metodología fue:

1. Identificar el paquete o servicio.
2. Determinar su función.
3. Comprobar si estaba activo.
4. Revisar dependencias.
5. Simular la eliminación cuando procedía.
6. Evaluar el impacto.
7. Eliminar solamente cuando existía una justificación clara.
8. Validar posteriormente el sistema.

---

# 5. Componentes eliminados

## 5.1 ModemManager

Se eliminó `ModemManager`.

### Motivo

La máquina virtual no dispone de hardware de módem ni necesita gestionar conexiones móviles.

La eliminación no aporta una funcionalidad necesaria al laboratorio.

### Decisión

**ELIMINADO**

---

## 5.2 upower

Se eliminó `upower`.

### Motivo

La máquina es un servidor virtual y no dispone de batería ni necesita gestión de energía orientada a equipos portátiles o entornos de escritorio.

### Decisión

**ELIMINADO**

---

## 5.3 hwctl

Se eliminó el snap relacionado con `hwctl`.

Se conservó el estado previo mediante snapshot antes de proceder.

### Motivo

El componente no era necesario para el escenario actual del laboratorio.

### Decisión

**ELIMINADO**

---

# 6. Componentes analizados y conservados

Una parte fundamental del laboratorio consistió en **no eliminar componentes simplemente porque parecieran innecesarios**.

---

## 6.1 multipath-tools

Se comprobó:

```bash
multipath -ll
```

No se detectaron dispositivos multipath.

Sin embargo, la simulación de purga mostró que su eliminación afectaría a metapaquetes de Ubuntu Server.

### Decisión

**CONSERVAR**

Motivo:

* ausencia de necesidad inmediata;
* pero fuerte integración con el sistema base;
* riesgo innecesario de eliminar componentes del metapaquete `ubuntu-server`.

El servicio `multipathd` continúa activo después del reinicio como consecuencia de esta decisión.

---

## 6.2 open-iscsi

No se detectaron sesiones iSCSI activas.

Sin embargo, la simulación de eliminación mostró impacto sobre componentes del sistema base.

### Decisión

**CONSERVAR**

No se justifica eliminarlo únicamente por no utilizar iSCSI en este laboratorio.

---

## 6.3 open-vm-tools

Se analizó el componente de integración con máquinas virtuales.

El entorno utiliza VirtualBox.

### Decisión

**CONSERVAR**

No se realiza una eliminación indiscriminada de herramientas relacionadas con la virtualización.

---

## 6.4 Cloud-init

Se comprobó:

```bash
cloud-init status --long
```

El sistema está marcado explícitamente como deshabilitado mediante:

```text
/etc/cloud/cloud-init.disabled
```

El estado corresponde a:

```text
disabled-by-marker-file
```

### Decisión

**CONSERVAR / DESHABILITADO**

No se elimina el paquete.

El mecanismo permanece disponible para futuros escenarios en los que pueda ser necesario.

---

## 6.5 snapd

Se analizó el sistema Snap.

### Decisión

**CONSERVAR**

No se considera necesario eliminarlo como medida de hardening por sí misma.

---

## 6.6 fwupd

`fwupd` permanece instalado y activo.

### Motivo

Forma parte del ecosistema de mantenimiento de firmware de Ubuntu y no representa una superficie que justifique su eliminación en este laboratorio.

Además, otros componentes dependen de él.

### Decisión

**CONSERVAR**

---

## 6.7 VirtualBox Guest Additions

El servicio:

```text
vboxadd-service.service
```

permanece activo después del reinicio.

### Motivo

Forma parte de la integración entre el sistema invitado y VirtualBox.

### Decisión

**CONSERVAR**

---

## 6.8 sysstat

Se comprobó el funcionamiento de las tareas asociadas a recopilación y rotación de métricas.

### Decisión

**CONSERVAR**

La monitorización será útil en laboratorios posteriores.

---

## 6.9 kdump-tools

Se comprobó que:

```text
USE_KDUMP=0
```

Por tanto, el mecanismo no está preparado para realizar dumps de kernel.

### Decisión

**CONSERVAR**

La eliminación podría afectar al metapaquete del servidor y no aporta un beneficio suficiente en este punto.

---

## 6.10 lvm2

Se comprobó el estado de LVM:

```bash
pvs
vgs
lvs
```

No se detectaron volúmenes LVM activos.

Aun así, el paquete forma parte de la integración del sistema Ubuntu Server.

### Decisión

**CONSERVAR**

No se elimina únicamente porque actualmente no exista un volumen LVM.

---

## 6.11 thermald

El servicio no se activa debido a las condiciones de virtualización.

### Decisión

**CONSERVAR / SIN ACCIÓN**

No existe necesidad de forzar su activación ni de eliminar el paquete.

---

## 6.12 udisks2

Se mantiene activo.

### Motivo

Existe integración con otros componentes del sistema, incluido `fwupd`.

### Decisión

**CONSERVAR**

---

## 6.13 networkd-dispatcher

Permanece activo.

### Decisión

**CONSERVAR**

Puede resultar útil para automatizaciones asociadas a cambios de red y no supone un beneficio claro eliminarlo en esta fase.

---

## 6.14 apport

`apport` permanece instalado.

Se comprobó que su eliminación tendría impacto sobre metapaquetes del servidor.

### Decisión

**CONSERVAR**

---

## 6.15 gpu-manager

Se observó como servicio oneshot inactivo.

El binario pertenece a `ubuntu-drivers-common`.

La simulación de purga mostró impacto sobre componentes de Ubuntu Server.

### Decisión

**CONSERVAR**

---

## 6.16 secureboot-db

El componente permanece instalado.

El servicio no se ejecuta cuando las condiciones necesarias no se cumplen.

### Decisión

**CONSERVAR**

Además, forma parte de las recomendaciones de otros componentes de Ubuntu.

---

## 6.17 Ubuntu Pro / ubuntu-pro-client

Se revisaron los componentes `ua-*` y `ubuntu-pro-client`.

No existe actualmente un token de máquina asociado.

Los timers relacionados quedan omitidos cuando no existe dicho registro.

### Decisión

**CONSERVAR**

La eliminación tendría un impacto innecesario sobre componentes del sistema base.

---

## 6.18 blk-availability

El servicio aparece como:

```text
active (exited)
```

con una acción equivalente a:

```text
/usr/bin/true
```

El componente está relacionado con `lvm2`.

### Decisión

**CONSERVAR**

No representa una carga funcional significativa y su eliminación afecta a paquetes del sistema base.

---

# 7. H-04 — Cuentas y privilegios

Se revisó la política de la cuenta administrativa `sysadmin`.

Estado de contraseña:

* último cambio: 15/09/2026;
* sin caducidad configurada;
* cuenta sin fecha de expiración.

Para este laboratorio se documenta la configuración sin imponer una política de caducidad propia de un entorno corporativo.

### Root

La cuenta `root` permanece bloqueada:

```text
root L
```

La administración se realiza mediante `sudo`.

### Resultado

**H-04 — VALIDADO**

---

# 8. AppArmor

Se comprobó el estado de AppArmor.

Resultado:

```text
3 processes are in enforce mode.
0 processes are in complain mode.
0 processes are in kill mode.
```

Los procesos identificados incluyen:

* `chronyd`;
* `rsyslogd`.

### Decisión

No se realizan modificaciones.

### Resultado

**APPARMOR — VALIDADO**

---

# 9. Actualizaciones automáticas

Se verificaron:

```text
apt-daily.timer
apt-daily-upgrade.timer
```

Además, `unattended-upgrades` permanece instalado y habilitado.

### Decisión

No se realizan modificaciones adicionales.

### Resultado

**ACTUALIZACIONES AUTOMÁTICAS — VALIDADO**

---

# 10. Soft Lockups

Durante el laboratorio se detectaron soft lockups recurrentes.

La investigación incluyó:

* watchdog;
* clocksource;
* CPU virtual;
* VirtualBox;
* controladores ATA;
* AHCI;
* almacenamiento;
* errores de E/S;
* timeouts;
* resets.

Se observó repetidamente:

```text
Workqueue: ata_sff ata_sff_pio_task
```

No se encontraron errores explícitos de E/S ni fallos del dispositivo virtual.

### Decisión

No modificar:

* kernel;
* clocksource;
* parámetros ATA;
* controladores;
* configuración de VirtualBox.

### Estado

**INCIDENCIA CONOCIDA — SIN CAUSA RAÍZ CONFIRMADA**

Queda pendiente de observación en futuros laboratorios.

---

# 11. Chrony

Se observaron mensajes de conexión rechazada hacia determinadas fuentes:

```text
Could not connect to ...:4460
```

Se verificó posteriormente el estado funcional de `chronyd`.

Resultado:

```text
Stratum: 3
Leap status: Normal
System time: ~0.0008 seconds
```

### Decisión

No modificar la configuración.

### Estado

**SIN IMPACTO FUNCIONAL DEMOSTRADO**

---

# 12. Principios de hardening aplicados

El laboratorio establece las siguientes reglas para futuras intervenciones:

### 12.1 No eliminar por apariencia

Un paquete aparentemente innecesario no se elimina automáticamente.

### 12.2 No confundir "inactivo" con "innecesario"

Un servicio puede estar inactivo debido a una condición de ejecución y seguir siendo parte legítima del sistema.

### 12.3 Comprobar dependencias

Los metapaquetes de Ubuntu Server se consideran parte del análisis.

### 12.4 Simular antes de purgar

Toda eliminación potencialmente relevante debe analizarse previamente.

### 12.5 Validar después de modificar

Los cambios importantes deben comprobarse mediante:

* estado de systemd;
* red;
* firewall;
* puertos;
* servicios;
* reinicio controlado.

### 12.6 No corregir síntomas sin causa demostrada

Los soft lockups y mensajes de chrony se investigaron antes de realizar modificaciones.

---

# 13. Resultado del hardening

El sistema queda con:

* firewall activo;
* política de entrada restrictiva;
* AppArmor operativo;
* `root` bloqueado;
* actualizaciones automáticas programadas;
* servicios y paquetes revisados;
* componentes innecesarios eliminados cuando existía justificación;
* componentes sensibles conservados cuando su eliminación podía comprometer la integración del servidor;
* incidencias conocidas documentadas.

**HARDENING H-01/H-02/H-03/H-04 — COMPLETADO**
