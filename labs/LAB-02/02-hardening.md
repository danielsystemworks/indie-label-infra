# LAB-02 — Hardening y acceso remoto

## 1. Objetivo

Documentar las medidas de protección aplicadas y revisar de forma controlada la configuración de acceso remoto.

## 2. Firewall UFW

Configuración comprobada:

- UFW activo.
- Política predeterminada de entrada: `deny`.
- Política predeterminada de salida: `allow`.
- Regla SSH restringida a `192.168.56.1` mediante `enp0s8`.

La regla permite la administración desde el equipo anfitrión a través de la red Host-Only. Antes de cambiarla, se debe confirmar el método alternativo de acceso para evitar perder la conexión administrativa.

## 3. SSH

La conexión SSH desde Windows se ha probado correctamente y `ssh.socket` está activo.

La prueba de conectividad no demuestra por sí sola que toda la configuración de SSH cumpla las recomendaciones de seguridad.

Pendiente de comprobar:

- Configuración efectiva del servidor SSH.
- Autenticación por contraseña y por clave.
- Restricciones de acceso de `root`.
- Usuarios autorizados y privilegios.
- Configuración de `sshd` y activación por socket.
- Persistencia de las restricciones del firewall después de reiniciar.

No se declarará completado el hardening de SSH hasta revisar estos puntos y validar que la administración sigue funcionando.

## 4. Usuarios y permisos

Pendiente:

- Inventariar cuentas locales relevantes.
- Revisar grupos administrativos.
- Verificar los privilegios de `sysadmin`.
- Examinar permisos de archivos y directorios sensibles.
- Documentar cualquier modificación y su justificación.

## 5. Criterio de seguridad

No se eliminarán cuentas, servicios ni paquetes sin comprobar previamente su función, dependencias e impacto.

## 6. Estado

**En curso.** UFW y el acceso SSH están configurados y parcialmente verificados. La revisión completa del hardening permanece pendiente.
