# Fase 3: Diseño Funcional y Técnico

## Estado

🏗️ En diseño

## Arquitectura Funcional

### Capas de la Aplicación

```
┌─────────────────────────────────────────────────┐
│           Presentation Layer (UI)               │
│   (Angular 20 + Ionic 8 + Capacitor 8)         │
├─────────────────────────────────────────────────┤
│         Application/Business Logic              │
│   (Services, Guards, Interceptors)              │
├─────────────────────────────────────────────────┤
│           API Integration Layer                 │
│   (HTTP Client, Request/Response handlers)      │
├─────────────────────────────────────────────────┤
│            Backend API Gateway                  │
│    (Spring Boot 3.5 Endpoints, Auth)            │
├─────────────────────────────────────────────────┤
│     Business & Domain Logic (Spring Services)   │
├─────────────────────────────────────────────────┤
│         Data Access Layer (JPA/Hibernate)       │
├─────────────────────────────────────────────────┤
│        PostgreSQL Database (Encrypted)          │
└─────────────────────────────────────────────────┘
```

### Componentes principales

#### Frontend (Angular/Ionic)

```
modules/
├── auth/
│   ├── login.component
│   ├── signup.component
│   ├── auth.service
│   ├── auth.guard
│   └── auth.interceptor
├── dashboard/
│   ├── home.component
│   ├── patient-card.component
│   ├── dashboard.service
│   └── state-indicator.component
├── calendar/
│   ├── calendar.component
│   ├── event-detail.component
│   └── calendar.service
├── notifications/
│   ├── notifications.component
│   ├── notification.service
│   └── push.service
├── patient/
│   ├── link-patient.component
│   ├── patient-list.component
│   └── patient.service
├── profile/
│   └── profile.component
└── shared/
    ├── models/
    ├── interceptors/
    ├── guards/
    └── pipes/
```

#### Backend (Spring Boot)

```
src/main/java/com/hospitalfamilia/
├── HospitalFamiliaServerApplication
├── config/
│   ├── SecurityConfig
│   ├── JwtConfig
│   ├── CorsConfig
│   └── FlywayConfig
├── api/
│   ├── controller/
│   │   ├── AuthController
│   │   ├── PatientController
│   │   ├── TutorController
│   │   ├── EventController
│   │   └── NotificationController
│   ├── dto/
│   │   ├── LoginRequest
│   │   ├── LoginResponse
│   │   ├── PatientDTO
│   │   ├── EventDTO
│   │   └── LinkingRequestDTO
│   └── mapper/
│       └── (MapStruct mappers)
├── service/
│   ├── AuthService
│   ├── PatientService
│   ├── TutorService
│   ├── LinkingService
│   ├── EventService
│   ├── NotificationService
│   └── SecurityService
├── repository/
│   ├── PatientRepository
│   ├── TutorRepository
│   ├── LinkingRequestRepository
│   ├── EventRepository
│   └── NotificationRepository
├── entity/
│   ├── Patient
│   ├── Tutor
│   ├── LinkingRequest
│   ├── Event
│   ├── Notification
│   ├── User (abstract)
│   └── Role
├── security/
│   ├── JwtTokenProvider
│   ├── JwtAuthenticationFilter
│   └── CustomUserDetailsService
├── exception/
│   ├── ResourceNotFoundException
│   ├── UnauthorizedException
│   ├── ValidationException
│   └── GlobalExceptionHandler
└── util/
    ├── EncryptionUtil
    └── DateUtil
```

---

## Definición de Módulos

### Módulo 1: Autenticación & Autorización (Auth)

**Responsabilidad**: Gestionar login, signup, sesiones, roles y permisos

**Componentes**:

- `AuthService`: Login, signup, logout, refresh token
- `JwtTokenProvider`: Generar y validar JWT
- `AuthGuard`: Proteger rutas (¿Usuario autenticado?)
- `RoleGuard`: Validar roles específicos
- `JwtAuthenticationFilter`: Interceptar requests

**Entidades**:

- `User` (abstract)
  - `Tutor` extends User
  - `Paciente` extends User
  - `Staff` extends User
