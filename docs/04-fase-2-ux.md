# Fase 2: Definición UX, Flujos y Comunicación

## Estado

✏️ Prototipado

## Mapa de Flujos Principales

### Flujo 1: Onboarding (Tutor nuevo)

```
Descarga app
  ↓
Splash screen (2s)
  ↓
¿Tienes cuenta? NO → Signup flow
            SI → Login flow
  ↓
[Signup]
  Email/Teléfono → Verificación OTP → Contraseña → Términos → Dashboard vacío
  ↓
[Primer acceso]
  "Vincular primer paciente" → Escanear QR → Solicitud enviada
  ↓
Dashboard (esperando aprobación)
  "Tu solicitud está en revisión"
```

### Flujo 2: Ver estado del paciente

```
Home/Dashboard
  ↓
[Si aprobado]
  Tarjeta de paciente → Click
  ↓
  Detalle:
    - Ubicación (servicio, piso, habitación)
    - Estado general (color indicator)
    - Responsable médico
    - Última actualización
    - Botón "Ver calendario"
    - Botón "Contactar staff"
```

### Flujo 3: Revisar calendario y procedimientos

```
Dashboard → Pestaña "Calendario" (o click en paciente)
  ↓
Calendario visual (próximos 30 días)
  ↓
Click en evento
  ↓
Detalle:
  - Tipo (Cirugía/Examen/Visita/Cambio de estado)
  - Fecha/Hora/Duración estimada
  - Servicio/Ubicación
  - Responsable (si aplicable)
  - Notas (si las hay)
  - Botón "Recordatorio" (on/off)
```

### Flujo 4: Vincular nuevo paciente

```
Home → Botón "Agregar paciente" / Menu → "Vincular paciente"
  ↓
Opción A: Escanear QR
  - Camera permisos → Scan código
  - "Solicitud enviada" → Esperar aprobación
  ↓
Opción B: Código manual
  - Input campo → "Solicitud enviada"
  ↓
[Staff approval]
  - Email/Push "Solicitud aprobada"
  - Paciente aparece en lista principal
```

### Flujo 5: Staff aprueba vinculación

```
Staff: App web/desktop → Dashboard
  ↓
Tab "Vinculaciones pendientes"
  ↓
Listado:
  - Nombre tutor, Paciente, Fecha solicitada
  - Botones: Aprobar / Rechazar
  ↓
Click Aprobar
  ↓
"Confirmado" + Notificación a tutor
```

---

## Tareas Críticas por Tipo de Usuario

### Tutor

1. **Crítica**: Ver estado actual del paciente (acceso rápido, <3 taps)
2. **Crítica**: Revisar próximos procedimientos/cambios
3. **Crítica**: Recibir notificaciones sin perderlas
4. **Importante**: Vincularse a un paciente nuevo
5. **Importante**: Recuperar contraseña si la olvida
6. **Nice-to-have**: Revisisualizar historial últimas 7 días

### Staff (Médico/Administrativo)

1. **Crítica**: Crear/actualizar evento del paciente
2. **Crítica**: Aprobar vinculaciones nuevas
3. **Importante**: Ver lista de pacientes asignados
4. **Importante**: Enviar notificación ad-hoc si es necesario

### Paciente

1. **Importante**: Ver su estado actual (lectura)
2. **Importante**: Ver procedimientos programados
3. **Important**: Ver historial de notificaciones

---

## Wireframes Conceptuales

### Pantalla 1: Login

```
┌─────────────────────┐
│   Hospital Familia  │
│       Logo          │
├─────────────────────┤
│ Email              │
│ [____________________]│
│                     │
│ Contraseña         │
│ [____________________]│
│                     │
│ [ INGRESAR ]       │
│                     │
│ ¿No tienes cuenta?  │
│ Registrate aqui    │
│                     │
│ ¿Olvidaste tu pass?│
│ Recuperar          │
└─────────────────────┘
```

### Pantalla 2: Dashboard Tutor

