# Fase 0: Lectura y Descomposición del Documento Base

## Estado

🔄 En progreso

## Resumen ejecutivo del documento

El Informe de Proyecto de Título aborda la creación de una solución digital para mejorar la comunicación y seguimiento entre tutores de pacientes y el equipo hospitalario, reduciendo ansiedad y tiempos de espera mediante un sistema móvil accesible, escalable y seguro.

## Problema principal que se busca resolver

Familias de pacientes experimentan:

- Incertidumbre sobre el estado del paciente durante internación o tratamiento
- Dificultad para comunicarse con el equipo médico
- Ansiedad por falta de información oportuna
- Ineficiencia en coordinación de visitas o procedimientos

## Usuarios y actores involucrados

### Primarios

- **Tutor/Familia**: responsable legal del paciente, accede a información y eventos
- **Paciente**: puede ver información básica y notificaciones (si es mayor de edad)
- **Personal Médico/Administrativo**: autoriza información y gestiona eventos

### Secundarios

- Administrativos hospitalarios
- Supervisores/Coordinadores de cuidado
- Auditoría interna (cumplimiento)

## Funcionalidades mencionadas o implícitas

### Críticas (MVP)

1. **Autenticación y roles**
   - Login seguro tutor + paciente
   - Gestión de permisos por rol
   - Recuperación de contraseña

2. **Vinculación tutor-paciente**
   - Registro y validación de relación tutor-paciente
   - Autorización bidireccional
   - Revocación de permisos

3. **Dashboard/Vista de seguimiento**
   - Estado actual del paciente (ubicación, servicio, estado general)
   - Calendario de eventos (procedimientos, visitas, alta)
   - Historial condensado de cambios recientes

4. **Notificaciones**
   - Cambios de estado
   - Procedimientos programados
   - Alertas de disponibilidad

### Secundarias (Futuro)

5. Chat en tiempo real (tutor-staff) - fase controlada
6. Formularios de feedback
7. Integración con sistemas hospitalarios existentes
8. Reportes y analytics

## Restricciones técnicas, operativas o académicas

### Técnicas

- Debe funcionar en iOS y Android (Capacitor)
- Datos sensibles → encriptación en tránsito y reposo
- Cumplir estándares de salud (HIPAA-like)
- Arquitectura escalable para múltiples hospitales

### Operativas

- Implementación gradual en un hospital piloto
- Requiere validación legal/ética de datos
- Capacitación de staff
- Plan de soporte 24/7

### Académicas

- Proyecto de Título: debe tener rigor técnico documentado
- Validación con stakeholders reales
- Métricas de impacto medibles

## Dudas abiertas que requieren validación

### Producto

- ¿Cuál es el flujo exacto de alta/internación que afecta la información mostrada?
- ¿Qué datos específicos son permitidos mostrar a tutores según regulaciones?
- ¿Existe audiencia secundaria además de tutores (ej: hermanos, abuelos)?
- ¿Notificaciones push o solo in-app inicialmente?

### Técnica

- ¿Integración con sistema HIS del hospital o API dedicada?
- ¿Ambientes: desarrollo, staging, producción desde el inicio?
- ¿Base de datos centralizada o por hospital?
- ¿Estrategia de backups y desastre?

### Operativa

- ¿Cuántos usuarios en beta (tuples tutor-paciente)?
- ¿Soporte en idioma único (español) o multiidioma?
- ¿Período de privacidad de datos (ej: 90 días)?

## Supuestos por validar

1. **Supuesto de valor**: Tutores accediendo a información en tiempo real reducirá ansiedad en 30%+ y llamadas al hospital en 40%+.
2. **Supuesto de mercado**: Hospitales adoptan soluciones que mejoren retención de pacientes y satisfacción familiar.
3. **Supuesto técnico**: Capacitor + Angular es suficiente para mantener sincronización con backend real-time si es necesario.
4. **Supuesto normativo**: Mostrar ubicación y estado de paciente es permitido si hay consentimiento previo.
5. **Supuesto de operación**: Un hospital piloto puede generar 100-500 usuarios en 3 meses.

## Salida esperada: Versión resumida en lenguaje de producto

**Hospital - Familia: Conectar familias con la tranquilidad que merecen**

Una app mobile que permite a tutores de pacientes hospitalizados:

- Ver en tiempo real dónde está su familiar, qué servicio lo atiende y cómo está
- Recibir notificaciones automáticas sobre procedimientos, cambios de estado y alta
- Reducir la incertidumbre y la ansiedad mediante información clara y oportuna
- Facilitar coordinación de visitas sin necesidad de llamar constantemente

**Para hospitales**: una herramienta que mejora la experiencia del paciente, reduce llamadas de consulta y fortalece la confianza con las familias.

---

## Siguiente paso

→ Validar supuestos con stakeholders del hospital piloto
→ Avanzar a Fase 1: Definición MVP
