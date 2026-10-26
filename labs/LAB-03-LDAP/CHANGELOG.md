# Changelog — LAB-03-LDAP

## 2026-10-18 — Configuración principal completada

- Instalación y comprobación del servicio OpenLDAP en Ubuntu Server 24.04.5 LTS.
- Configuración del dominio `indielabel.com`.
- Creación de las unidades organizativas `usuarios`, `grupos` y `servicios`.
- Alta y consulta del usuario `mrivera`.
- Alta del grupo POSIX `produccion` (GID 2001).
- Incorporación de `mrivera` al grupo mediante `memberUid`.
- Verificación del servicio, las entradas del directorio y la autenticación LDAP.

## 2026-10-24 — Documentación y evidencias

- Organizadas siete capturas PNG en cuatro carpetas por fase.
- Añadido el índice `evidencias/README.md`.
- Actualizados el apartado de evidencias y el estado del laboratorio en `README.md`.
- Revisados los cuatro ejemplos LDIF y corregido el nombre `modificar-grupo.ldif`.
- Realizada una búsqueda preliminar de atributos sensibles en los LDIF, sin coincidencias en los patrones comprobados.
- Pendiente: revisión final de todos los archivos publicables, validación del estado de Git y publicación en GitHub.
## 2026-10-26 — Cierre documental

- Actualizado el estado final del laboratorio en `README.md`.
- Revisado el checklist de cierre y diferenciadas las tareas completadas de las pendientes.
- Commit `1ad00d4` creado y enviado a GitHub.
- Verificada la sincronización de `main` con `origin/main` después del envío.
- Checklist actualizado tras confirmar la publicación.