- `Role` (TUTOR, PACIENTE, DOCTOR, ADMIN)

**Endpoints**:

```
POST   /api/auth/signup          → Crear cuenta
POST   /api/auth/login           → Login
POST   /api/auth/refresh         → Refresh token
POST   /api/auth/logout          → Logout
POST   /api/auth/verify-otp      → Verificar OTP
POST   /api/auth/forgot-password → Recuperar contraseña
```

---

### Módulo 2: Vinculación Tutor-Paciente (Linking)

**Responsabilidad**: Gestionar solicitudes y aprobación de vinculaciones

**Componentes**:

- `LinkingService`: Crear, aprobar, rechazar solicitudes
- `LinkingRepository`: Persistencia

**Entidades**:

- `LinkingRequest`
  - `tutorId` (FK)
  - `patientId` (FK)
  - `status` (PENDING, APPROVED, REJECTED)
  - `createdAt`, `approvedAt`, `approvedBy`
  - `rejectionReason` (nullable)

**Endpoints**:

```
POST   /api/linking/request          → Crear solicitud (tutor)
GET    /api/linking/pending          → Listar pendientes (staff)
PUT    /api/linking/{id}/approve     → Aprobar (staff)
PUT    /api/linking/{id}/reject      → Rechazar (staff)
DELETE /api/linking/{id}             → Revocar (tutor/staff)
GET    /api/linking/my-patients      → Listar mis pacientes (tutor)
```

---

### Módulo 3: Pacientes (Patients)

**Responsabilidad**: Gestionar datos e información de pacientes

**Componentes**:

- `PatientService`: CRUD, búsqueda, filtrado
- `PatientRepository`: Persistencia

**Entidades**:

- `Patient`
  - `id`, `firstName`, `lastName`, `email`, `phone`
  - `dateOfBirth`, `medicalRecordNumber`
  - `currentService` (text), `currentFloor`, `currentRoom`
  - `currentStatus` (STABLE, MONITORED, IN_PROCEDURE)
  - `responsibleDoctor` (text)
  - `lastStatusUpdate` (timestamp)
  - `encryptedData` (JSON con info sensible)

**Endpoints**:

```
GET    /api/patients/{id}           → Obtener detalle (autorizado)
GET    /api/patients/{id}/status    → Obtener estado actual
GET    /api/patients/{id}/events    → Obtener eventos (próximos 30d)
GET    /api/patients/{id}/history   → Obtener historial (últimos 7d)
PUT    /api/patients/{id}/status    → Actualizar estado (staff)
```

---

### Módulo 4: Eventos (Events)

**Responsabilidad**: Gestionar procedimientos, cambios de estado, visitas

**Componentes**:

- `EventService`: CRUD, notificaciones
- `EventRepository`: Persistencia

**Entidades**:

- `Event`
  - `id`, `patientId` (FK), `type` (SURGERY, EXAM, VISIT, STATE_CHANGE)
  - `title`, `description`
  - `scheduledAt`, `estimatedDuration`
  - `service`, `location`
  - `responsibleStaff` (text)
  - `status` (SCHEDULED, IN_PROGRESS, COMPLETED, CANCELLED)
  - `createdAt`, `createdBy`

**Endpoints**:

```
POST   /api/events              → Crear evento (staff)
GET    /api/events/patient/{id} → Listar eventos de paciente
PUT    /api/events/{id}         → Actualizar evento (staff)
PUT    /api/events/{id}/status  → Cambiar estado (staff)
DELETE /api/events/{id}         → Cancelar evento (staff)
```

---

### Módulo 5: Notificaciones (Notifications)

**Responsabilidad**: Gestionar y enviar notificaciones push/in-app

**Componentes**:

- `NotificationService`: Crear, enviar, registrar
- `PushNotificationService`: Integración FCM/APNs
- `NotificationRepository`: Persistencia

**Entidades**:

