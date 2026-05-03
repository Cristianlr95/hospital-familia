# Ruta de desarrollo

## Objetivo
Usar el documento base `base/Informe Proyecto de Titulo - Cristian Lagos.pdf` como fuente principal para definir, validar y construir `Hospital - Familia` de forma incremental.

## Enfoque metodologico integrado
Esta ruta combina las metodologias de:
- `visionIA product`
- `uxIA design`
- `arquitectIA backend`
- `interfazIA frontend`
- `calidadIA QA`
- `infraIA devops`
- `estrategiaIA business`
- `costIA analysis`

## Stack base acordado
- Frontend: Angular 20 + Ionic 8 + Capacitor 8
- Backend: Spring Boot 3.5 + Java 21 + Maven Wrapper
- Datos: PostgreSQL + Flyway

## Fase 0. Lectura y descomposicion del documento base
Objetivo: convertir el PDF en insumos ejecutables.

Entregables:
- resumen ejecutivo del documento,
- problema principal que se busca resolver,
- usuarios y actores involucrados,
- funcionalidades mencionadas o implicitas,
- restricciones tecnicas, operativas o academicas,
- dudas abiertas que requieren validacion.

Salida esperada:
- una version resumida del informe en lenguaje de producto,
- una lista de supuestos por validar.

## Fase 1. Definicion de producto y alcance MVP
Objetivo: bajar la idea a un MVP claro y medible.

Entregables:
- propuesta de valor,
- perfil de usuario principal y secundario,
- alcance MVP,
- backlog inicial con priorizacion MoSCoW,
- historias de usuario,
- criterios de aceptacion iniciales,
- riesgos de producto.

Preguntas que esta fase debe responder:
- que problema urgente resuelve para familias y/o hospital,
- quien usara primero la solucion,
- que queda fuera del MVP,
- como se medira el valor inicial.

Preguntas criticas adicionales:
- que datos clinico-administrativos se pueden mostrar legal y eticamente,
- quien autoriza y revoca la relacion tutor-paciente,
- si el chat entra en MVP o queda para una fase controlada,
- como convivira con sistemas hospitalarios existentes,
- como se medira impacto en ansiedad, tiempos de respuesta o satisfaccion.

## Fase 2. Definicion UX, flujos y comunicacion
Objetivo: asegurar una experiencia clara, calmada y util en un contexto sensible.

Entregables:
- mapa de flujos principales,
- tareas criticas por tipo de usuario,
- wireframes conceptuales,
- jerarquia de pantallas y acciones,
- estados de carga, error, vacio, exito y permisos,
- guia breve de tono y microcopy.

Principios de trabajo:
- reducir friccion en momentos sensibles,
- priorizar mobile,
- evitar sobrecarga cognitiva,
- alinear interfaz y narrativa con confianza, claridad y acompanamiento.

## Fase 3. Diseno funcional y tecnico
Objetivo: preparar una base implementable sin deuda temprana innecesaria.

Entregables:
- arquitectura funcional,
- definicion de modulos,
- contratos API y entidades principales,
- estrategia de autenticacion, roles y permisos,
- decision de stack,
- estrategia de ambientes,
- plan QA por modulo.

## Fase 4. Implementacion incremental
Objetivo: construir por modulos pequenos y verificables.

Orden sugerido:
1. modulo base de autenticacion y roles,
2. vinculacion tutor-paciente con reglas de autorizacion,
3. estado del paciente, ubicacion y calendario de eventos o visitas,
4. paneles o vistas de seguimiento,
5. notificaciones o comunicacion si aplica,
6. chat en fase posterior o controlada,
7. mejoras secundarias.

Reglas:
- cambios pequenos,
- validacion temprana,
- no romper compatibilidad,
- separar critico de opcional.

## Fase 5. QA, operacion y despliegue beta
Objetivo: validar antes de abrir el sistema a uso real.

Entregables:
- checklist QA,
- casos felices y de borde,
- matriz de permisos,
- plan de despliegue,
- variables de entorno,
- logs y health checks,
- plan de backup,
- criterios de salida a beta.

## Fase 6. Validacion comercial y optimizacion
Objetivo: aprender con usuarios reales y ajustar valor, operacion y costos.

Entregables:
- reporte de feedback beta,
- metricas de adopcion,
- riesgos operativos reales,
- revision de costos mensuales,
- mejoras priorizadas para siguiente iteracion.

## Riesgos transversales
- MVP demasiado amplio para una primera entrega.
- UX recargada o poco clara en un contexto emocionalmente delicado.
- Seguridad y permisos insuficientes para datos sensibles.
- Costos operativos subestimados antes de validar adopcion.
- Falta de observabilidad, backups o estrategia de soporte.

## Criterio de avance
No avanzar de fase sin dejar evidencia minima:
- decisiones tomadas,
- pendientes abiertos,
- riesgos visibles,
- entregables escritos en `docs/`.