```
┌─────────────────────┐
│ ← Perfil  Notif  ⚙ │
├─────────────────────┤
│ Hola, María         │
│                     │
│ ┌─────────────────┐ │
│ │ Juan García     │ │
│ │ 🟢 ESTABLE      │ │
│ │ Cirugía General │ │
│ │ Piso 3, H302    │ │
│ │ Dr. López       │ │
│ │ Actualizado hace│ │
│ │ 10 minutos      │ │
│ └─────────────────┘ │
│                     │
│ [ + Vincular ]      │
│                     │
│ PRÓXIMOS EVENTOS    │
│ ┌─────────────────┐ │
│ │ Radiología      │ │
│ │ Hoy 14:30       │ │
│ │ ⚠ Recordatorio  │ │
│ └─────────────────┘ │
│                     │
│ 📞 CONTACTAR STAFF  │
└─────────────────────┘
```

### Pantalla 3: Detalle de Evento

```
┌─────────────────────┐
│ ← Radiología        │
├─────────────────────┤
│ Radiografía de      │
│ Pecho               │
│                     │
│ 📅 Hoy a las 14:30  │
│ ⏱ ~30 minutos       │
│                     │
│ Servicio:           │
│ Radiología (Piso 2) │
│                     │
│ Responsable:        │
│ Dr. Gómez           │
│                     │
│ Notas:              │
│ "En ayunas"         │ │
│                     │
│ [ 🔔 Recordatorio ] │ │
│ [ 📞 Contactar ]    │ │
└─────────────────────┘
```

### Pantalla 4: Calendario

```
┌─────────────────────┐
│ ← Mayo              │
├─────────────────────┤
│ L  M  X  J  V  S  D│
│ 28 29 30 1  2  3  4│
│  5  6  7  8  9 10 11│
│ 12 13 14 15 16 17 18│
│ 19 20 21 22 23 24 25│
│ 26 27 28 29 30 31  1│
│                     │
│ EVENTOS PRÓXIMOS    │
│                     │
│ HOY (3 de mayo)     │
│ ├ 14:30 Radiología  │
│ ├ 18:00 Visita Dr.  │
│                     │
│ MAÑANA (4 de mayo)  │
│ ├ 09:00 Examen      │
│                     │
│ Ver todos...        │
└─────────────────────┘
```

### Pantalla 5: Notificaciones

```
┌─────────────────────┐
│ ← Notificaciones    │
├─────────────────────┤
│ HOY                 │
│                     │
│ 16:45               │
│ ✓ Juan cambió de    │ │
│   servicio          │
│   Está en Cirugía   │
│   General piso 3    │
│                     │
│ 14:35               │
│ ⚠ Recordatorio:     │
│   Radiología a las  │
│   14:30             │
│                     │
│ AYER                │
│ 10:20               │
│ ✓ Vinculación       │
│   aprobada          │
└─────────────────────┘
```

---

## Jerarquía de Pantallas y Acciones

### Estructura: Bottom Tab Navigation (Tutor)

```
Home        Calendar       Notifications    Profile
────        ────           ─────────        ───────
• Estado    • Próximos 30  • Lista de       • Datos
  actual      eventos        notif recent   • Cambiar
• Rápido    • Filtros      • Historial 7d     password
  acceso    • Detalle      • Silenciar      • Mis
• Vincular                   notif            pacientes
                                           • Cerrar
                                             sesión
```

### Estructura: Staff Web/Desktop

```
Dashboard (Sidebar left)
├ Inicio
├ Mis pacientes
├ Vinculaciones pendientes
├ Crear evento
├ Reportes
└ Configuración
```

---

## Estados de Carga, Error, Vacío, Éxito y Permisos

### Estado: Cargando

```
┌─────────────────────┐
│   Hospital Familia  │
│                     │
│    ⟳ Cargando...    │
│   (spinner)         │
│                     │
│   "Trayendo tu      │
│    información..."  │
└─────────────────────┘
```

### Estado: Vacío (Sin pacientes vinculados)

