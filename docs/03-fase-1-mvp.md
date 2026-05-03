# Fase 1: Definición de Producto y Alcance MVP

## Estado

🎯 Objetivo claro

## Propuesta de valor

**Para tutores/familias:**
Saber en tiempo real cómo está tu familiar sin depender de llamadas. Tranquilidad, claridad, control.

**Para el hospital:**
Familias informadas = menos llamadas, más confianza, mejor imagen, retención de pacientes.

**Para médicos/staff:**
Una fuente única de verdad: Estado en un dashboard, notificaciones automáticas, menos interrupciones por consultas.

---

## Perfiles de usuario

### Usuario Primario: Tutor/Familia

- **Quién**: Persona adulta (18+) responsable legal del paciente durante internación
- **Por qué**: Necesita saber estado y reducir ansiedad
- **Cómo usa**: 3-5 veces al día en promedio, en momentos de preocupación, antes de visitar
- **Devices**: Smartphone (iOS/Android), ocasionalmente web
- **Comportamiento**: Busca información rápida, clara, visual

### Usuario Secundario: Paciente

- **Quién**: Paciente mayor de 18 años (MVP), capacidad de consentimiento
- **Por qué**: Autorregulación, autonomía
- **Cómo usa**: 1-2 veces al día, información sobre su estado y visitas programadas
- **Devices**: Lo mismo que tutor
- **Restricción**: No puede ver historia completa ni modificar estado

### Usuario Terciario: Staff Hospitalario

- **Quién**: Personal médico, enfermería, administrativo
- **Por qué**: Gestionar eventos, autorizar información, reducir consultas
- **Cómo usa**: Transaccional, cuando hay un cambio en el estado del paciente
- **Devices**: Desktop/tablet en hospital, ocasionalmente móvil
- **Restricción**: Solo lectura en turno actual (no puede borrar datos)

---

## Alcance MVP (Must Have)

### M1. Autenticación y Gestión de Identidad

```
- [x] Login/Signup para tutor con email/teléfono
- [x] Verificación de identidad (OTP, email)
- [x] Recuperación de contraseña segura
- [x] Sesión persistent con timeout (15 min inactividad)
- [x] Logout
- [x] Gestión de dispositivos (revocar sesiones)
```

### M2. Vinculación Tutor-Paciente

```
- [x] Registro de paciente (manual o código QR hospitalario)
- [x] Solicitud de vinculación por tutor
- [x] Aprobación en staff (médico/administrativo)
- [x] Estado de vinculación visible
- [x] Revocación de permisos (tutor o staff)
- [x] Múltiples tutores por paciente
```

### M3. Dashboard Principal (Tutor)

```
- [x] Estado actual del paciente (ubicación, servicio, estado general)
  - En internación: servicio, piso, habitación
  - Responsable médico
  - Última actualización
- [x] Indicadores visuales (color code: estable, bajo vigilancia, en procedimiento)
- [x] Acceso rápido a calendario y notificaciones
- [x] Botón de emergencia (contactar con staff) - MVP simplificado
```

### M4. Calendario de Eventos

```
- [x] Listado de procedimientos programados (cirugía, examenes, etc.)
  - Nombre, fecha/hora, servicio, notas
- [x] Visitas autorizadas
- [x] Eventos de estado (ingreso, egreso, cambio de servicio)
- [x] Notificación push cuando hay nuevo evento
- [x] Historia últimos 30 días
```

### M5. Notificaciones

```
- [x] Push notifications (iOS/Android)
  - Cambio de estado del paciente
  - Nuevo procedimiento programado
  - Alta/Egreso
  - Solicitud de revisión importante
- [x] In-app notifications (banner)
- [x] Preferencias: on/off por tipo de evento
- [x] Historial de notificaciones (7 días)
```

### M6. Permisos y Roles Base

```
- [x] Role: Tutor (lectura de su(s) paciente(s), puede revocar sesiones)
- [x] Role: Paciente (lectura limitada de su información)
- [x] Role: Staff (creación/edición de eventos, autorización de vinculaciones)
- [x] Role: Admin (gestión de usuarios, reportes, configuración)
- [x] Control de acceso por relación tutor-paciente
```

---

## Backlog Inicial (Priorización MoSCoW)

### Must Have (Sprint 1-2)

1. Login/Signup tutor
2. Vinculación tutor-paciente simple
3. Dashboard con estado actual
4. Calendario básico
5. Notificaciones push

### Should Have (Sprint 3-4)

6. Historial de cambios de estado (últimas 7 días)
7. Preferencias de notificaciones
8. Múltiples pacientes por tutor
9. Revocación de permisos
10. Página de perfil/configuración

### Could Have (Post-MVP)

11. Chat en tiempo real
12. Formularios de feedback
13. Reportes personalizados
14. Integración con calendario del dispositivo
15. Dark mode

