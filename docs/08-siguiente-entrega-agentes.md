# Siguiente entrega con agentes

## Estado actualizado

Fecha de actualizacion: 2026-05-04.

Se avanzo desde la decision inicial de ir backend primero y luego frontend. El corte actual ya deja una base real de autenticacion para construir los siguientes flujos sin depender de mocks de seguridad.

## Realizado

### Backend Auth Foundation

Repositorio: `hospital-familia-server`.

Commit publicado en `main`:

- `5733916 feat(auth): implement base JWT authentication flow`

Implementado:

- Modulo Auth bajo paquete base `com.hospitalfamilia.server`.
- Registro de tutor en `POST /api/auth/register`.
- Login JWT en `POST /api/auth/login`.
- Refresh token en `POST /api/auth/refresh`.
- Validacion de sesion en `GET /api/auth/validate`.
- Logout stateless en `POST /api/auth/logout`.
- Respuestas estandar `ApiResponse<T>`.
- Passwords con BCrypt.
- Seguridad stateless con Spring Security.
- Interceptor/filtro JWT backend.
- Proteccion por defecto de endpoints no publicos.
- Migraciones Flyway para `app_users`, `roles` y `user_roles`.
- Seed inicial de roles `TUTOR`, `PACIENTE`, `STAFF`, `ADMIN`.
- Configuracion por variables de entorno para datasource y JWT.
- Tests de integracion del flujo Auth.

Validacion ejecutada:

- `./mvnw.cmd test`: OK, 6 tests, 0 fallos.

Notas de alcance:

- Logout es stateless por ahora: el cliente elimina tokens.
- Revocacion persistida de sesiones/dispositivos queda pendiente.
- No se implementaron pacientes, vinculacion ni datos clinicos todavia.

### Frontend Auth + Dashboard Tutor Vacio

Repositorio: `hospital-familia-app`.

Rama publicada para merge posterior:

- `feat/auth-frontend-flow`

Commit publicado:

- `de00baa feat(auth): add tutor login and empty dashboard flow`

Implementado:

- Rutas lazy `/auth/login`, `/auth/register` y `/dashboard/tutor`.
- Redireccion inicial hacia `/auth/login`.
- `AuthService` conectado al backend Auth en `http://localhost:8080/api`.
- `StorageService` para sesion local inicial.
- `AuthGuard` para proteger dashboard.
- `AuthInterceptor` para enviar `Authorization: Bearer <token>`.
- Login tutor con validaciones, loading y errores en espanol.
- Registro tutor con validaciones y confirmacion de password.
- Dashboard tutor protegido con estado vacio seguro.
- Eliminacion de datos clinicos mock del flujo principal.
- Pantalla `home` queda como legacy/derivacion hacia Auth.
- Tema visual base en `variables.scss` y ajuste de presupuesto CSS.

Validaciones ejecutadas:

- `npm run lint`: OK.
- `npm run build`: OK.
- `npm test -- --watch=false --browsers=ChromeHeadless`: OK, 2 tests, 0 fallos.

Notas de alcance:

- El dashboard aun no consume pacientes porque falta backend de vinculacion.
- El boton de vinculacion queda deshabilitado/proximamente para no simular permisos.
- El storage actual usa `localStorage`; puede evolucionar a storage seguro para mobile.

## Decision vigente

El orden de trabajo sigue siendo incremental y seguro:

1. Auth base y roles: completado en backend y frontend inicial.
2. Vinculacion tutor-paciente con reglas de autorizacion: siguiente prioridad.
3. Estado del paciente, ubicacion y calendario: despues de vinculacion aprobada.
4. Notificaciones e historial: despues del dashboard con datos autorizados.
5. Chat o comunicacion avanzada: fase posterior/controlada.

## Lo que queda por hacer

### Prioridad 1: Merge y estabilizacion

- Crear PR de `feat/auth-frontend-flow` hacia `main` en `hospital-familia-app`.
- Revisar visualmente login, registro y dashboard en mobile y desktop.
- Probar flujo completo con backend corriendo localmente.
- Confirmar CORS si Angular y Spring corren en puertos distintos.
- Decidir si el frontend debe quedar en `main` despues del PR o si se requiere una rama de integracion.

### Prioridad 2: Vinculacion Tutor-Paciente Backend

Objetivo: permitir que un tutor autenticado solicite acceso a un paciente sin exponer datos clinicos hasta aprobacion.

Pendiente backend:

