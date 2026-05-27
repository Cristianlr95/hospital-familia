# Marco de avance porcentual

## Objetivo
Definir un criterio estable para medir el avance real de `Hospital - Familia` sin depender de estimaciones subjetivas.

## Regla base
El porcentaje del proyecto se calcula por evidencia entregable y no por cantidad de archivos, commits o lineas de codigo.

Cada fase tiene un peso fijo. Cada fase se evalua de `0` a `100` segun evidencia real:
- documentacion cerrada,
- backend implementado,
- frontend conectado,
- validacion por pruebas,
- endurecimiento operativo.

## Pesos por fase

| Fase | Descripcion | Peso |
| --- | --- | ---: |
| 0 | Lectura, discovery y descomposicion | 10% |
| 1 | Producto, MVP y backlog | 10% |
| 2 | UX, flujos y comunicacion | 10% |
| 3 | Arquitectura tecnica | 10% |
| 4 | Implementacion backend | 20% |
| 5 | Implementacion frontend | 20% |
| 6 | Seguridad, sesiones, hardening y despliegue | 10% |
| 7 | QA integral, validacion final y salida beta | 10% |

## Formula

```text
avance_total = suma(peso_fase * nivel_fase)

donde nivel_fase se expresa entre 0.00 y 1.00
```

## Estado actual estimado

| Fase | Peso | Nivel actual | Aporte |
| --- | ---: | ---: | ---: |
| 0. Discovery | 10% | 1.00 | 10.0 |
| 1. MVP | 10% | 0.95 | 9.5 |
| 2. UX | 10% | 0.90 | 9.0 |
| 3. Arquitectura | 10% | 0.85 | 8.5 |
| 4. Backend | 20% | 0.99 | 19.8 |
| 5. Frontend | 20% | 0.99 | 19.8 |
| 6. Hardening y sesiones | 10% | 0.97 | 9.7 |
| 7. QA y beta | 10% | 0.77 | 7.7 |

**Avance total actual estimado: 94.0%**

## Evidencia del corte actual

- Feed de actividad transversal para tutor y staff implementado en backend y dashboards.
- Gestion multiple de sesiones disponible para el usuario autenticado:
  - listado de sesiones activas,
  - revocacion individual,
  - cierre de otras sesiones manteniendo la actual.
- Endurecimiento del ciclo auth:
  - refresh token rotado en cada refresh,
  - invalidacion del refresh token anterior,
  - recuperacion automatica del frontend ante `401` por expiracion de access token.
- Seed demo controlado para revision local:
  - activable solo con `APP_DEMO_SEED_ENABLED=true` o `app.demo.seed-enabled=true`,
  - usuarios tutor/staff reproducibles,
  - paciente demo con vinculacion aprobada, snapshot clinico y eventos proximos.
- Entorno DEV formalizado:
  - perfil `dev` explicito,
  - conexion validada contra `jdbc:postgresql://localhost:5432/hospital_familia_dev`,
  - healthcheck local verificado con `/actuator/health`.
- Preferencias de notificacion tutor:
  - tabla `notification_preferences` versionada con Flyway,
  - endpoints `GET/PUT /api/notifications/preferences`,
  - validacion de horario silencioso,
  - panel frontend para cambios de estado, eventos, vinculaciones y horario silencioso.
- Centro de notificaciones in-app:
  - tabla `notifications` versionada con Flyway,
  - endpoints `GET /api/notifications` y `PUT /api/notifications/{id}/read`,
  - emisiones desde aprobacion/rechazo/revocacion de vinculaciones,
  - emisiones desde cambios de estado visible y eventos familiares,
  - respeto de preferencias por tipo y horario silencioso,
  - bandeja tutor con estados `Nueva/Leida` y marcado de lectura.
- Actualizacion de estado visible por staff:
  - endpoint `PUT /api/patients/{patientPublicId}/status`,
  - formulario conectado en panel staff,
  - validaciones visibles en formulario de eventos/estado,
  - pruebas de integracion para escritura staff y lectura tutor.
- Preparacion de revision beta local:
  - login con inputs corregidos y menor riesgo de autofill cruzado entre proyectos `localhost`,
  - seed de revision con usuarios realistas tutor/staff y paciente `Maria Gonzalez Rivera`,
  - logs de runtime fuera de Git,
  - staff puede crear/listar pacientes activos y generar codigos de vinculacion desde la app,
  - tutor puede solicitar vinculacion usando un codigo creado por staff.
- Validacion tecnica ejecutada:
  - `hospital-familia-server`: `.\mvnw.cmd -q test`
  - `hospital-familia-app`: `npm run build`

## Motivo del ajuste por fase

- Backend sube por cerrar persistencia y consulta del centro de notificaciones, marcar lectura, emisiones transaccionales desde flujos criticos y pruebas de integracion.
- Frontend sube por integrar una bandeja tutor accionable para avisos familiares, mantener preferencias/actividad como vistas complementarias y mejorar el flujo staff para crear pacientes/codigos sin seed manual.
- Hardening y sesiones sube porque el refresh token ya no solo se revoca al logout: ahora rota de forma segura, sostiene la continuidad de sesion, suma seed de revision apagado por defecto, deja perfil DEV explicito por entorno y evita versionar logs generados.
- QA y beta sube por ampliar evidencia automatizada con centro de notificaciones, preferencias, lectura tutor y creacion/listado de pacientes por staff, aunque sigue pendiente la validacion visual end-to-end formal.

## Criterios de nivel por fase

### 0. Discovery
- `100%` si problema, usuarios, restricciones y supuestos quedaron documentados.

### 1. MVP
- `100%` si alcance, backlog, historias y criterios base quedaron definidos.

### 2. UX
- `100%` si flujos principales, estados, wireframes conceptuales y microcopy base quedaron cerrados.

### 3. Arquitectura
- `100%` si stack, modulos, seguridad, datos y contratos principales ya estan decididos.

### 4. Backend
- `100%` si auth, linking, estado, eventos, sesiones, endurecimiento y pruebas criticas estan completos.

### 5. Frontend
- `100%` si login, dashboards, linking, estado, eventos, historial, sesiones y feedback visual estan integrados.

### 6. Hardening y sesiones
- `100%` si logout persistido, refresh seguro, swagger por entorno, variables externas, CORS, logs y secretos estan cerrados.

### 7. QA y beta
- `100%` si existe validacion funcional, visual y tecnica con criterios de salida a beta.

## Como actualizar este porcentaje
1. Ajustar el `nivel actual` de la fase afectada.
2. Recalcular el `aporte`.
3. Actualizar `MEMORY.md` con el total y el delta de la tarea.
4. No subir el porcentaje si no existe evidencia verificable en codigo, pruebas o documentos.
