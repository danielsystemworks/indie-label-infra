# LAB-01 — Validación final

## 1. Objetivo

Comprobar que las medidas aplicadas durante el hardening no han comprometido:

* el arranque del sistema;
* el estado de systemd;
* la red;
* la conectividad;
* la resolución DNS;
* el firewall;
* los puertos expuestos;
* los servicios necesarios;
* los mecanismos de seguridad;
* las actualizaciones automáticas.

La validación se realizó después de aplicar el hardening y mediante un **reinicio controlado** de la máquina virtual.

---

# 2. Estado de systemd

Se comprobó la existencia de unidades fallidas:

```bash id="c2klv9"
systemctl --failed
```

Resultado:

```text
0 loaded units listed
```

### Conclusión

No existen unidades de systemd en estado `failed`.

**Resultado: PASS**

---

# 3. Estado general del sistema

Se comprobó el estado global:

```bash id="j2l7sp"
systemctl is-system-running
```

Resultado:

```text
running
```

### Conclusión

El sistema ha completado correctamente el arranque y se encuentra operativo.

**Resultado: PASS**

---

# 4. Reinicio controlado

Se realizó un reinicio explícito mediante:

```bash id="e2d7ck"
sudo reboot
```

Después del reinicio fue posible iniciar sesión nuevamente con `sysadmin`.

Se repitieron las comprobaciones principales.

### Conclusión

El sistema mantiene su configuración después del reinicio.

**Resultado: PASS**

---

# 5. Estado de las interfaces de red

Se comprobó:

```bash id="b9r1z7"
ip -br addr
```

Resultado relevante:

```text
enp0s3 UP 10.0.2.15/24
```

La interfaz principal se encuentra activa y conserva su dirección IPv4.

### Conclusión

La configuración de red continúa operativa después del hardening y del reinicio.

**Resultado: PASS**

---

# 6. Conectividad con el gateway

Se ejecutó:

```bash id="x2c8sf"
ping -c 4 10.0.2.2
```

Resultado:

```text
4 packets transmitted
4 packets received
0% packet loss
```

### Conclusión

Existe conectividad correcta con el gateway de VirtualBox.

**Resultado: PASS**

---

# 7. Conectividad externa

Se ejecutó:

```bash id="h8r0zn"
ping -c 4 1.1.1.1
```

Resultado:

```text
4 packets transmitted
4 packets received
0% packet loss
```

### Conclusión

La máquina mantiene conectividad IP externa después del hardening.

**Resultado: PASS**

---

# 8. Resolución DNS

Se comprobó:

```bash id="6a5r4x"
resolvectl status
```

La interfaz `enp0s3` dispone de ámbito DNS y utiliza:

```text
192.168.1.1
```

También aparece una dirección DNS IPv6 proporcionada por la infraestructura.

### Conclusión

`systemd-resolved` mantiene una configuración DNS operativa.

**Resultado: PASS**

---

# 9. Firewall

Se comprobó:

```bash id="xkj3qv"
sudo ufw status verbose
```

Resultado:

```text
Status: active
Logging: on (low)
Default: deny (incoming)
Default: allow (outgoing)
Default: disabled (routed)
```

### Conclusión

UFW permanece activo después del reinicio y mantiene la política restrictiva definida durante el hardening.

**Resultado: PASS**

---

# 10. Puertos y sockets

Se ejecutó:

```bash id="d7x8bc"
sudo ss -tulpen
```

Se observaron principalmente sockets asociados a:

* `systemd-resolved`;
* `systemd-networkd`;
* `chronyd`.

No se detectaron servicios TCP externos innecesarios.

No existe SSH instalado en esta fase.

### Conclusión

La superficie de escucha de red permanece limitada.

**Resultado: PASS**

---

# 11. Servicios activos

Después del reinicio se revisaron los servicios cargados y activos.

Entre los servicios observados se encuentran:

```text id="6yrt5k"
chrony.service
cron.service
dbus.service
fwupd.service
getty@tty1.service
multipathd.service
networkd-dispatcher.service
polkit.service
rsyslog.service
snapd.service
systemd-journald.service
systemd-logind.service
systemd-networkd.service
systemd-resolved.service
systemd-udevd.service
udisks2.service
unattended-upgrades.service
user@1000.service
vboxadd-service.service
```

La presencia de `multipathd` es coherente con la decisión documentada de conservar `multipath-tools`.

La presencia de `vboxadd-service` es coherente con la conservación de VirtualBox Guest Additions.

### Conclusión

Los servicios conservados deliberadamente continúan funcionando después del reinicio.

**Resultado: PASS**

---

# 12. AppArmor

Se comprobó:

```bash id="4jy2vw"
sudo aa-status
```

Resultado:

```text
3 processes have profiles defined.
3 processes are in enforce mode.
0 processes are in complain mode.
0 processes are in kill mode.
```

Procesos relevantes:

* `chronyd`;
* `rsyslogd`.