- `Notification`
  - `id`, `tutorId` (FK)
  - `type` (STATE_CHANGE, NEW_EVENT, REMINDER, LINKING_APPROVED)
  - `title`, `message`
  - `relatedEntityId`, `relatedEntityType`
  - `isRead`, `readAt`
  - `createdAt`
  - `pushSent`, `pushSentAt`

**Endpoints**:

```
GET    /api/notifications              → Listar notif recientes (tutor)
PUT    /api/notifications/{id}/read    → Marcar como leída
DELETE /api/notifications/{id}         → Eliminar notif
GET    /api/notifications/preferences  → Obtener preferencias (tutor)
PUT    /api/notifications/preferences  → Actualizar preferencias
```

---

## Contratos API y Entidades Principales

### DTO: LoginRequest

```json
{
  "email": "string",
  "password": "string"
}
```

### DTO: LoginResponse

```json
{
  "accessToken": "jwt_token_here",
  "refreshToken": "refresh_jwt_here",
  "user": {
    "id": "uuid",
    "email": "string",
    "name": "string",
    "role": "TUTOR|PACIENTE|DOCTOR|ADMIN"
  },
  "expiresIn": 3600
}
```

### DTO: PatientDTO

```json
{
  "id": "uuid",
  "firstName": "string",
  "lastName": "string",
  "medicalRecordNumber": "string",
  "currentService": "string",
  "currentFloor": "number",
  "currentRoom": "string",
  "currentStatus": "STABLE|MONITORED|IN_PROCEDURE",
  "responsibleDoctor": "string",
  "lastStatusUpdate": "iso8601_timestamp"
}
```

### DTO: EventDTO

```json
{
  "id": "uuid",
  "patientId": "uuid",
  "type": "SURGERY|EXAM|VISIT|STATE_CHANGE",
  "title": "string",
  "description": "string",
  "scheduledAt": "iso8601_timestamp",
  "estimatedDuration": "number (minutes)",
  "service": "string",
  "location": "string",
  "responsibleStaff": "string",
  "status": "SCHEDULED|IN_PROGRESS|COMPLETED|CANCELLED"
}
```

### DTO: LinkingRequestDTO

```json
{
  "id": "uuid",
  "tutorId": "uuid",
  "patientId": "uuid",
  "status": "PENDING|APPROVED|REJECTED",
  "createdAt": "iso8601_timestamp",
  "approvedAt": "iso8601_timestamp (nullable)",
  "rejectionReason": "string (nullable)"
}
```

---

## Estrategia de Autenticación, Roles y Permisos

### Autenticación: JWT (JSON Web Tokens)

**Flujo**:

```
1. Tutor hace login con email + password
2. Backend valida, genera JWT con claims:
   - sub (user ID)
   - email
   - role
   - exp (expira en 1h)
   - iat (issued at)
3. Token se almacena en device (localStorage si web, secure storage si app)
4. Cada request incluye: Authorization: Bearer <jwt>
5. Backend valida firma y expiración
6. Si expira, usa refresh token (válido por 7 días)
```

### Roles & Permisos (Role-Based Access Control)

| Recurso            | TUTOR | PACIENTE | DOCTOR | ADMIN |
| ------------------ | ----- | -------- | ------ | ----- |
| Ver su paciente    | ✓     | -        | ✓      | ✓     |
| Ver su info        | ✓     | ✓        | ✓      | ✓     |
| Crear evento       | -     | -        | ✓      | ✓     |
| Actualizar estado  | -     | -        | ✓      | ✓     |
| Aprobar linking    | -     | -        | ✓      | ✓     |
| Gestionar usuarios | -     | -        | -      | ✓     |
| Reportes           | -     | -        | -      | ✓     |

**Implementación**:

- `@RoleRequired(TUTOR)` en endpoints
- Guards en Angular con `AuthService.canActivate()`
- Validators en Backend con Spring Security

---

## Decisión de Stack

### Confirmado

- **Frontend**: Angular 20 + Ionic 8 + Capacitor 8
  - ✓ Multiplataforma (iOS/Android)
  - ✓ Progressive Web App
  - ✓ TypeScript para type safety
