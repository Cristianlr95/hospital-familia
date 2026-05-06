# MEMORY

## Avance porcentual

- Avance estimado del proyecto Hospital - Familia: 69%.
- Delta de esta tarea: +2 puntos porcentuales por mejorar la UX de decision staff: rechazo inline con motivo obligatorio, validacion visual y cancelacion sin usar `prompt` del navegador.
- Base completada: auth JWT backend/frontend, roles, vinculacion tutor-paciente con aprobacion staff, dashboard tutor protegido, estado visible autorizado, calendario familiar de eventos proximos y regla de no exponer datos de paciente sin vinculo `APPROVED`.
- Base completada ampliada: panel staff conectado a endpoints existentes de linking/eventos, redireccion post-login segun rol tutor/staff/admin, rechazo con motivo inline y validacion de build/test frontend.
- Pendiente principal para subir el avance: vista staff de historial completo, notificaciones/historial, revocacion persistida de sesiones, endurecimiento productivo de Swagger/variables y validacion visual end-to-end.

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
