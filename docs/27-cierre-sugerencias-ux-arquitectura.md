# Cierre de sugerencias UX, arquitectura y QA

Fecha: 2026-06-22

## Resultado

Se completaron las sugerencias pendientes detectadas después del refinamiento visual del dashboard tutor.

## Backend

- Los endpoints de eventos tutor y staff aceptan rangos opcionales `from` y `to` en formato ISO-8601.
- El rango máximo permitido es de 366 días.
- La consulta predeterminada conserva una agenda activa de 30 días y excluye eventos cancelados.
- Las consultas con rango explícito permiten revisar histórico, incluidos eventos cancelados.
- Los resultados se ordenan por fecha y por identificador para mantener un orden determinista.
- Se mantiene la autorización tutor-paciente aprobada.
- PR backend `#26`, merge `7ab5ba7`.
- Suite backend: 53 pruebas aprobadas.

## Frontend

- El calendario se movió a `EventsModule` como componente reutilizable y presentacional.
- Se habilitó `ChangeDetectionStrategy.OnPush`.
- Las etiquetas y colores de tipos/estados quedaron centralizados.
- Tutor y staff reutilizan el mismo calendario.
- La navegación mensual consulta el rango real en backend mediante instantes UTC.
- Las solicitudes mensuales se almacenan en caché y se invalidan al crear o actualizar eventos.
- El calendario mantiene el mes visible después de respuestas vacías.
- Se agregó navegación por teclado, foco visible, etiquetas descriptivas y anuncios de mes/día.
- El tutor conserva resultados de pacientes correctos si falla una agenda individual.
- Tutor y staff cargan datos secundarios al entrar en cada sección, una sola vez.
- Los perfiles solo se guardan cuando existen cambios y vuelven a estado limpio después de guardar.
- Las sesiones se separan en activas, expiradas y revocadas.
- Las acciones destructivas requieren confirmación y quedan bloqueadas durante otra operación.
- PR frontend `#20`, merge `2a5e174`.
- Suite frontend: 24 pruebas aprobadas.

## QA

- `npm run lint`: aprobado.
- `npm run build`: aprobado.
- `npm test -- --watch=false --browsers=ChromeHeadlessCI`: 24/24.
- `mvnw test`: 53 pruebas aprobadas.
- Responsive validado sin desplazamiento horizontal en anchos:
  - 320, 360, 390, 520, 759, 760, 1366, 1440 y 1920 px.
- Zoom 200% validado sin desbordamiento horizontal.
- Se revisaron 36 combinaciones de sección y resolución del dashboard tutor.
- La navegación junio → julio → agosto → julio realizó dos solicitudes de rango únicas; el regreso a julio reutilizó caché.

## Agentes

- Backend: implementación de rango y pruebas.
- Frontend: módulo reutilizable, integración tutor/staff y accesibilidad.
- QA: auditoría de carga diferida, sesiones, perfil, responsive y cobertura.

## Estado

El proyecto permanece en 100%. Esta entrega corresponde a robustecimiento post-MVP y elimina las observaciones técnicas y de usabilidad registradas en `docs/26-refinamiento-ux-tutor-calendario.md`.
