# Validacion entorno DEV

## Objetivo
Confirmar que el backend de `Hospital - Familia` usa PostgreSQL local `hospital_familia_dev` durante desarrollo y no la base H2 usada por pruebas automatizadas.

## Resultado verificado

Validacion ejecutada el 2026-05-20:

```text
Database: jdbc:postgresql://localhost:5432/hospital_familia_dev (PostgreSQL 18.1)
Actuator health: UP
```

## Backend DEV

Desde `hospital-familia-server`:

```powershell
.\mvnw.cmd spring-boot:run -Dspring-boot.run.profiles=dev
```

El perfil `dev` resuelve por defecto:

```text
SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/hospital_familia_dev
SPRING_DATASOURCE_USERNAME=hospital_familia_dev
SPRING_DATASOURCE_PASSWORD=hospital_familia_dev
```

## Comprobaciones

Verificar que el backend responde:

```powershell
Invoke-RestMethod http://localhost:8080/actuator/health
```

Verificar que el log contiene:

```text
Database: jdbc:postgresql://localhost:5432/hospital_familia_dev
```

## Diferencia con tests

- Desarrollo: perfil `dev`, PostgreSQL local `hospital_familia_dev`.
- Tests: perfil `test`, H2 en memoria `hospital_familia_test`.
- Revision demo: puede usar `APP_DEMO_SEED_ENABLED=true` para cargar credenciales y datos de prueba reproducibles.
