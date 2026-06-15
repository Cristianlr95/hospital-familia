# Decision de alcance SMTP para beta

## Decision

El envio real de correos SMTP queda fuera del alcance obligatorio de la beta de `Hospital - Familia`.

La beta mantiene:

- recuperacion de contrasena funcional por token temporal,
- token expuesto solo en modo desarrollo si `APP_PASSWORD_RESET_EXPOSE_TOKEN=true`,
- servicio SMTP configurable para ambientes con proveedor real,
- healthcheck SMTP opcional mediante `MANAGEMENT_HEALTH_MAIL_ENABLED`.

## Motivo

No existen credenciales SMTP reales disponibles en el entorno local. Exigir envio externo para cerrar beta generaria una dependencia operativa fuera del control del desarrollo actual.

La integracion tecnica ya esta preparada y documentada. La activacion del envio real queda como tarea de despliegue/operacion cuando exista proveedor definido.

## Criterio de aceptacion beta

Para beta se considera suficiente:

- solicitar recuperacion sin enumerar emails inexistentes,
- generar token temporal hasheado,
- confirmar nueva contrasena con token valido,
- revocar sesiones activas tras el cambio,
- mantener envio SMTP desactivado por defecto,
- documentar variables requeridas para activar SMTP real.

## Criterio post-beta

Antes de produccion se debe validar:

- proveedor SMTP real,
- remitente autorizado,
- TLS/autenticacion,
- entrega a casillas reales,
- politicas de rebote y auditoria operativa.
