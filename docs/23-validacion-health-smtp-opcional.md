# Validacion health SMTP opcional

## Objetivo

Corregir la lectura de salud del backend en desarrollo para que SMTP opcional no marque la API como `DOWN` cuando no existe proveedor de correo configurado.

## Problema detectado

Al agregar `spring-boot-starter-mail`, Spring Boot registra automaticamente el indicador `mail` dentro de `/actuator/health`. En desarrollo local el envio de recuperacion de contrasena esta apagado por defecto, pero el indicador intentaba conectar a `localhost:1025` y dejaba el backend en estado `DOWN`.

La API y PostgreSQL funcionaban correctamente, por lo que el estado `DOWN` era un falso negativo operativo.

## Correccion aplicada

- Se agrega `MANAGEMENT_HEALTH_MAIL_ENABLED=false` por defecto.
- Se documenta `MANAGEMENT_HEALTH_MAIL_ENABLED=true` solo para ambientes con SMTP real o MailHog disponible.
- Se mantiene `APP_PASSWORD_RESET_DELIVERY_ENABLED=false` por defecto en desarrollo.

## Validacion ejecutada

- Backend conectado a `jdbc:postgresql://localhost:5432/hospital_familia_dev`.
- Flyway validado hasta version `v11`.
- `GET /actuator/health` respondio `UP`.
- Componente `db` respondio `UP` con PostgreSQL.
- Login tutor valido por API con `familia.rivera@hospitalfamilia.local`.
- Login staff valido por API con `enfermeria.central@hospitalfamilia.local`.
- Suite backend ejecutada con `.\mvnw.cmd -q test`.

## Pendiente para 100%

- Ejecutar validacion visual end-to-end en navegador interactivo.
- Validar SMTP con credenciales reales o declarar formalmente que correo externo queda fuera del alcance beta.
