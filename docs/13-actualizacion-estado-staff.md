# Actualizacion de estado visible por staff

## Objetivo
Permitir que personal `STAFF` o `ADMIN` actualice el resumen operativo visible para la familia sin exponer historia clinica completa.

## Alcance implementado

- Endpoint protegido para staff:

```text
PUT /api/patients/{patientPublicId}/status
```

- Campos editables:
  - estado visible,
  - servicio actual,
  - ubicacion,
  - resumen breve para familia.

- Dashboard staff:
  - formulario de actualizacion de estado,
  - autocompletado del paciente al seleccionar una solicitud/historial,
  - validaciones visibles para campos obligatorios,
  - correccion de navegacion staff para evitar enviar al rol staff hacia vista tutor.

## Seguridad

- Tutor solo puede leer estados de pacientes con vinculo `APPROVED`.
- Tutor no puede actualizar estados.
- Staff/admin puede actualizar el snapshot visible, no historia clinica completa.

## Validacion

- Backend: `.\mvnw.cmd -q test`.
- Frontend: `npm run build`.
