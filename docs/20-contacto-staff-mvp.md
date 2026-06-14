# Contacto staff MVP

## Objetivo
Cerrar el flujo MVP simplificado de contacto entre tutor y staff sin convertirlo todavia en chat en tiempo real.

## Alcance implementado

- Tabla `contact_requests` versionada con Flyway.
- Endpoint tutor `POST /api/contact-requests`.
- Endpoint tutor `GET /api/contact-requests/my`.
- Endpoint staff `GET /api/contact-requests/staff/open`.
- Endpoint staff `PUT /api/contact-requests/staff/{id}/resolve`.
- Regla de seguridad:
  - el tutor solo puede crear solicitudes para pacientes con vinculacion `APPROVED`.
  - staff/admin puede listar y resolver solicitudes abiertas.
- Notificacion in-app al tutor cuando staff resuelve la solicitud.
- Panel tutor para enviar solicitud y revisar estado.
- Panel staff para listar solicitudes abiertas y cerrarlas con nota opcional.

## Criterio MVP

Este flujo no es mensajeria bidireccional ni chat clinico. Es una solicitud trazable de orientacion administrativa/familiar, suficiente para beta y consistente con privacidad del MVP.

## Validacion tecnica

- `hospital-familia-server`: `.\mvnw.cmd -q test`
- `hospital-familia-app`: `npm run build`

## Pendiente posterior

- Definir si se requiere SLA, prioridad o derivacion por unidad hospitalaria.
- Evaluar chat controlado solo si beta demuestra necesidad real.
