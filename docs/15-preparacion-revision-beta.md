# Preparacion de revision beta

## Objetivo

Reducir friccion de revision local y cerrar la dependencia exclusiva del seed para crear pacientes/codigos de vinculacion.

## Alcance de esta entrega

- Login frontend con campos visualmente estables y menor riesgo de autofill cruzado entre proyectos `localhost`.
- Microcopy de acceso ajustado para tutor y staff.
- Seed de revision con usuarios realistas:
  - `familia.rivera@hospitalfamilia.local`
  - `enfermeria.central@hospitalfamilia.local`
- Limpieza de logs generados y regla `logs/*.log` en backend.
- API staff para pacientes:
  - `GET /api/staff/patients`
  - `POST /api/staff/patients`
- Panel staff con creacion/listado de pacientes y accion para usar `publicId` en eventos o estado visible.

## Criterios de aceptacion

- Staff puede crear un paciente con nombre visible y codigo de vinculacion.
- El codigo queda normalizado en mayusculas.
- No se permiten codigos duplicados.
- Tutor no puede acceder a endpoints staff de pacientes.
- Tutor puede solicitar vinculacion usando un codigo creado por staff.
- Login no muestra labels cortados ni autocompleta credenciales de otros proyectos en revision local.

## Validacion esperada

- Backend: `.\mvnw.cmd -q test`
- Frontend: `npm run build`
- Revision manual:
  - iniciar sesion como staff,
  - crear paciente,
  - copiar codigo de vinculacion,
  - iniciar sesion/registrar tutor,
  - solicitar vinculacion con ese codigo,
  - aprobar desde staff.
