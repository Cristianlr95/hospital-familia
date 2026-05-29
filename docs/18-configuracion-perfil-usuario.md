# Configuracion basica de perfil de usuario

## Objetivo

Permitir que tutores y staff corrijan datos personales basicos durante la revision beta sin abrir funciones administrativas sensibles.

## Alcance

- El usuario autenticado puede editar:
  - nombre,
  - apellido,
  - telefono de contacto.
- El usuario no puede cambiar desde este flujo:
  - email,
  - roles,
  - contrasena.
- Los dashboards tutor y staff muestran un panel de perfil conectado.
- El frontend sincroniza el usuario actualizado en almacenamiento local.

## Endpoint

- `PUT /api/auth/profile`

## Criterios de aceptacion

- Requiere sesion autenticada.
- Valida nombre y apellido obligatorios.
- Recorta espacios en valores guardados.
- Limpia telefono vacio como `null`.
- Devuelve `UserDto` actualizado sin exponer hash de contrasena.
- La UI muestra confirmacion de guardado o error.

## Validacion

- Backend: `.\mvnw.cmd -q test`
- Frontend: `npm run build`