### Won't Have (Fase posterior)

- Video consulta
- Prescripciones
- Historial médico completo (solo resumen)
- Integración con ERP hospitalario (usar API separada)

---

## Historias de Usuario

### H1: Tutor quiere registrarse y verificar su identidad

```
Como tutor
Quiero crear una cuenta con email y verificar mi identidad
Para poder acceder de forma segura a la información de mi familiar

Criterios de aceptación:
- Puedo registrarse con email/teléfono
- Recibo OTP para verificar identidad
- Después de verificar, puedo establecer contraseña
- Sesión se mantiene tras cierre y reapertura de app
```

### H2: Tutor quiere vincular a su familiar internado

```
Como tutor
Quiero vincular a mi familiar internado escaneando código QR
Para poder ver su estado en la app

Criterios de aceptación:
- Veo opción "Vincular paciente" en home
- Puedo escanear código QR de pulsera/cama del paciente
- Se crea solicitud de vinculación
- Staff recibe notificación para aprobar
- Una vez aprobado, veo el paciente en mi lista
```

### H3: Tutor quiere ver estado actual de su familiar

```
Como tutor
Quiero ver en un vistazo dónde está mi familiar y cómo está
Para reducir mi ansiedad y saber si puedo visitarlo

Criterios de aceptación:
- Dashboard muestra: servicio, piso, habitación, estado general
- Indicador visual de color (estable/vigilancia/procedimiento)
- Última actualización visible
- Puedo acceder sin cargas lentas (<2s)
```

### H4: Tutor quiere saber qué procedimientos tiene programados

```
Como tutor
Quiero ver calendario de eventos (cirugías, exámenes, visitas)
Para poder organizarme y llegar a tiempo

Criterios de aceptación:
- Veo listado de eventos próximos (30 días)
- Cada evento muestra: tipo, fecha, hora, servicio, notas
- Puedo filtrar por tipo de evento
- Recibo notificación 24h antes de procedimiento importante
```

### H5: Tutor quiere recibir notificaciones sobre cambios importantes

```
Como tutor
Quiero recibir notificación push cuando mi familiar cambia de estado
Para no perder información crítica

Criterios de aceptación:
- Recibo push: cambio de servicio, procedimiento iniciado, alta
- Puedo preferir notificaciones por tipo
- Puedo silenciar notificaciones en horarios (ej: 22-8h)
- Historial de notificaciones último 7 días visible en app
```

### H6: Staff quiere aprobar vinculación tutor-paciente

```
Como personal médico
Quiero revisar y aprobar solicitudes de vinculación
Para garantizar que solo tutores autorizados accedan a información

Criterios de aceptación:
- Dashboard del staff muestra solicitudes pendientes
- Puedo ver: nombre tutor, nombre paciente, fecha solicitada
- Apruebo o rechazo con motivo (opcional)
- Tutor recibe notificación de resultado
- Rechazadas se archivan automáticamente
```

---

## Criterios de aceptación iniciales

### Funcionalidad

- [ ] Todos los flujos de login/logout funcionan sin errores
- [ ] Vinculación crea relación consistente en DB
- [ ] Dashboard carga en <2s con conexión 4G
- [ ] Notificaciones llegan dentro de 10s de generarse
- [ ] No hay fuga de datos entre pacientes

### Performance

- [ ] App ocupa <150MB en almacenamiento
- [ ] Consumo de batería <5% en uso 1h
- [ ] API responde en <500ms (p95)

### Seguridad

- [ ] Contraseñas encriptadas (bcrypt+)
- [ ] Sesiones con timeout
- [ ] No se expone IDs de pacientes en URLs públicas
- [ ] Logs de acceso por tutor

### UX

- [ ] Tamaño de fuente legible (min 12pt)
- [ ] Colores accesibles (WCAG AA)
- [ ] Flujos completables en <3 taps
- [ ] Mensajes claros en español

---

## Riesgos de Producto

| Riesgo                                       | Impacto | Probabilidad | Mitigación                                   |
| -------------------------------------------- | ------- | ------------ | -------------------------------------------- |
| MVP demasiado amplio, falta tiempo           | Alto    | Media        | Remover chat, feedback forms inicialmente    |
| Fuga de datos de paciente                    | Alto    | Baja         | Auditoría seguridad semana 6                 |
| Staff no activa vinculaciones a tiempo       | Medio   | Alta         | Alert si >2h sin revisión, soporte proactivo |
| Baja adopción por UX confusa                 | Medio   | Media        | Testing con tutores reales semana 3          |
| Integración con HIS más lenta de lo esperado | Medio   | Media        | API mock mientras se negocia acceso          |

---

## Siguiente paso

→ Validar historias con stakeholders del hospital
→ Avanzar a Fase 2: UX y Diseño