```
┌─────────────────────┐
│ Hola, María         │
├─────────────────────┤
│                     │
│      📋             │
│                     │
│ No tienes pacientes │
│ vinculados aún      │
│                     │
│ [ + Vincular tu     │
│   primer paciente ] │
│                     │
└─────────────────────┘
```

### Estado: Error (Fallo de vinculación)

```
┌─────────────────────┐
│ ← Vincular          │
├─────────────────────┤
│                     │
│      ⚠️             │
│                     │
│ No pudimos aprobar  │
│ la solicitud        │
│                     │
│ Código: 404         │
│ Mensaje:            │
│ Paciente no         │
│ encontrado          │
│                     │
│ [ Reintentar ]      │
│ [ Ir atrás ]        │
└─────────────────────┘
```

### Estado: Éxito (Vinculación aprobada)

```
┌─────────────────────┐
│                     │
│      ✓              │
│                     │
│ ¡Listo!             │
│                     │
│ Tu solicitud fue    │
│ aprobada            │
│ Ahora puedes ver a  │
│ Juan García         │
│                     │
│ [ Ir al inicio ]    │
└─────────────────────┘
```

### Estado: Permisos (Tutor viendo paciente no vinculado)

```
┌─────────────────────┐
│      🔒             │
│                     │
│ No tienes acceso    │
│                     │
│ Tu solicitud de     │
│ vinculación para    │
│ María López está    │
│ en revisión         │
│                     │
│ Espera la           │
│ aprobación del      │
│ personal médico     │
│                     │
│ [ Ir al inicio ]    │
└─────────────────────┘
```

---

## Guía Breve de Tono y Microcopy

### Principios de tono

1. **Calmo y empático**: Reconocer que el contexto es emocional
2. **Claro y sin jerga médica**: Explicar en lenguaje simple
3. **Oportuno**: Información exacta sin demoras innecesarias
4. **Accesible**: Fuentes legibles, contraste alto, lenguaje inclusivo

### Microcopy ejemplos

| Situación            | Mala                         | Buena                                                               |
| -------------------- | ---------------------------- | ------------------------------------------------------------------- |
| Esperando aprobación | "Status: PENDING"            | "Tu solicitud está en revisión. El personal médico la verá pronto." |
| Cambio de estado     | "Patient moved to unit 3B"   | "Juan cambió al servicio de Cirugía General, piso 3"                |
| Error de conexión    | "ERR_CONNECTION_TIMEOUT"     | "No tenemos conexión. Reintentaremos en 5 segundos."                |
| Próximo evento       | "SCHEDULED: RADIOLOGY 14:30" | "Radiología hoy a las 14:30. ¿Recordatorio?"                        |
| Sin eventos          | "No data"                    | "No hay eventos programados en los próximos 7 días."                |

### Ejemplos de redacción completa

**Situación**: Tutor ve que su familiar está en procedimiento

```
Texto en pantalla:
"Acualmente en procedimiento
Radiología | Piso 2

Comenzó a las 14:30
Duración estimada: 30 minutos

Recibirás una notificación cuando termine"
```

**Situación**: Vinculación rechazada

```
"Tu solicitud no pudo ser aprobada

Razón: El paciente no tiene tutor pendiente de aprobación

¿Necesitas ayuda?
📞 Contacta al staff"
```

---

## Accesibilidad (WCAG AA)

### Tipografía

- Tamaño mínimo: 12pt (body), 16pt (tuples importantes)
- Contraste: 4.5:1 mínimo (normal text)
- Font: -apple-system, sans-serif

### Colores (Paleta)

```
Verde (Estable):    #22C55E
Amarillo (Vigilancia): #F59E0B
Rojo (Procedimiento/Alerta): #EF4444
Gris (Neutral):     #6B7280
Fondo:              #FFFFFF
Texto:              #1F2937
```

### Interactividad

- Botones: mín 44x44px (touch targets)
- Espaciado: mín 8px entre interactivos
- Focus visible: outline 2px azul
- Hover states: contraste de color

---

## Siguiente paso

→ Testing con 5 tutores reales
→ Iterar wireframes
→ Avanzar a Fase 3: Arquitectura Técnica
