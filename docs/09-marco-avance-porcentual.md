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
| 1. MVP | 10% | 1.00 | 10.0 |
| 2. UX | 10% | 0.94 | 9.4 |
| 3. Arquitectura | 10% | 0.90 | 9.0 |
| 4. Backend | 20% | 1.00 | 20.0 |
| 5. Frontend | 20% | 1.00 | 20.0 |
| 6. Hardening y sesiones | 10% | 1.00 | 10.0 |
| 7. QA y beta | 10% | 0.98 | 9.8 |

**Avance total actual estimado: 98.2%**

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
- Archivado seguro de pacientes desde staff:
  - endpoint `PATCH /api/staff/patients/{publicId}/deactivate`,
  - el listado staff mantiene solo pacientes activos,
  - el codigo archivado queda bloqueado para nuevas solicitudes de vinculacion,
  - UI staff agrega accion de archivado con confirmacion antes de desactivar.
- Recuperacion de contrasena beta:
  - tabla `password_reset_tokens` versionada con Flyway,
  - endpoints publicos `POST /api/auth/password-reset/request` y `POST /api/auth/password-reset/confirm`,
  - token temporal hasheado en base de datos,
  - respuesta sin enumeracion de emails inexistentes,
  - revocacion de sesiones activas tras cambio de contrasena,
  - pantalla Angular `/auth/reset-password` conectada desde login.
- Configuracion basica de perfil:
  - endpoint autenticado `PUT /api/auth/profile`,
  - actualizacion de nombre, apellido y telefono sin permitir cambios de email/roles,
  - almacenamiento local del usuario sincronizado tras validar sesion o guardar perfil,
  - paneles de perfil conectados en dashboards tutor y staff.
- Checklist beta readiness:
  - endpoint staff `GET /api/staff/review-readiness`,
  - metricas de pacientes activos, solicitudes pendientes, vinculaciones aprobadas, eventos futuros y notificaciones in-app sin leer,
  - checklist calculado por backend y protegido para `STAFF/ADMIN`,
  - panel staff con resumen visual, detalle de checks y fecha del corte,
  - prueba de integracion que valida acceso staff, bloqueo tutor y evidencia operativa minima.
- Contacto tutor-staff MVP:
  - tabla `contact_requests` versionada con Flyway,
  - endpoints tutor para crear/listar solicitudes de contacto,
  - endpoints staff para listar abiertas y resolver con nota,
  - regla de autorizacion por vinculacion `APPROVED`,
  - notificacion in-app al tutor al resolver,
  - panel tutor y panel staff conectados.
- Checklist formal de salida beta:
  - tabla `beta_exit_checks` versionada con Flyway,
  - seed inicial con checks funcionales y visuales principales,
  - endpoints staff/admin para listar y actualizar checks,
  - panel staff con avance, notas de evidencia y marcado/reapertura,
  - prueba de integracion para acceso staff, bloqueo tutor y actualizacion.
- Integracion SMTP para recuperacion de contrasena:
  - dependencia `spring-boot-starter-mail`,
  - servicio `PasswordResetDeliveryService`,
  - envio configurable con `APP_PASSWORD_RESET_DELIVERY_ENABLED`,
  - variables SMTP externas documentadas,
  - modo dev mantiene token expuesto solo si se habilita explicitamente.
- Validacion tecnica ejecutada:
  - `hospital-familia-server`: `.\mvnw.cmd -q test`
  - `hospital-familia-app`: `npm run build`

## Motivo del ajuste por fase

- Backend llega a cierre MVP por completar administracion staff de pacientes: creacion, listado activo, archivado no destructivo y bloqueo de codigos archivados con prueba de integracion.
- Frontend llega a cierre MVP por integrar el ciclo staff completo de pacientes/codigos: crear, seleccionar para eventos/estado y archivar con confirmacion.
- Hardening y sesiones sube porque el refresh token ya no solo se revoca al logout: ahora rota de forma segura, sostiene la continuidad de sesion, suma seed de revision apagado por defecto, deja perfil DEV explicito por entorno, evita versionar logs generados y revoca sesiones activas tras recuperar contrasena.
- Arquitectura sube porque la recuperacion de contrasena ya no depende solo de token visible en dev: queda separada en un servicio de entrega SMTP configurable por entorno.
- Hardening y sesiones llega a cierre tecnico porque recuperacion, sesiones, refresh, CORS, docs por entorno, logs y secretos configurables quedan cubiertos para beta.
- UX sube porque el tutor ya cuenta con una via visible y acotada para pedir orientacion al staff sin abrir chat clinico.
- QA y beta sube por ampliar evidencia automatizada con centro de notificaciones, preferencias, lectura tutor, creacion/listado de pacientes por staff, archivado de codigos, recuperacion de contrasena, perfil editable, checklist beta readiness visible para staff, contacto tutor-staff, checklist formal de salida beta persistido e integracion SMTP testeada a nivel de contexto. No llega a 100% porque todavia falta ejecutar la validacion visual completa y probar SMTP con credenciales reales o descartar formalmente ese alcance.

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
