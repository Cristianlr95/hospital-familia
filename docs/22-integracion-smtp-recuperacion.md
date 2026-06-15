# Integracion SMTP recuperacion de contrasena

## Objetivo
Dejar preparado el flujo de recuperacion de contrasena para envio real por proveedor SMTP configurable.

## Alcance implementado

- Dependencia `spring-boot-starter-mail`.
- Servicio `PasswordResetDeliveryService`.
- Envio de email al solicitar recuperacion para usuarios existentes.
- Configuracion por variables:
  - `APP_PASSWORD_RESET_DELIVERY_ENABLED`,
  - `APP_PASSWORD_RESET_FROM`,
  - `APP_PASSWORD_RESET_URL`,
  - `SPRING_MAIL_HOST`,
  - `SPRING_MAIL_PORT`,
  - `SPRING_MAIL_USERNAME`,
  - `SPRING_MAIL_PASSWORD`,
  - `SPRING_MAIL_SMTP_AUTH`,
  - `SPRING_MAIL_SMTP_STARTTLS_ENABLE`.
- Respuesta publica sin enumeracion de emails.
- Modo dev mantiene token visible solo si `APP_PASSWORD_RESET_EXPOSE_TOKEN=true`.

## Criterio operativo

Para beta productiva se debe activar `APP_PASSWORD_RESET_DELIVERY_ENABLED=true`, desactivar exposicion de token y configurar credenciales SMTP reales.

Si el proveedor SMTP falla, el backend registra error en logs sin exponer al usuario si el correo existe o no.

## Validacion tecnica

- `hospital-familia-server`: `.\mvnw.cmd -q test`

## Pendiente para 100%

- Ejecutar prueba real con credenciales SMTP productivas o registrar decision formal de no usar email externo en beta.
- Completar validacion visual end-to-end y marcar checklist de salida beta desde el panel staff.
