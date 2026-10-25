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
