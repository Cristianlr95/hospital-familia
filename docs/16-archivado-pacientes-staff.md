# Archivado seguro de pacientes por staff

## Objetivo

Permitir que el staff retire de circulacion un paciente/codigo de vinculacion sin borrar historial clinico, eventos ni decisiones previas.

## Alcance

- El staff puede archivar pacientes activos desde el panel.
- El backend marca el paciente como inactivo.
- El listado staff muestra solo pacientes activos.
- Un codigo archivado no puede crear nuevas solicitudes de vinculacion.
- La accion frontend pide confirmacion antes de ejecutarse.

## Criterios de aceptacion

- Staff autenticado puede llamar `PATCH /api/staff/patients/{publicId}/deactivate`.
- La respuesta retorna el paciente con `active=false`.
- El paciente archivado deja de aparecer en `GET /api/staff/patients`.
- Un tutor que use el codigo archivado recibe error de codigo invalido o no disponible.
- La UI elimina el paciente archivado del listado y limpia seleccion/eventos si estaba activo en el formulario.

## Validacion

- Backend: `.\mvnw.cmd -q test`
- Frontend: `npm run build`
