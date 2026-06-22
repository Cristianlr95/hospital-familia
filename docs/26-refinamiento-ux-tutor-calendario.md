# Refinamiento UX tutor y calendario familiar

Fecha: 2026-06-22

## Objetivo

Mejorar la lectura y operación del dashboard tutor, priorizando la información clínica autorizada y reduciendo la altura innecesaria de tarjetas en escritorio y móvil.

## Cambios implementados

- Inicio reorganizado por prioridad: estado del paciente, próximo evento, avisos, solicitudes y pacientes autorizados.
- Tarjetas compactas y de altura natural, sin filas que se estiran por contenido vecino.
- Próximo evento filtrado para excluir hitos completados o cancelados.
- Agenda declarada explícitamente como próximos 30 días, de acuerdo con el contrato actual del backend.
- Calendario familiar mensual reutilizable, con navegación, indicadores por tipo y detalle de hitos por día.
- Estados técnicos traducidos al español: Programado, En curso, Completado, Cancelado, Aprobado y otros.
- Fechas de Angular configuradas globalmente con locale `es-CL`.
- Avisos priorizados antes del formulario de contacto y preferencias presentadas en filas compactas.
- Historial de actividad acompañado por la agenda visual y sin fechas ISO visibles.
- Sesiones agrupadas en este dispositivo, otros accesos e historial de sesiones cerradas.
- Otros accesos limitados inicialmente a tres, con expansión explícita.
- Acciones concurrentes de revocación bloqueadas para evitar solicitudes cruzadas.

## Componente reutilizable

`FamilyCalendarComponent` recibe eventos autorizados y se mantiene presentacional. No crea ni modifica eventos, por lo que puede reutilizarse en futuras vistas staff sin mezclar permisos.

Incluye:

- semana iniciada en lunes;
- agrupación por fecha local;
- selección de día accesible por botones;
- colores por tipo de evento;
- detalle con paciente, hora, ubicación y estado;
- adaptación a escritorio y móvil.

## Validación

- `npm run lint`: aprobado.
- `npm run build`: aprobado.
- `npm test -- --watch=false --browsers=ChromeHeadlessCI`: 5 pruebas aprobadas.
- QA visual en `1440x900`: Inicio, Pacientes, Avisos y Cuenta aprobados.
- QA visual en `390x844`: navegación inferior y secciones sin desbordamiento horizontal.
- Backend `http://localhost:8080/actuator/health`: estado `UP`.
- Frontend `http://localhost:4200`: acceso tutor y navegación funcional.

## Trazabilidad

- Repositorio frontend: `hospital-familia-app`.
- Pull request: `#19 Refine tutor dashboard usability and calendar`.
- Merge frontend: `cd28e20`.
- Agentes utilizados: diseño/usabilidad, arquitectura Angular y QA.

## Alcance futuro

El calendario muestra los próximos 30 días porque ese es el rango entregado actualmente por la API. Un calendario histórico o de rango arbitrario requerirá ampliar el endpoint con parámetros `from` y `to`; no se simula información fuera de ese contrato.
