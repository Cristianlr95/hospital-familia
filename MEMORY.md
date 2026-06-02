# MEMORY

## Avance porcentual

- Avance estimado del proyecto Hospital - Familia: 96.4%.
- Marco de calculo: ver `docs/09-marco-avance-porcentual.md`.
- Delta de esta tarea: +0.5 puntos porcentuales por agregar checklist beta readiness con endpoint staff, metrica operativa backend, panel frontend y prueba de integracion.
- Base completada: auth JWT backend/frontend, roles, vinculacion tutor-paciente con aprobacion staff, dashboard tutor protegido, estado visible autorizado, calendario familiar de eventos proximos y regla de no exponer datos de paciente sin vinculo `APPROVED`.
- Base completada ampliada: panel staff conectado a endpoints existentes de linking/eventos/estado visible, redireccion post-login segun rol tutor/staff/admin, rechazo con motivo inline, historial staff de vinculaciones, logout con revocacion persistida de sesion, feed de actividad transversal tutor/staff, listado de sesiones activas, revocacion individual de sesiones, cierre de otras sesiones desde la app, refresh de sesion con rotacion segura del refresh token, datos de revision reproducibles para revision local, perfil DEV conectado a PostgreSQL `hospital_familia_dev`, preferencias de notificacion tutor persistidas y centro de notificaciones in-app conectado.
- Pendiente principal para subir el avance: validacion visual end-to-end formal, checklist de salida beta ejecutado e integracion real con proveedor email/push si el alcance productivo lo exige.

## UI/UX Decisions

### [2026-05-05] Sistema de estilos para aplicación médica (estándar principal)

**Paleta base (alta accesibilidad - WCAG AAA):**
- Primary (acciones principales): #2563EB
- Primary Hover: #1E40AF
- Background (fondo general): #F5F7FA
- Surface (cards/contenedores): #FFFFFF

**Tipografía:**
- Texto principal: #111827
- Texto secundario: #6B7280

**Estados semánticos:**
- Success (estado correcto): #16A34A
- Error (crítico): #DC2626
- Warning (advertencia): #D97706

**Reglas de uso:**
- Mantener contraste mínimo 4.5:1 (ideal 7:1)
- No usar color como único indicador (acompañar con iconos/texto)
- Evitar saturación de colores
- Usar azul como color dominante (confianza clínica)
- Limitar uso de rojo solo a estados críticos

**Motivo:**
- Optimizado para legibilidad clínica
- Reduce fatiga visual en uso prolongado
- Alineado con estándares de sistemas hospitalarios
- Mejora UX en dashboards y monitoreo
