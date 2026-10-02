# CHANGELOG — LAB-01

Historial de cambios realizados durante la auditoría y hardening de `SRV-LNX-01`.

---

## [2026-09] — LAB-01 — Auditoría y hardening inicial

### Sistema

* Sistema auditado: Ubuntu Server 26.04.1 LTS
* Hostname: `SRV-LNX-01`
* Kernel: `7.0.0-31-generic`
* Hipervisor: VirtualBox 7.2.16
* Virtualización detectada: Oracle VirtualBox
* Red: NAT
* IPv4: `10.0.2.15/24`

---

## Cambios ejecutados

### 1. Actualización del sistema

Se realizó la actualización inicial de paquetes disponibles.

Resultado:

* Sistema actualizado.
* Posteriormente no quedaron actualizaciones pendientes.
* No fue necesario realizar un reinicio adicional por actualización del kernel.

---

### 2. Firewall UFW

Se habilitó y configuró `UFW`.

Configuración final:

```text
Status: active
Logging: on (low)
Default: deny (incoming)
Default: allow (outgoing)
Routed: disabled
IPv6: enabled
```

No se añadieron reglas de entrada innecesarias.

Como consecuencia, el servidor mantiene una política de mínimo privilegio para conexiones entrantes.

---

### 3. Eliminación de ModemManager

Se comprobó que `ModemManager` no era necesario para la función prevista del servidor virtual.

Se procedió a su eliminación.

Durante la operación también se eliminaron dependencias asociadas que APT consideró innecesarias.

Resultado:

* `ModemManager` eliminado.
* No se identificó impacto sobre la red utilizada por la VM.

---

### 4. Eliminación de `upower`

Se determinó que `upower` no era necesario en este servidor Ubuntu virtualizado.

Se ejecutó:

```bash
sudo apt purge upower
```

Resultado:

* `upower` eliminado.
* No existe hardware de batería que requiera gestión mediante `upower`.

Los paquetes huérfanos potencialmente eliminables se dejaron pendientes de revisión independiente para evitar una limpieza indiscriminada.

---

### 5. Eliminación de `hwctl`

Se identificó el snap `hwctl` durante la auditoría.

Antes de eliminarlo se conservó una instantánea de su estado/configuración para poder documentar la modificación.

Se procedió posteriormente a su eliminación.

Resultado:

* `hwctl` eliminado.
* No quedaron unidades o sockets relacionados activos.

---

## Componentes auditados y CONSERVADOS

Durante el hardening se analizaron diversos componentes que inicialmente podían parecer prescindibles.

No se eliminaron cuando la evidencia indicaba que formaban parte del entorno Ubuntu Server o que su eliminación podía afectar a metapaquetes o funcionalidades del sistema.

---

### 6. `multipath-tools`

Se comprobó:

```bash
multipath -ll
```

Resultado:

* Sin dispositivos multipath detectados.

También se comprobó que una eliminación simulada afectaba a metapaquetes de Ubuntu Server.

Decisión:

**CONSERVAR**

Motivo:

* No existe actualmente un dispositivo multipath.
* La eliminación produciría un impacto innecesario sobre el conjunto de paquetes del servidor.

`multipathd.service` continúa presente/activo como consecuencia de esta decisión.

---

### 7. `open-iscsi`

Se comprobó la existencia de sesiones iSCSI.

Resultado:

* No había sesiones activas.

La simulación de eliminación también mostraba impacto sobre componentes del servidor.

Decisión:

**CONSERVAR**

No se modificó la configuración de iSCSI.

---

### 8. `open-vm-tools`

Se comprobó su presencia dentro del conjunto de paquetes del sistema.

Decisión:

**CONSERVAR**

Motivo:

* Se trata de una dependencia del conjunto Ubuntu Server instalado.
* No se realizó una eliminación únicamente para reducir el número de paquetes.

---

### 9. Cloud-init

Cloud-init se encontraba deshabilitado mediante el mecanismo:

```text
/etc/cloud/cloud-init.disabled
```

Se comprobó:

```bash
cloud-init status --long
```

El estado correspondía a:

```text
disabled-by-marker-file
```

Decisión:

**CONSERVAR / DESHABILITADO**

No se eliminó el paquete.

Motivo:

* El sistema ya estaba configurado para no ejecutar cloud-init.
* No existía una necesidad de eliminar físicamente el componente.

---

### 10. `fwupd`

Se auditó el servicio y sus dependencias.

Decisión:

**CONSERVAR**

Motivo:

* Forma parte de la integración de Ubuntu.
* No se justificó su eliminación en el entorno del laboratorio.

---

### 11. VirtualBox Guest Additions

Se conservaron los componentes de integración de VirtualBox.

El servicio:

```text
vboxadd-service.service
```

quedó operativo después del reinicio.

Decisión:

**CONSERVAR**

Motivo:

* La máquina es una VM de laboratorio.
* Las Guest Additions forman parte de su integración con VirtualBox.

---

### 12. `sysstat`

Se auditó el conjunto de herramientas de estadísticas del sistema.

Decisión:

**CONSERVAR**

Motivo:

* Proporciona información útil para futuras tareas de monitorización y diagnóstico.

---

### 13. `kdump-tools`

Se comprobó su configuración.

Aunque el servicio aparece habilitado, la configuración indica:

```text
USE_KDUMP=0
```

Por tanto, no se encuentra preparado para capturar un crash dump.

Decisión:

**CONSERVAR**

Motivo:

* Forma parte del conjunto Ubuntu Server.
* La eliminación no aportaba una ventaja suficiente frente al impacto potencial sobre metapaquetes.

---