- Entidades `Patient` o equivalente administrativo inicial.
- Entidad `TutorPatientLink` o `LinkRequest`.
- Estados: `PENDING`, `APPROVED`, `REJECTED`, `REVOKED`.
- Endpoint tutor para solicitar vinculacion por codigo manual.
- Endpoint tutor para ver estado de sus solicitudes.
- Endpoint staff para listar pendientes.
- Endpoint staff para aprobar/rechazar.
- Validadores para evitar duplicados y fugas de datos.
- Tests de permisos y casos borde.

Endpoints sugeridos:

- `POST /api/linking/request`
- `GET /api/linking/my-requests`
- `GET /api/linking/my-patients`
- `GET /api/linking/pending`
- `PUT /api/linking/{id}/approve`
- `PUT /api/linking/{id}/reject`
- `DELETE /api/linking/{id}`

### Prioridad 3: Vinculacion Tutor-Paciente Frontend

Pendiente frontend:

- Pantalla `/link-patient` o `/patient/link`.
- Formulario de codigo manual primero; QR puede quedar posterior.
- Estado de solicitud enviada/en revision.
- Empty state actualizado en dashboard con solicitud pendiente.
- Mensajes de error claros: codigo invalido, solicitud duplicada, sin permisos.
- Guardar la experiencia en menos de 3 taps desde dashboard.

### Prioridad 4: Dashboard con datos autorizados

Solo despues de tener vinculacion aprobada.

Pendiente backend:

- Endpoint para listar pacientes vinculados al tutor autenticado.
- Endpoint de estado visible y limitado del paciente.
- Reglas para no exponer historia clinica completa.
- Auditoria/logs de acceso por tutor.

Pendiente frontend:

- Tarjeta de paciente real solo si hay vinculo `APPROVED`.
- Estado general, servicio, ubicacion y ultima actualizacion.
- Empty/loading/error states.
- Manejo de multiples pacientes.

### Prioridad 5: Calendario y eventos

Pendiente:

- Modelo de eventos visibles para familia.
- Eventos proximos 30 dias.
- Filtros por tipo.
- Historial acotado segun definicion del MVP.
- UI de detalle de evento.

### Prioridad 6: Seguridad, operacion y calidad

Pendiente:

- CORS explicito para frontend local y futuro dominio.
- Politica de expiracion de access/refresh tokens validada.
- Revocacion persistida de refresh tokens/sesiones.
- Rate limiting en auth.
- Logs de seguridad sin datos sensibles.
- Variables de entorno reales para desarrollo, test y produccion.
- Revisar exposicion de Swagger en produccion.
- Evaluar storage seguro en mobile para tokens.
- Agregar tests frontend para AuthService, guard e interceptor.

## Riesgos actuales

- Si se avanza a pacientes sin vinculacion aprobada, aumenta el riesgo de fuga de datos sensibles.
- `localStorage` es suficiente para el primer corte web, pero no ideal para una app mobile con datos sensibles.
- Falta CORS probado end-to-end entre Ionic y Spring Boot.
- Logout stateless no revoca tokens ya emitidos.
- El endpoint productivo de frontend usa placeholder `https://api.hospitalfamilia.com/api` y debe definirse antes de despliegue real.

## Criterios para cerrar la siguiente entrega

- PR frontend mergeado a `main`.
- Backend y frontend corren localmente juntos.
- Tutor puede registrarse desde frontend contra backend real.
- Tutor puede iniciar sesion desde frontend contra backend real.
- Dashboard protegido valida token y muestra estado vacio sin datos clinicos.
- Usuario no autenticado no accede a `/dashboard/tutor`.
- No hay datos de paciente visibles antes de vinculacion aprobada.
- `mvn test`, `npm run lint`, `npm run build` y tests headless frontend pasan.

## Prompt recomendado para el siguiente agente

```text
Actua como arquitectIA backend.
Continua en C:\Users\Crist\Proyectos\Hospital - Familia\hospital-familia-server.
Implementa el modulo de vinculacion tutor-paciente despues de Auth.
Mantener paquete base com.hospitalfamilia.server.
No exponer datos clinicos antes de vinculo aprobado.
Crear entidades, migraciones Flyway, endpoints, servicios, reglas de autorizacion y tests.
Validar con mvn test.
```

## Prompt recomendado para frontend despues del backend de vinculacion

```text
Actua como interfazIA frontend.
Continua en C:\Users\Crist\Proyectos\Hospital - Familia\hospital-familia-app.
Conecta el dashboard tutor al modulo real de vinculacion tutor-paciente.
Crear pantalla de vinculacion por codigo manual, estados pendiente/aprobado/rechazado, empty states y errores claros.
No mostrar datos clinicos sin vinculo APPROVED.
Validar con npm run lint, npm run build y tests headless.
```
