# LAB-02 — Validación final

## 1. Objetivo

Registrar las pruebas finales que demuestren que el servidor continúa operativo y que las medidas de seguridad funcionan después de los cambios.

## 2. Resultados disponibles

| Prueba | Resultado |
|---|---|
| Conexión SSH desde Windows | Correcta |
| UFW activo | Confirmado |
| Regla SSH restringida a Host-Only | Confirmada |
| Conectividad IP a `1.1.1.1` | Correcta en la prueba realizada |
| Resolución DNS de `ubuntu.com` | Correcta |
| Reinicio pendiente | No, según la comprobación realizada |

Estos resultados son parciales y no equivalen a la aceptación final del laboratorio.

## 3. Pruebas pendientes

- Ejecutar `systemctl --failed`.
- Comprobar `systemctl is-system-running`.
- Revisar servicios activos y sockets de escucha.
- Comprobar interfaces, rutas y conectividad.
- Verificar UFW y las reglas efectivas.
- Validar la configuración de SSH y el acceso administrativo.
- Revisar usuarios, grupos y permisos.
- Examinar registros del sistema.
- Ejecutar y validar el script Bash.
- Repetir las comprobaciones necesarias después de los cambios.
- Revisar las evidencias y la documentación.

## 4. Evidencias

Guardar las pruebas finales en `evidencias/03-validacion/`.

Cada evidencia debe indicar qué comando se ejecutó, qué se observó y qué criterio se considera superado.

## 5. Criterio de aceptación

El laboratorio solo podrá cerrarse cuando las comprobaciones requeridas estén ejecutadas, los resultados se documenten y las incidencias pendientes estén resueltas o justificadas expresamente.

## 6. Estado

**Pendiente.** No se declara aprobada la validación final.
