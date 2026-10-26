# LAB-02 — Checklist de cierre

## Identificación

| Campo | Valor |
|---|---|
| Laboratorio | LAB-02 |
| Máquina | `SRV-LNX-01` |
| Sistema | Ubuntu Server 26.04.1 LTS |
| Kernel comprobado | `7.0.0-38-generic` |
| Hipervisor | VirtualBox 7.2.16 |
| Estado | EN CURSO |

## Auditoría y sistema

- [x] Sistema operativo y kernel identificados.
- [x] Direcciones de las interfaces NAT y Host-Only registradas.
- [ ] Estado general de systemd comprobado.
- [ ] Unidades fallidas revisadas.
- [ ] Servicios activos y sockets auditados.
- [ ] Rutas de red documentadas.
- [x] Conectividad IP comprobada.
- [x] Resolución DNS comprobada.

## Acceso remoto y seguridad

- [x] Conexión SSH desde Windows comprobada.
- [x] UFW activo.
- [x] Política de entrada predeterminada denegada.
- [x] Regla SSH restringida a la dirección Host-Only del anfitrión.
- [ ] Configuración efectiva de SSH revisada.
- [ ] Autenticación y acceso de `root` revisados.
- [ ] Usuarios y grupos auditados.
- [ ] Privilegios de `sudo` revisados.
- [ ] Permisos de archivos sensibles revisados.

## Registros y automatización

- [ ] Registros del sistema revisados.
- [ ] Incidencias documentadas con evidencias.
- [ ] Script Bash desarrollado.
- [ ] Script Bash probado.
- [ ] Resultados y errores del script documentados.

## Validación y publicación

- [ ] Validación posterior a los cambios completada.
- [ ] Evidencias revisadas y saneadas.
- [ ] Documentación revisada.
- [ ] `git diff` inspeccionado.
- [ ] Commit de LAB-02 creado.
- [ ] Cambios enviados a `origin/main`.
- [ ] Repositorio verificado después de publicar.

## Criterio de cierre

LAB-02 permanecerá **EN CURSO** hasta completar las comprobaciones requeridas, documentar las evidencias y publicar los cambios revisados.
