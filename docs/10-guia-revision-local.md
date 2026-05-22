# Guia de revision local

## Objetivo
Levantar `Hospital - Familia` con datos demo reproducibles para revisar frontend y backend sin crear usuarios manualmente cada vez que se reinicia H2.

## Backend

Desde `hospital-familia-server`:

```powershell
$env:APP_DEMO_SEED_ENABLED='true'
.\mvnw.cmd spring-boot:run -Dspring-boot.run.profiles=test
```

El seed demo esta apagado por defecto. Solo se activa al usar `APP_DEMO_SEED_ENABLED=true` o `app.demo.seed-enabled=true`.

## Frontend

Desde `hospital-familia-app`:

```powershell
npm start -- --port 4200
```

Abrir:

```text
http://localhost:4200
```

## Credenciales de revision

| Perfil | Email | Password | Uso |
| --- | --- | --- | --- |
| Tutor | `familia.rivera@hospitalfamilia.local` | `password123` | Dashboard familiar, paciente vinculado, estado, eventos, actividad, sesiones y notificaciones |
| Staff | `enfermeria.central@hospitalfamilia.local` | `password123` | Dashboard staff, feed transversal, historial y gestion operativa |

## Datos incluidos

- Paciente: `Maria Gonzalez Rivera`.
- Codigo de vinculacion: `HF-REV-001`.
- Vinculacion tutor-paciente aprobada.
- Snapshot de estado: estable, medicina interna, habitacion demo.
- Eventos proximos: examen de control y visita familiar autorizada.

## Criterio de revision

- Tutor puede iniciar sesion y ver paciente autorizado con eventos.
- Staff puede iniciar sesion y revisar actividad/gestion sin crear datos manuales.
- La app mantiene recuperacion automatica de sesion ante `401` usando refresh token rotado.
