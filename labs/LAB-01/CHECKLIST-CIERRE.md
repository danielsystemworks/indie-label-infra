# LAB-01 — Checklist de aceptación y cierre

## 1. Identificación

| Campo       | Valor                                              |
| ----------- | -------------------------------------------------- |
| Laboratorio | LAB-01                                             |
| Sistema     | `SRV-LNX-01`                                       |
| SO          | Ubuntu Server 26.04.1 LTS                          |
| Kernel      | `7.0.0-31-generic`                                 |
| Hipervisor  | VirtualBox 7.2.16                                  |
| Objetivo    | Auditoría, hardening y validación de Ubuntu Server |
| Estado      | **FINALIZADO**                                     |

---

# 2. Instalación e inventario

| ID     | Comprobación                    | Estado |
| ------ | ------------------------------- | ------ |
| INV-01 | Sistema operativo identificado  | ✅      |
| INV-02 | Versión del kernel registrada   | ✅      |
| INV-03 | Virtualización identificada     | ✅      |
| INV-04 | CPU/vCPU identificadas          | ✅      |
| INV-05 | Memoria identificada            | ✅      |
| INV-06 | Almacenamiento identificado     | ✅      |
| INV-07 | Interfaces de red identificadas | ✅      |
| INV-08 | Dirección IP registrada         | ✅      |
| INV-09 | Estado inicial documentado      | ✅      |

**Resultado:** inventario inicial completado.

---

# 3. Auditoría inicial

| ID     | Comprobación               | Estado |
| ------ | -------------------------- | ------ |
| AUD-01 | Estado general del sistema | ✅      |
| AUD-02 | Unidades systemd           | ✅      |
| AUD-03 | Servicios activos          | ✅      |
| AUD-04 | Puertos y sockets          | ✅      |
| AUD-05 | Interfaces de red          | ✅      |
| AUD-06 | Rutas de red               | ✅      |
| AUD-07 | Firewall                   | ✅      |
| AUD-08 | Usuarios y cuentas         | ✅      |
| AUD-09 | Estado de root             | ✅      |
| AUD-10 | AppArmor                   | ✅      |
| AUD-11 | Paquetes relevantes        | ✅      |
| AUD-12 | Logs del sistema           | ✅      |

**Resultado:** auditoría inicial completada.

---

# 4. Actualización y mantenimiento

| ID     | Comprobación                                                    | Estado |
| ------ | --------------------------------------------------------------- | ------ |
| UPD-01 | Actualizaciones disponibles revisadas                           | ✅      |
| UPD-02 | Sistema actualizado                                             | ✅      |
| UPD-03 | Paquetes innecesarios revisados                                 | ✅      |
| UPD-04 | Eliminaciones realizadas de forma controlada                    | ✅      |
| UPD-05 | Simulación de purgas utilizada antes de eliminaciones sensibles | ✅      |

**Resultado:** actualización y mantenimiento inicial completados.

---

# 5. Hardening H-01 — Base del sistema

| ID     | Comprobación                                | Estado |
| ------ | ------------------------------------------- | ------ |
| H01-01 | Cuenta administrativa `sysadmin` verificada | ✅      |
| H01-02 | `sysadmin` pertenece a `sudo`               | ✅      |
| H01-03 | Root bloqueado                              | ✅      |
| H01-04 | AppArmor operativo                          | ✅      |
| H01-05 | Estado de contraseñas documentado           | ✅      |
| H01-06 | Políticas de expiración revisadas           | ✅      |
| H01-07 | Componentes innecesarios evaluados          | ✅      |

**Resultado:** H-01 completado.

---

# 6. Hardening H-02 — Firewall

Configuración final:

```text
UFW: active
Logging: on (low)
Default incoming: deny
Default outgoing: allow
Routed: disabled
IPv6: enabled
```

| ID     | Comprobación                           | Estado |
| ------ | -------------------------------------- | ------ |
| H02-01 | UFW habilitado                         | ✅      |
| H02-02 | Entrada por defecto denegada           | ✅      |
| H02-03 | Salida por defecto permitida           | ✅      |
| H02-04 | IPv6 protegido por UFW                 | ✅      |
| H02-05 | Logging activado                       | ✅      |
| H02-06 | Reglas innecesarias evitadas           | ✅      |
| H02-07 | Estado comprobado después del reinicio | ✅      |

