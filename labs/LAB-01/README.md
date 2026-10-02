# LAB-01 — Auditoría y Hardening de Ubuntu Server

## 1. Descripción

Primer laboratorio práctico del proyecto de infraestructura, sistemas y ciberseguridad.

El objetivo es desplegar una máquina virtual Ubuntu Server, realizar una auditoría inicial, aplicar medidas de hardening justificadas y validar posteriormente el funcionamiento del sistema.

El laboratorio establece la metodología que se utilizará como base para los siguientes laboratorios del proyecto.

**Entorno:** laboratorio virtualizado
**Hipervisor:** Oracle VirtualBox
**Sistema:** Ubuntu Server 26.04.1 LTS
**Hostname:** `SRV-LNX-01`
**Usuario administrativo:** `sysadmin`
**Red:** NAT
**Dirección IPv4:** `10.0.2.15/24`

---

## 2. Objetivos

* Inventariar el sistema operativo y el hardware virtual.
* Identificar servicios y paquetes instalados.
* Revisar la configuración de red.
* Auditar puertos y servicios expuestos.
* Configurar y verificar el firewall.
* Revisar servicios y paquetes potencialmente innecesarios.
* Comprobar mecanismos de protección del sistema.
* Revisar cuentas y privilegios.
* Comprobar actualizaciones automáticas.
* Analizar incidencias detectadas durante la ejecución.
* Validar el sistema después del hardening.
* Documentar todas las decisiones y evidencias.

---

## 3. Entorno

### Máquina virtual

| Parámetro                | Valor                     |
| ------------------------ | ------------------------- |
| Hostname                 | `SRV-LNX-01`              |
| Sistema operativo        | Ubuntu Server 26.04.1 LTS |
| Kernel                   | `7.0.0-31-generic`        |
| Hipervisor               | VirtualBox 7.2.16         |
| Virtualización detectada | Oracle VirtualBox         |
| CPU virtual              | 2 vCPU                    |
| RAM                      | ~3.3 GiB                  |
| Disco                    | 40 GiB                    |
| Sistema de archivos      | ext4                      |
| Red                      | NAT                       |
| IPv4                     | `10.0.2.15/24`            |

### Cuenta administrativa

La administración se realiza mediante el usuario:

```text
sysadmin
```

con privilegios mediante `sudo`.

La cuenta `root` permanece bloqueada para autenticación mediante contraseña.

---

## 4. Metodología

El laboratorio se ha desarrollado siguiendo cuatro principios:

1. **Auditar antes de modificar.**
2. **No eliminar componentes sin comprobar previamente su función y dependencias.**
3. **Simular las operaciones destructivas antes de ejecutarlas.**
4. **Validar el sistema después de cada bloque importante de cambios.**

Cuando un componente se ha conservado deliberadamente, la decisión queda documentada junto con su justificación.

---

# 5. Auditoría inicial

Durante la fase inicial se revisaron:

* Sistema operativo y kernel.
* Hardware virtual.
* CPU y memoria.
* Almacenamiento.
* Interfaces de red.
* Servicios activos.
* Paquetes instalados.
* Puertos de escucha.
* Firewall.
* Cuentas y privilegios.
* Mecanismos de actualización.
* AppArmor.
* Logs del kernel y del sistema.

La auditoría permitió establecer una línea base antes de aplicar cambios.

---

# 6. Hardening realizado

## 6.1 Firewall — UFW

Se verificó y configuró UFW con:

```text
Default: deny (incoming)
Default: allow (outgoing)
Logging: low
```

El firewall quedó activo tanto para IPv4 como para IPv6.

No se habilitaron puertos de entrada innecesarios.

---

## 6.2 Servicios y paquetes

Se analizaron diferentes componentes del sistema antes de decidir su conservación o eliminación.

### Eliminados

#### ModemManager

Eliminado debido a que la máquina virtual no utiliza hardware de módem.

#### `multipath-tools`

Se analizó inicialmente, pero la decisión final fue conservarlo debido a sus dependencias con el metapaquete de Ubuntu Server y para evitar eliminar componentes del sistema base sin una necesidad funcional.

#### `upower`

Eliminado porque el servidor virtual no dispone de batería ni requiere gestión de energía propia de equipos portátiles o entornos gráficos.

#### `hwctl`

Snap eliminado después de comprobar que no era necesario para el entorno del laboratorio.

---

## 6.3 Componentes conservados

Se decidió conservar determinados paquetes y servicios aunque no fueran estrictamente necesarios para el funcionamiento mínimo de la máquina.

Entre ellos:

* `multipath-tools`
* `open-iscsi`
* `open-vm-tools`
* `cloud-init`
* `snapd`
* `fwupd`
* VirtualBox Guest Additions
* `sysstat`
* `kdump-tools`
* `lvm2`
* `thermald`
* `udisks2`
* `networkd-dispatcher`
* `apport`
* `gpu-manager`
* `secureboot-db`
* Ubuntu Pro / `ubuntu-pro-client`
* `blk-availability`

La decisión se tomó considerando dependencias, integración con Ubuntu Server y el riesgo de eliminar componentes del sistema base sin una necesidad clara.

---

# 7. Protección del sistema

## AppArmor

La comprobación final confirmó perfiles activos en modo `enforce`.

Procesos confinados:

* `chronyd`
* `rsyslogd`

Resultado:

