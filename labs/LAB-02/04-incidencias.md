# LAB-02 — Registro de incidencias

## INC-01 — Mensajes de soft lockup en la máquina virtual

**Contexto:** durante sesiones anteriores se observaron mensajes del watchdog del kernel asociados a pausas de la máquina virtual, especialmente en el contexto de la suspensión del equipo anfitrión.

**Impacto confirmado:** no se ha establecido una causa raíz definitiva en el contexto documentado.

**Decisión:** no realizar cambios especulativos en el kernel, el almacenamiento ni la configuración de VirtualBox sin nuevas pruebas que los justifiquen.

**Estado:** incidencia conocida; pendiente de investigación si vuelve a aparecer fuera del contexto de suspensión.

## INC-02 — Estado de autenticación DNS

**Observación:** la consulta de `ubuntu.com` se resolvió correctamente, pero la salida indicó `Data is authenticated: no`.

**Interpretación:** la resolución DNS funciona. Esta salida no acredita autenticación DNSSEC ni cifrado del transporte.

**Acción:** registrar el resultado sin tratarlo como un fallo de conectividad. Investigar la validación DNSSEC o el cifrado solo si forman parte de los requisitos del laboratorio.

**Estado:** observación documentada; sin fallo funcional de resolución demostrado.

## INC-03 — Verificaciones administrativas pendientes

**Observación:** aún falta completar la revisión de unidades systemd, cuentas y permisos, registros y automatización.

**Impacto:** no es posible declarar cerrado el laboratorio hasta obtener esos resultados.

**Acción:** completar las pruebas y actualizar `CHECKLIST-CIERRE.md`.

**Estado:** pendiente de trabajo.

## Criterio de registro

Cada incidencia nueva deberá incluir fecha, contexto, evidencias, impacto observado, acciones realizadas y estado. No se afirmará una causa raíz sin pruebas suficientes.
