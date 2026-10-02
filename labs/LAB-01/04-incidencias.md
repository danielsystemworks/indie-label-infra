# LAB-01 — Incidencias y diagnóstico

## 1. Objetivo

Registrar las incidencias relevantes detectadas durante la auditoría y el hardening de `SRV-LNX-01`, documentando:

* evidencia obtenida;
* análisis realizado;
* acciones ejecutadas;
* resultado;
* decisión final.

El objetivo es evitar modificaciones especulativas y dejar constancia de las anomalías que deberán revisarse en laboratorios posteriores.

---

# 2. Incidencia INC-01 — Soft lockups del kernel

## 2.1 Descripción

Durante la ejecución del laboratorio se detectaron múltiples mensajes:

```text
watchdog: BUG: soft lockup - CPU#X stuck for XXs!
```

Los eventos aparecieron de forma recurrente durante varios días y afectaron tanto a `CPU#0` como a `CPU#1`.

Los tiempos registrados estuvieron aproximadamente entre **22 y 52 segundos**.

---

## 2.2 Evidencia

Consulta realizada:

```bash
sudo journalctl -k -b | grep -i -E 'soft lockup|watchdog|stuck'
```

Se observaron múltiples eventos de `soft lockup`.

También apareció:

```text
NMI watchdog: Perf NMI watchdog permanently disabled
```

y mensajes relacionados con el mecanismo de reloj:

```text
clocksource: Long readout interval, skipping watchdog check
```

El watchdog del kernel se encontraba habilitado:

```bash
cat /proc/sys/kernel/watchdog
```

Resultado:

```text
1
```

Umbral configurado:

```bash
cat /proc/sys/kernel/watchdog_thresh
```

Resultado:

```text
10
```

---

## 2.3 Análisis del entorno

La máquina auditada es una máquina virtual:

```bash
systemd-detect-virt
```

Resultado:

```text
oracle
```

El hardware virtual corresponde a:

```text
innotek GmbH VirtualBox/VirtualBox
```

El almacenamiento principal es:

```text
/dev/sda
VBOX HARDDISK
```

El sistema utiliza dos vCPU.

---

## 2.4 Análisis de la traza del kernel

Una de las trazas más representativas fue:

```text
watchdog: BUG: soft lockup - CPU#0 stuck for 40s!
```

El proceso implicado era:

```text
kworker/0:0
```

y la workqueue mostraba:

```text
Workqueue: ata_sff ata_sff_pio_task
```

También aparecieron módulos de VirtualBox:

```text
vboxsf(OE)
vboxguest(OE)
vboxvideo
```

La traza incluía:

```text
_raw_spin_unlock_irq
ata_sff_pio_task
process_one_work
worker_thread
kthread
```

---

## 2.5 Investigación del almacenamiento

Se comprobó la topología de almacenamiento:

```bash
lsblk -o NAME,TYPE,SIZE,FSTYPE,MOUNTPOINTS,MODEL,TRAN
```

El disco raíz virtual es:

```text
/dev/sda
VBOX HARDDISK
SATA
```

El controlador PCI asociado al almacenamiento principal es:

```text
00:0d.0 SATA controller
Kernel driver in use: ahci
```

La asociación mediante `udev` confirmó:

```text
/dev/sda
→ ata3
→ 00:0d.0
→ AHCI
```

También se comprobó:

```bash
ls -l /sys/bus/pci/devices/0000:00:0d.0/driver
```

Resultado:

```text
driver -> .../ahci
```

Por tanto, el disco principal **no utiliza la ruta `ata_piix` asociada al dispositivo PIIX4 que aparece en la topología ATA**.

---

## 2.6 Búsqueda de errores de E/S

Se revisaron los registros buscando:

```text
I/O error
timeout
reset
failed
```

No se encontraron errores explícitos de E/S, timeouts, resets ni fallos del disco virtual asociados a los eventos investigados.

En particular, alrededor de uno de los eventos:

```text
2026-09-23 11:19:53
```

se ejecutó una revisión específica de los registros del kernel.

No aparecieron errores de almacenamiento adicionales relacionados con el soft lockup.

---

## 2.7 Clocksource

Se comprobó el reloj actualmente utilizado:

```bash
cat /sys/devices/system/clocksource/clocksource0/current_clocksource
```

Resultado:

```text
tsc
```

Clocksource disponibles:

```bash
cat /sys/devices/system/clocksource/clocksource0/available_clocksource
```

Resultado:

```text
tsc kvm-clock acpi_pm
```

No se modificó el clocksource.

La presencia de `kvm-clock` no se consideró motivo suficiente para seleccionarlo, ya que el hipervisor utilizado es VirtualBox y no KVM.

---

## 2.8 Hipótesis considerada

Una posible explicación compatible con el entorno es una **pausa prolongada de la máquina virtual provocada por la suspensión o interrupción temporal de la máquina anfitriona**.

En ese escenario:

1. Windows puede suspender temporalmente la ejecución.
2. La máquina virtual deja de recibir tiempo de CPU.
3. Al reanudarse la ejecución, el watchdog del kernel puede detectar que una CPU virtual ha permanecido demasiado tiempo sin progresar.
4. El kernel registra el evento como `soft lockup`.

