# Checklist de cierre — LAB-03-LDAP

## A. Configuración técnica

- [x] OpenLDAP instalado en Ubuntu Server 24.04.5 LTS.
- [x] Servicio `slapd` activo.
- [x] Dominio LDAP `dc=indielabel,dc=com` configurado.
- [x] Unidades organizativas `usuarios`, `grupos` y `servicios` creadas.
- [x] Usuario `mrivera` creado y consultado.
- [x] Grupo POSIX `produccion` creado con GID 2001.
- [x] Usuario `mrivera` añadido a `produccion` mediante `memberUid`.
- [x] Consulta global del directorio completada.
- [x] Autenticación LDAP del usuario comprobada durante las pruebas.

## B. Documentación y evidencias

- [x] README revisado y guardado.
- [x] Capturas originales organizadas en `evidencias/`.
- [x] Salidas de comandos legibles y coherentes con el README.
- [x] Archivos LDIF de ejemplo revisados y saneados.
- [x] Búsqueda preliminar de contraseñas, hashes, claves privadas y secretos completada; sin coincidencias en los patrones revisados.
- [x] CHANGELOG actualizado.

## C. GitHub

- [ ] Revisado `git status`.
- [ ] Revisado el contenido de los archivos nuevos y modificados.
- [ ] Confirmado que no se publican credenciales ni datos sensibles.
- [ ] Cambios preparados y commit realizado.
- [ ] Cambios enviados a GitHub.
- [ ] Repositorio remoto revisado después del envío.

## D. Cierre

- [ ] Conclusiones finales revisadas.
- [ ] Laboratorio marcado como documentado y cerrado.

**Observación:** las casillas técnicas reflejan las comprobaciones realizadas en la sesión de laboratorio. Las casillas documentales y de GitHub deben marcarse cuando se hayan completado realmente.