```text
3 processes are in enforce mode.
0 processes are in complain mode.
0 processes are in kill mode.
```

AppArmor queda validado como mecanismo de protección activo.

---

# 8. Gestión de cuentas

La cuenta administrativa `sysadmin` utiliza `sudo`.

La cuenta `root` permanece bloqueada:

```text
root L
```

No se habilitó acceso directo mediante contraseña para `root`.

La política de contraseña de `sysadmin` queda documentada para futuras fases de hardening y eventual adaptación a un entorno productivo.

---

# 9. Actualizaciones automáticas

Se verificó la presencia y programación del sistema de actualizaciones automáticas.

Timers detectados:

```text
apt-daily.timer
apt-daily-upgrade.timer
```

Además, `unattended-upgrades` se encuentra instalado y habilitado.

Resultado:

**Actualizaciones automáticas → VALIDADO**

---

# 10. Servicios y puertos

Tras el hardening y el reinicio controlado se revisaron los servicios activos y los sockets de escucha.

No se detectaron servicios TCP externos innecesarios.

Los principales sockets observados corresponden a:

* `systemd-resolved`
* `systemd-networkd`
* `chronyd`

SSH no se encuentra instalado en esta fase del laboratorio.

---

# 11. Validación posterior

Se realizó un reinicio controlado para comprobar que las modificaciones no impedían el arranque del sistema.

Después del reinicio:

```text
systemctl --failed
→ 0 loaded units listed

systemctl is-system-running
→ running
```

### Red

La interfaz principal volvió a estar disponible:

```text
enp0s3 UP 10.0.2.15/24
```

### Conectividad

Se verificó:

* Gateway `10.0.2.2` → respuesta correcta.
* Internet `1.1.1.1` → respuesta correcta.
* Resolución DNS → configuración operativa.

### Firewall

UFW continuó activo después del reinicio.

### Servicios

Los servicios necesarios volvieron a iniciarse correctamente.

**Resultado de la validación: APROBADO**

---

# 12. Incidencia: Soft Lockup

Durante el laboratorio se detectaron varios mensajes de:

```text
watchdog: BUG: soft lockup
```

Los eventos fueron recurrentes y afectaron a ambos vCPU.

En varias trazas apareció:

```text
Workqueue: ata_sff ata_sff_pio_task
```

También se identificaron módulos de VirtualBox:

```text
vboxsf
vboxguest
vboxvideo
```

La máquina utiliza un disco virtual `VBOX HARDDISK` sobre un controlador AHCI.

No se encontraron evidencias explícitas de:

* errores de E/S;
* timeouts de almacenamiento;
* resets del dispositivo;
* fallos del disco virtual.

Debido a que el sistema se ejecuta dentro de VirtualBox y pueden producirse pausas prolongadas de la máquina virtual cuando el equipo anfitrión entra en suspensión, no se estableció una causa raíz definitiva.

### Decisión

No se realizaron modificaciones especulativas sobre:

* kernel;
* clocksource;
* almacenamiento;
* controladores;
* configuración de VirtualBox.

La incidencia queda registrada como **conocida y pendiente de investigación futura**.

---

# 13. Incidencia: chronyd

Durante la revisión de logs aparecieron múltiples mensajes:

```text
Could not connect to ...:4460
```

Sin embargo, la comprobación mediante `chronyc tracking` confirmó que la sincronización horaria funciona correctamente.

Estado observado:

```text
Stratum: 3
System time: ~0.0008 seconds slow
Leap status: Normal
```

La máquina dispone de una fuente de tiempo válida y mantiene sincronización.

### Decisión

No se realizaron modificaciones.

Los mensajes se registran como eventos observados sin impacto funcional demostrado.

---

# 14. Resultado final

El servidor queda en un estado funcional y endurecido para continuar con los siguientes laboratorios.

### Estado

**LAB-01 — TÉCNICAMENTE FINALIZADO**

El sistema:

* arranca correctamente;
* mantiene conectividad;
* mantiene el firewall activo;
* no presenta unidades fallidas;
* mantiene AppArmor activo;
* mantiene las actualizaciones automáticas programadas;
* mantiene la cuenta `root` bloqueada;
* mantiene los servicios necesarios;
* supera la validación posterior al reinicio.

Las incidencias conocidas quedan documentadas sin aplicar modificaciones especulativas.

---

# 15. Checklist de aceptación

* [x] Sistema operativo instalado y actualizado
* [x] Inventario inicial realizado
* [x] Auditoría inicial completada
* [x] Servicios revisados
* [x] Paquetes revisados
* [x] Firewall configurado
* [x] Puertos revisados
* [x] AppArmor validado
* [x] Cuentas y privilegios revisados
* [x] `root` bloqueado
* [x] Actualizaciones automáticas verificadas
* [x] Logs revisados
* [x] Incidencias investigadas
* [x] Reinicio controlado realizado
* [x] Conectividad validada
* [x] Servicios validados después del reinicio
* [x] Estado general del sistema `running`
* [x] Decisiones documentadas
* [x] Incidencias conocidas documentadas

---

# 16. Próximos pasos

LAB-01 establece la línea base de un servidor Ubuntu Server endurecido.

Los siguientes laboratorios ampliarán progresivamente la infraestructura hacia el escenario objetivo del proyecto:

**sistemas → redes → servicios → seguridad → monitorización → automatización → infraestructura empresarial.**

LAB-01 queda cerrado como línea base para las siguientes fases.
