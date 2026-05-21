# Centro de notificaciones in-app

## Objetivo

Agregar una bandeja de avisos familiares dentro de la app para que el tutor pueda revisar cambios relevantes sin depender del feed de actividad ni de proveedores externos de push.

## Alcance implementado

- Persistencia backend en tabla `notifications`.
- Endpoint tutor `GET /api/notifications` para listar los 30 avisos recientes.
- Endpoint tutor `PUT /api/notifications/{id}/read` para marcar lectura.
- Emision automatica desde:
  - aprobacion, rechazo y revocacion de vinculaciones,
  - actualizacion de estado visible del paciente,
  - creacion, actualizacion, cambio de estado y cancelacion de eventos.
- Respeto de preferencias por tipo:
  - cambios de estado,
  - eventos,
  - vinculaciones.
- Respeto de horario silencioso configurado en preferencias.
- Bandeja frontend en dashboard tutor con estado `Nueva/Leida` y accion de marcado.

## Contrato backend

```http
GET /api/notifications
Authorization: Bearer <token_tutor>
```

```http
PUT /api/notifications/{id}/read
Authorization: Bearer <token_tutor>
```

Ambos endpoints estan restringidos a rol `TUTOR`. El backend valida que la notificacion pertenezca al usuario autenticado antes de permitir el marcado como leida.

## Tipos de notificacion

- `STATE_CHANGE`: cambios de estado visible del paciente.
- `NEW_EVENT`: nuevo evento familiar programado.
- `EVENT_UPDATED`: cambios o cancelaciones de eventos.
- `LINKING_APPROVED`: vinculacion aprobada.
- `LINKING_REJECTED`: vinculacion rechazada.
- `LINKING_REVOKED`: acceso revocado.

## Validacion ejecutada

- `hospital-familia-server`: `.\mvnw.cmd -q test`
- `hospital-familia-app`: `npm run build`

## Pendiente recomendado

- Definir si el horario silencioso debe suprimir avisos in-app o solo entregas push/email futuras.
- Evaluar `notification_outbox` si se integra proveedor externo de push, email o SMS.
- Agregar validacion visual end-to-end en navegador antes de salida beta.
