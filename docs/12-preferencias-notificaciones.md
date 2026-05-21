# Preferencias de notificaciones

## Objetivo
Permitir que el tutor controle que tipos de avisos familiares desea recibir sin desactivar la seguridad ni el feed interno de actividad.

## Alcance implementado

- Backend persistente por usuario tutor.
- Frontend conectado desde dashboard tutor.
- Preferencias disponibles:
  - cambios de estado,
  - eventos y calendario,
  - actualizaciones de vinculacion,
  - horario silencioso.

## Contratos API

```text
GET /api/notifications/preferences
PUT /api/notifications/preferences
```

`PUT` recibe:

```json
{
  "stateChangesEnabled": true,
  "eventsEnabled": true,
  "linkingUpdatesEnabled": true,
  "quietHoursEnabled": true,
  "quietHoursStart": "22:00",
  "quietHoursEnd": "07:00"
}
```

## Regla de seguridad

Solo usuarios con rol `TUTOR` pueden gestionar sus preferencias. Staff y admin mantienen sus paneles operativos sin modificar preferencias familiares.

## Estado MVP

Las preferencias quedan persistidas y visibles en UI. La integracion con proveedor push real queda como paso posterior si se decide activar notificaciones push fuera del feed in-app.