### Conclusión

AppArmor permanece activo y aplicando perfiles en modo `enforce`.

**Resultado: PASS**

---

# 13. Cuenta root

Se comprobó el estado de `root`:

```bash id="7a4qkv"
sudo passwd --status root
```

Resultado:

```text
root L 2026-08-26 0 99999 7 -1
```

La letra `L` indica que la contraseña de `root` está bloqueada.

### Conclusión

La cuenta `root` no está habilitada para autenticación mediante contraseña.

**Resultado: PASS**

---

# 14. Política de `sysadmin`

Se comprobó:

```bash id="j7p2nr"
sudo chage -l sysadmin
```

Estado observado:

* último cambio de contraseña: 15/09/2026;
* sin caducidad;
* sin inactividad;
* cuenta sin fecha de expiración;
* aviso configurado en 7 días.

Esta configuración queda documentada como característica del entorno de laboratorio.

No se modificó durante LAB-01.

**Resultado: DOCUMENTADO**

---

# 15. Actualizaciones automáticas

Se comprobó el servicio:

```bash id="z7j3q0"
sudo systemctl status unattended-upgrades --no-pager
```

`unattended-upgrades` se encuentra instalado y habilitado.

Posteriormente se comprobaron los timers:

```bash id="1v5fha"
systemctl list-timers --all | grep -iE 'apt|unattended'
```

Se identificaron:

```text
apt-daily.timer
apt-daily-upgrade.timer
```

Ambos presentan ejecuciones previas y próximas ejecuciones programadas.

### Conclusión

El mecanismo periódico de actualización automática está configurado.

**Resultado: PASS**

---

# 16. Sincronización horaria

Se detectaron mensajes de `chronyd` relacionados con conexiones rechazadas hacia determinadas fuentes.

Para determinar si existía un problema funcional se ejecutó:

```bash id="v3l1q4"
chronyc tracking
```

Resultado:

```text
Reference ID : ntp-nts-1.ps6.canonical.com
Stratum      : 3
System time  : ~0.0008 seconds slow
Last offset  : ~-0.0008 seconds
Leap status  : Normal
```

### Conclusión

La sincronización horaria funciona correctamente.

Los mensajes observados en los logs no han demostrado un fallo funcional del servicio.

**Resultado: PASS**

---

# 17. Logs del sistema

Se revisaron mensajes de prioridad alta mediante:

```bash id="0w4h6x"
sudo journalctl -p 0..3 -b --no-pager
```

Se identificaron mensajes repetidos de `chronyd` relacionados con conexiones rechazadas.

Estos mensajes fueron contrastados con `chronyc tracking`, que confirmó sincronización correcta.

No se realizaron modificaciones basadas únicamente en estos mensajes.

---

# 18. Soft lockups

La validación también tuvo en cuenta la incidencia de soft lockups detectada durante el laboratorio.

La investigación identificó trazas recurrentes asociadas a:

```text id="6o8n0w"
ata_sff_pio_task
```

No se encontraron errores explícitos de E/S, timeouts o resets del almacenamiento.

La máquina continúa operativa.

### Conclusión

La incidencia no se considera resuelta, sino **conocida y documentada**.

No se realizaron modificaciones especulativas.

**Resultado: OBSERVACIÓN ABIERTA**

---

# 19. Matriz final de validación

| Comprobación                  | Resultado           |
| ----------------------------- | ------------------- |
| Arranque                      | PASS                |
| `systemctl --failed`          | PASS                |
| `systemctl is-system-running` | PASS                |
| Interfaz de red               | PASS                |
| Gateway                       | PASS                |
| Conectividad externa          | PASS                |
| DNS                           | PASS                |
| UFW                           | PASS                |
| Puertos                       | PASS                |
| Servicios                     | PASS                |
| AppArmor                      | PASS                |
| Root bloqueado                | PASS                |
| Actualizaciones automáticas   | PASS                |
| Chrony                        | PASS                |
| Logs                          | PASS                |
| Soft lockups                  | OBSERVACIÓN ABIERTA |

---

# 20. Criterio de aceptación

LAB-01 se considera técnicamente aceptado porque:

* el sistema arranca correctamente;
* no existen unidades fallidas;
* la red funciona;
* existe conectividad;
* el firewall permanece activo;
* no se han introducido puertos innecesarios;
* AppArmor permanece operativo;
* `root` permanece bloqueado;
* las actualizaciones automáticas están programadas;
* los servicios necesarios continúan funcionando;
* las incidencias conocidas están documentadas;
* no existen cambios pendientes necesarios para cerrar el laboratorio.

---

# 21. Estado final

**LAB-01 — VALIDACIÓN FINAL: APROBADA**

La máquina virtual `SRV-LNX-01` queda preparada como línea base endurecida para continuar con el siguiente laboratorio.

Las incidencias no críticas permanecen registradas para observación futura y no impiden el cierre del laboratorio.