**Resultado:** H-02 completado.

---

# 7. Hardening H-03 — Servicios y paquetes

| Componente                 | Decisión                  | Estado |
| -------------------------- | ------------------------- | ------ |
| ModemManager               | Eliminar                  | ✅      |
| upower                     | Eliminar                  | ✅      |
| hwctl                      | Eliminar                  | ✅      |
| multipath-tools            | Conservar                 | ✅      |
| open-iscsi                 | Conservar                 | ✅      |
| open-vm-tools              | Conservar                 | ✅      |
| cloud-init                 | Conservar / deshabilitado | ✅      |
| fwupd                      | Conservar                 | ✅      |
| VirtualBox Guest Additions | Conservar                 | ✅      |
| sysstat                    | Conservar                 | ✅      |
| kdump-tools                | Conservar                 | ✅      |
| lvm2                       | Conservar                 | ✅      |
| thermald                   | Sin cambios               | ✅      |
| udisks2                    | Conservar                 | ✅      |
| networkd-dispatcher        | Conservar                 | ✅      |
| apport                     | Conservar                 | ✅      |
| gpu-manager                | Conservar                 | ✅      |
| secureboot-db              | Conservar                 | ✅      |
| Ubuntu Pro / `ua-*`        | Conservar                 | ✅      |
| blk-availability           | Conservar                 | ✅      |

### Criterio de aceptación

No se considera una buena práctica eliminar servicios únicamente para reducir el número de procesos.

Cada componente fue evaluado teniendo en cuenta:

1. función;
2. estado;
3. dependencias;
4. necesidad dentro del laboratorio;
5. impacto potencial de la eliminación;
6. resultado de simulaciones APT cuando correspondía.

**Resultado:** H-03 completado.

---

# 8. Hardening H-04 — Actualizaciones y protección

| ID     | Comprobación                                | Estado |
| ------ | ------------------------------------------- | ------ |
| H04-01 | `apt-daily.timer` activo/programado         | ✅      |
| H04-02 | `apt-daily-upgrade.timer` activo/programado | ✅      |
| H04-03 | Unattended Upgrades instalado               | ✅      |
| H04-04 | Estado de AppArmor comprobado               | ✅      |
| H04-05 | Root bloqueado                              | ✅      |
| H04-06 | Estado de cuentas documentado               | ✅      |

**Resultado:** H-04 completado.

---

# 9. Diagnóstico de incidencias

## INC-01 — Soft lockups

| Comprobación                            | Estado |
| --------------------------------------- | ------ |
| Eventos identificados                   | ✅      |
| Frecuencia documentada                  | ✅      |
| CPU afectadas identificadas             | ✅      |
| Traza del kernel revisada               | ✅      |
| Entorno VirtualBox comprobado           | ✅      |
| Almacenamiento investigado              | ✅      |
| Controlador AHCI identificado           | ✅      |
| Errores explícitos de E/S buscados      | ✅      |
| Clocksource comprobado                  | ✅      |
| Causa raíz demostrada                   | ❌      |
| Modificaciones especulativas realizadas | ❌      |

**Estado final:** incidencia conocida, sin causa raíz definitiva.

No se considera un bloqueo del laboratorio porque no se ha demostrado un fallo funcional del sistema y no se han realizado modificaciones especulativas.

---

## INC-02 — Chrony

| Comprobación                           | Estado |
| -------------------------------------- | ------ |
| Mensajes identificados                 | ✅      |
| Servicio comprobado                    | ✅      |
| Sincronización comprobada              | ✅      |
| `chronyc tracking` ejecutado           | ✅      |
| Estado `Leap status: Normal`           | ✅      |
| Configuración modificada sin necesidad | ❌      |

**Estado final:** cerrada sin cambios.

---

# 10. Validación posterior al hardening

Se realizó un reinicio controlado:

```bash
sudo reboot
```

Después del reinicio:

### Systemd

```bash
systemctl --failed
```