### 14. LVM / `lvm2`

Se comprobó la existencia de volúmenes LVM:

```bash
pvs
vgs
lvs
```

No se detectaron volúmenes LVM activos.

Decisión:

**CONSERVAR**

Motivo:

* `lvm2` forma parte del entorno Ubuntu Server.
* Su eliminación podía afectar a metapaquetes del sistema.

---

### 15. `thermald`

Se comprobó su comportamiento dentro de la VM.

El servicio no se ejecuta debido a una condición relacionada con la virtualización.

Decisión:

**SIN CAMBIOS**

No se modificó su configuración.

---

### 16. `udisks2`

Se auditó el servicio y sus relaciones con otros componentes.

Decisión:

**CONSERVAR**

Motivo:

* Se encuentra integrado con otros componentes del sistema, incluido `fwupd`.
* No se justificó su eliminación.

---

### 17. `networkd-dispatcher`

Se comprobó su estado y relación con la configuración de red.

Decisión:

**CONSERVAR**

Motivo:

* Puede resultar útil para gestionar eventos de red.
* No presenta un consumo o riesgo que justifique su eliminación en este laboratorio.

---

### 18. `apport`

Se comprobó su integración con Ubuntu Server.

Decisión:

**CONSERVAR**

Motivo:

* Está integrado con los metapaquetes del servidor.
* Su eliminación produciría cambios de mayor alcance de los necesarios.

---

### 19. `gpu-manager`

Se auditó su estado.

El servicio permanece inactivo como proceso one-shot/condicionado.

Decisión:

**CONSERVAR**

No se realizó ninguna modificación.

---

### 20. `secureboot-db`

Se comprobó su estado.

El servicio no se ejecuta debido a las condiciones del entorno virtualizado.

Decisión:

**CONSERVAR**

Motivo:

* Está relacionado con la infraestructura de seguridad de Ubuntu.
* Su eliminación no estaba justificada.

---

### 21. Ubuntu Pro / `ua-*`

Se comprobó la presencia de componentes asociados a Ubuntu Pro/Ubuntu Advantage.

La ejecución de determinados temporizadores no requiere cambios en este laboratorio y no existe una necesidad funcional de eliminarlos.

Además, la simulación de purga mostraba impacto sobre paquetes importantes del sistema.

Decisión:

**CONSERVAR**

---

### 22. `blk-availability`

Se auditó el servicio.

Resultado:

* Servicio `active (exited)`.
* Su ejecución corresponde a una acción de inicialización y no a un proceso permanente.

Decisión:

**CONSERVAR**

---

# Validaciones posteriores

Tras completar el hardening se realizó un reinicio controlado:

```bash
sudo reboot
```

Después del reinicio se verificó:

```bash
systemctl --failed
```

Resultado:

```text
0 loaded units listed
```

Estado general:

```bash
systemctl is-system-running
```

Resultado:

```text
running
```

---

## Red

La interfaz principal continuó operativa:

```text
enp0s3 UP 10.0.2.15/24
```

Gateway:

```text
10.0.2.2
```

Resultado:

* conectividad correcta.

Internet:

```text
1.1.1.1
```

Resultado:

* conectividad correcta.

DNS:

* `systemd-resolved` operativo.
* Servidor DNS detectado: `192.168.1.1`.

---

## Firewall

Después del reinicio:

```text
Status: active
Default: deny (incoming)
Default: allow (outgoing)
```

Resultado:

**VALIDADO**

---

## Puertos

Se revisaron los sockets de escucha mediante:

```bash
sudo ss -tulpen
```

No se detectaron servicios TCP escuchando externamente.

Los puertos observados correspondían principalmente a:

* `systemd-resolved`
* DHCP de `systemd-networkd`
* `chronyd`

SSH no estaba instalado en el estado auditado.

---

## Servicios

Se comprobó que los servicios necesarios continuaban funcionando después del reinicio.

No se detectaron unidades fallidas.

---

# Incidencias documentadas

Durante el laboratorio se detectaron dos situaciones que quedaron registradas como incidencias:

### INC-01 — Soft lockups

Se detectaron soft lockups recurrentes del kernel.

Se realizó una investigación específica sin modificar de forma especulativa el kernel, almacenamiento ni VirtualBox.

Estado:

**INCIDENCIA CONOCIDA — SIN CAUSA RAÍZ DEFINITIVA**

---

### INC-02 — Mensajes de Chrony

Se observaron conexiones rechazadas hacia determinados endpoints NTP.

La comprobación mediante `chronyc tracking` confirmó sincronización correcta.

Estado:

**CERRADA SIN CAMBIOS**

---

# Estado final del LAB-01

El sistema quedó en estado operativo después del hardening y del reinicio controlado.

Se verificó:

* sistema sin unidades fallidas;
* estado general `running`;
* red operativa;
* DNS operativo;
* conectividad con gateway;
* conectividad externa;
* UFW activo;
* política de entrada `deny`;
* ausencia de servicios TCP externos innecesarios;
* servicios conservados funcionando;
* AppArmor en modo enforce para los perfiles activos;
* root bloqueado;
* actualizaciones automáticas programadas;
* incidencias relevantes documentadas.

---

## Principio aplicado durante el laboratorio

El criterio utilizado para las modificaciones fue:

> **No eliminar un componente únicamente porque parezca innecesario. Primero se comprueba su función, dependencias, impacto de eliminación y comportamiento real.**

Cuando la eliminación no aportaba un beneficio claro o podía afectar a componentes esenciales del servidor, se optó por conservarlo y documentar la decisión.

Este criterio será reutilizado como metodología en los siguientes laboratorios.