Esta explicación es **plausible pero no demostrada**.

La presencia repetida de:

```text
ata_sff_pio_task
```

indica el contexto de ejecución en el momento en que el watchdog detectó el bloqueo, pero no permite establecer por sí sola que el almacenamiento sea la causa raíz.

---

## 2.9 Acciones realizadas

No se realizaron modificaciones especulativas sobre:

* kernel;
* clocksource;
* parámetros del watchdog;
* controladores SATA/AHCI;
* configuración de almacenamiento;
* configuración de VirtualBox;
* número de vCPU.

La decisión fue conservar el sistema en un estado estable y documentar la incidencia para futuras pruebas controladas.

---

## 2.10 Resultado

**Estado: CERRADA COMO INCIDENCIA CONOCIDA**

Conclusión:

> Se detectaron soft lockups recurrentes en el kernel de Ubuntu durante la ejecución de la VM en VirtualBox. Las trazas muestran repetidamente `ata_sff_pio_task`, aunque no se encontraron errores explícitos de E/S, timeouts, resets ni fallos del dispositivo de almacenamiento. El disco raíz virtual `/dev/sda` utiliza el controlador AHCI y no la ruta PIIX4 asociada al trabajo ATA SFF observado. Debido al entorno virtualizado y a la posibilidad de pausas prolongadas de la máquina anfitriona por suspensión, no se establece una causa raíz definitiva. No se realizan modificaciones especulativas del kernel, almacenamiento o configuración de VirtualBox. El comportamiento queda registrado como incidencia conocida para futuras pruebas.

---

# 3. Incidencia INC-02 — Mensajes de Chrony

## 3.1 Descripción

Durante la revisión de los mensajes del sistema aparecieron varios eventos relacionados con `chrony` y conexiones rechazadas hacia servidores NTP.

Consulta:

```bash
sudo journalctl -p 0..3 -b --no-pager
```

Se observaron mensajes como:

```text
Could not connect to ...:4460
Connection refused
```

relacionados con distintos servidores de tiempo de Ubuntu.

---

## 3.2 Verificación del servicio

Se comprobó el estado de sincronización mediante:

```bash
chronyc tracking
```

Se obtuvo:

```text
Stratum : 3
System time : 0.000839932 seconds slow of NTP time
Last offset : -0.000802771 seconds
RMS offset : 0.305132955 seconds
Leap status : Normal
```

La referencia utilizada era:

```text
ntp-nts-1.ps6.canonical.com
```

---

## 3.3 Análisis

Los mensajes de conexión rechazada no implicaron una pérdida de sincronización permanente.

La información de `chronyc tracking` confirmó que `chrony` estaba sincronizado correctamente con una fuente NTP válida.

Por tanto, no se consideró justificado modificar:

* servidores NTP;
* configuración de `chrony`;
* firewall;
* resolución DNS;
* servicio de sincronización horaria.

---

## 3.4 Resultado

**Estado: CERRADA SIN CAMBIOS**

Conclusión:

> Se observaron mensajes de conexión rechazada hacia determinados endpoints NTP, pero la comprobación posterior mediante `chronyc tracking` confirmó sincronización horaria correcta y estado `Leap status: Normal`. No se identificó un fallo funcional que justificara modificar la configuración de Chrony.

Los mensajes quedan registrados como comportamiento observado para futuras auditorías.

---

# 4. Criterio general de actuación

Durante el diagnóstico se aplicó el siguiente criterio:

```text
OBSERVAR
   ↓
RECOPILAR EVIDENCIA
   ↓
IDENTIFICAR EL COMPONENTE
   ↓
DESCARTAR CAUSAS EVIDENTES
   ↓
EVALUAR RIESGO
   ↓
¿Existe causa demostrada?
   ├── SÍ → aplicar corrección
   └── NO → no realizar cambio especulativo
   ↓
DOCUMENTAR
```

Este criterio es especialmente importante en un entorno de administración de sistemas, donde una modificación realizada únicamente para eliminar un mensaje del log puede introducir problemas nuevos.

---

# 5. Estado final de las incidencias

| ID     | Incidencia      | Investigación | Acción                        | Estado   |
| ------ | --------------- | ------------- | ----------------------------- | -------- |
| INC-01 | Soft lockups    | Completada    | Sin modificación especulativa | Conocida |
| INC-02 | Mensajes Chrony | Completada    | Sin modificación              | Cerrada  |

---

# 6. Próxima revisión

Las incidencias quedan como puntos de observación para laboratorios posteriores.

Especialmente:

* INC-01 podrá reevaluarse si los soft lockups continúan en condiciones controladas.
* Podrá compararse el comportamiento con la VM ejecutándose sin suspensión prolongada del host.
* Podrá realizarse posteriormente una prueba específica de almacenamiento/VirtualBox si la incidencia persiste.
* INC-02 se revisará únicamente si aparecen pérdidas reales de sincronización NTP.

No se modifica el estado estable conseguido al finalizar LAB-01.
