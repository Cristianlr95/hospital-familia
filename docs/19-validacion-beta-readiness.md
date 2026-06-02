# Validacion beta readiness

## Objetivo
Agregar una revision operativa visible para staff antes de salida beta, con datos reales del backend y evidencia automatizada.

## Alcance implementado

- Endpoint staff `GET /api/staff/review-readiness`.
- Proteccion por rol `STAFF` o `ADMIN`.
- Metricas de corte:
  - pacientes activos,
  - solicitudes pendientes,
  - vinculaciones aprobadas,
  - eventos futuros,
  - notificaciones in-app sin leer.
- Checklist calculado por backend con estado `OK` o `Pendiente`.
- Panel frontend en dashboard staff con resumen, detalle de checks y fecha del corte.

## Criterio de readiness

El checklist considera como evidencia minima:

- existe al menos un paciente activo,
- existe al menos una vinculacion aprobada,
- existe al menos un evento futuro visible,
- existe al menos una notificacion in-app pendiente,
- la cola de solicitudes staff es medible.

## Validacion tecnica

- `hospital-familia-server`: `.\mvnw.cmd -q test`
- `hospital-familia-app`: `npm run build`

## Impacto en avance

Esta tarea sube QA/beta porque transforma la validacion final desde una revision manual dispersa hacia una evidencia consultable desde backend y visible en frontend.

No cierra todavia el 100% de salida beta porque sigue pendiente una pasada visual end-to-end formal con el proyecto levantado y, si el alcance productivo lo exige, integracion real con proveedor externo de email/push.