- **Backend**: Spring Boot 3.5 + Java 21 + Maven
  - ✓ Maduro, escalable
  - ✓ Excelente ecosistema (Security, Data JPA)
  - ✓ Documentación robusta
- **Base de datos**: PostgreSQL + Flyway
  - ✓ Open source, confiable
  - ✓ Soporte nativo para JSON (encrypted fields)
  - ✓ Flyway para versionado de schema
- **Comunicación**: HTTP REST
  - ✓ MVP no requiere real-time (WebSocket futuro)

### Por definir

- **Cache**: Redis (para sesiones, notificaciones)
- **Notificaciones push**: Firebase Cloud Messaging (FCM)
- **Storage de archivos**: AWS S3 o storage local
- **Logs**: ELK stack o CloudWatch

---

## Estrategia de Ambientes

### Desarrollo (DEV)

- Base de datos local (PostgreSQL en Docker)
- Backend localhost:8080
- Frontend localhost:4200
- Seed data con tutores/pacientes de test
- Mock de notificaciones push

### Staging (STAGE)

- Base de datos en RDS (AWS)
- Backend en EC2/Kubernetes
- Frontend en S3 + CloudFront
- Datos de test (no reales)
- FCM real pero sandbox

### Producción (PROD)

- Base de datos RDS encrypted + backups diarios
- Backend con auto-scaling
- Frontend CDN global
- Datos reales encriptados
- FCM real, monitoring activo

**Variables de entorno**:

```
# Backend
SPRING_DATASOURCE_URL=...
SPRING_DATASOURCE_PASSWORD=... (encrypted)
JWT_SECRET=... (encrypted)
FCM_SERVER_KEY=...
HOSPITAL_API_BASE_URL=...

# Frontend
API_BASE_URL=...
ENVIRONMENT=dev|stage|prod
```

---

## Plan QA por Módulo

### Módulo 1: Auth (Semana 1-2)

```
[ ] Login con credenciales válidas
[ ] Login con credenciales inválidas
[ ] Signup con email válido
[ ] Signup con email duplicado
[ ] Token expira en 1h
[ ] Refresh token genera nuevo access token
[ ] JWT invalidado después de logout
[ ] Password recovery funciona
```

### Módulo 2: Linking (Semana 2-3)

```
[ ] Tutor crea solicitud con código QR válido
[ ] Solicitud rechazada con código inválido
[ ] Staff ve solicitudes pendientes
[ ] Staff aprueba vinculación
[ ] Tutor recibe notificación de aprobación
[ ] Vinculación rechazada no queda en BD
[ ] Revocación quita acceso inmediato
```

### Módulo 3: Dashboard (Semana 3-4)

```
[ ] Dashboard carga <2s con 4G
[ ] Estado del paciente se actualiza cada 30s
[ ] Color indicator cambia según estado
[ ] Múltiples pacientes se listan correctamente
[ ] Sin pacientes muestra estado vacío
```

### Módulo 4: Events (Semana 4-5)

```
[ ] Evento creado por staff es visible en calendario
[ ] Evento en pasado no aparece en "próximos"
[ ] Detalle de evento muestra toda info
[ ] Recordatorio funciona 24h antes
[ ] Cancelación de evento notifica a tutor
```

### Módulo 5: Notifications (Semana 5-6)

```
[ ] Push recibida en <10s
[ ] In-app notification visible
[ ] Historial persiste 7 días
[ ] Preferencias se guardan
[ ] Silenciado respeta horarios
```

---

## Seguridad

### Encriptación

- Contraseñas: bcrypt (rounds=12)
- Datos sensibles en BD: AES-256 (Spring Data)
- HTTPS en tránsito (certificado SSL)

### Validación

- Input sanitization (OWASP)
- SQL injection prevention (prepared statements)
- XSS prevention (Angular built-in)
- CSRF tokens en forms

### Auditoría

- Logs de login/logout
- Logs de cambios de estado
- Logs de acceso a datos sensibles
- Rotación de logs cada 7 días

---

## Siguiente paso

→ Diseño de base de datos (schema Flyway)
→ Implementar Módulo 1 (Auth)
→ Iteración con feedback de fase UX