Resultado:

```text
0 loaded units listed
```

```bash
systemctl is-system-running
```

Resultado:

```text
running
```

**Estado:** ✅

---

### Red

Interfaz:

```text
enp0s3 UP 10.0.2.15/24
```

Gateway:

```text
10.0.2.2
```

Resultado:

* Gateway accesible: ✅
* Internet accesible mediante `1.1.1.1`: ✅
* DNS operativo: ✅

---

### Firewall

```text
Status: active
Default: deny (incoming)
Default: allow (outgoing)
```

Resultado:

**✅**

---

### Puertos

La revisión mediante:

```bash
sudo ss -tulpen
```

confirmó que no existían servicios TCP escuchando externamente de forma innecesaria.

Resultado:

**✅**

---

### Servicios

Los servicios necesarios permanecieron operativos después del reinicio.

No se detectaron unidades fallidas.

Resultado:

**✅**

---

# 11. Criterios de aceptación finales

El LAB-01 puede considerarse aceptado cuando se cumplen simultáneamente:

* [x] Sistema instalado y correctamente identificado.
* [x] Auditoría inicial documentada.
* [x] Sistema actualizado.
* [x] Hardening H-01 completado.
* [x] UFW configurado y validado.
* [x] Hardening H-03 completado.
* [x] Actualizaciones automáticas verificadas.
* [x] AppArmor verificado.
* [x] Root bloqueado.
* [x] Servicios y paquetes relevantes auditados.
* [x] Eliminaciones realizadas de forma controlada.
* [x] Reinicio controlado realizado.
* [x] Sistema operativo después del reinicio.
* [x] No existen unidades systemd fallidas.
* [x] Red operativa.
* [x] DNS operativo.
* [x] Firewall operativo.
* [x] Puertos revisados.
* [x] Incidencias documentadas.
* [x] No quedan cambios técnicos imprescindibles pendientes.

---

# 12. Estado de cierre

## LAB-01 — CERRADO

**Resultado final: ACEPTADO**

El sistema `SRV-LNX-01` queda en un estado funcional y documentado después de la auditoría y el hardening.

Las incidencias no resueltas de forma causal quedan registradas como puntos de observación para futuras pruebas, sin impedir el cierre del laboratorio.

No se realizan cambios adicionales sobre el sistema como parte del LAB-01.

---

# 13. Documentación generada

El conjunto documental del laboratorio queda compuesto por:

```text
LAB-01/
├── README.md
├── 01-auditoria-inicial.md
├── 02-hardening.md
├── 03-validacion-final.md
├── 04-incidencias.md
├── CHANGELOG.md
└── CHECKLIST-CIERRE.md
```

Este conjunto constituye la documentación técnica de referencia del primer laboratorio.

---

# 14. Lecciones aprendidas

### 1. Auditar antes de modificar

Un servicio que parece innecesario puede estar relacionado con otros componentes del sistema.

### 2. Simular antes de purgar

La simulación de APT permitió detectar cuándo una eliminación aparentemente sencilla podía afectar a metapaquetes de Ubuntu Server.

### 3. Diferenciar estado de necesidad

Un servicio `inactive`, `active (exited)` o condicionado por una `Condition*` no implica automáticamente que deba eliminarse.

### 4. No confundir síntoma con causa

La aparición de `ata_sff_pio_task` en una traza de soft lockup no demuestra que el almacenamiento sea la causa raíz.

### 5. Validar después de modificar

El reinicio controlado permitió comprobar que el sistema continuaba operativo después del hardening.

### 6. Documentar también las decisiones de no actuar

Conservar un componente después de haberlo auditado también es una decisión técnica y debe quedar registrada.

---

# 15. Cierre técnico

El LAB-01 establece la metodología que se utilizará en los siguientes laboratorios:

```text
AUDITAR
   ↓
OBTENER EVIDENCIA
   ↓
ANALIZAR
   ↓
DECIDIR
   ↓
MODIFICAR
   ↓
VALIDAR
   ↓
DOCUMENTAR
```

Esta metodología será reutilizada y ampliada progresivamente en los siguientes laboratorios del proyecto indie-label-infra.
