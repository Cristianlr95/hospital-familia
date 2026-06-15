# Checklist salida beta

## Objetivo
Convertir la validacion final beta en evidencia trazable dentro del sistema, visible para staff/admin y persistida en base de datos.

## Alcance implementado

- Tabla `beta_exit_checks` versionada con Flyway.
- Seed inicial de checks de salida beta:
  - login tutor desktop,
  - login staff desktop,
  - vista tutor mobile,
  - vista staff mobile,
  - vinculacion tutor-paciente,
  - estado y calendario,
  - notificaciones in-app,
  - contacto tutor-staff,
  - recuperacion de contrasena,
  - entorno dev saludable.
- Endpoint staff/admin `GET /api/staff/beta-exit-checklist`.
- Endpoint staff/admin `PUT /api/staff/beta-exit-checklist/{id}`.
- Panel staff para ver avance, agregar notas y marcar/reabrir checks.
- Prueba de integracion para acceso staff, bloqueo tutor y actualizacion de checks.

## Criterio de uso

Cada check debe marcarse solo despues de una revision real en navegador o entorno local equivalente.

El porcentaje interno del checklist no reemplaza la decision de salida beta: deja evidencia operativa para sostenerla.

## Validacion tecnica

- `hospital-familia-server`: `.\mvnw.cmd -q test`
- `hospital-familia-app`: `npm run build`

## Pendiente para 100%

- Ejecutar la revision visual completa y registrar checks desde la app.
- Definir o descartar formalmente proveedor externo de email/push para beta productiva.
