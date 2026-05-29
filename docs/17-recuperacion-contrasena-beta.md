# Recuperacion de contrasena beta

## Objetivo

Cerrar el flujo MVP de recuperacion de contrasena para tutores y staff sin depender todavia de un proveedor de email.

## Alcance

- El usuario solicita recuperacion con su correo.
- El backend genera un token temporal, lo guarda hasheado y con expiracion.
- En ambiente DEV/test se expone el token en la respuesta para facilitar revision local.
- En ambiente productivo el token no se expone por API; queda listo para integracion futura con email.
- El usuario confirma token y nueva contrasena desde `/auth/reset-password`.
- Al cambiar la contrasena se revocan sesiones activas anteriores.

## Endpoints

- `POST /api/auth/password-reset/request`
- `POST /api/auth/password-reset/confirm`

## Criterios de aceptacion

- Solicitar recuperacion responde OK aunque el correo no exista, evitando enumeracion de usuarios.
- El token no se guarda en texto plano.
- El token usado no puede reutilizarse.
- La contrasena anterior deja de servir.
- La nueva contrasena permite login.
- Las sesiones activas previas quedan revocadas.
- El login tiene enlace visible a recuperacion.

## Validacion

- Backend: `.\mvnw.cmd -q test`
- Frontend: `npm run build`
