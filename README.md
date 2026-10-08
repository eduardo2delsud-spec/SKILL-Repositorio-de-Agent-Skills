# SKILL — Repositorio de Agent Skills

Colección independiente de **agent skills** (formato `SKILL.md`) organizadas por categoría.
Proyecto autocontenido: no depende de ningún otro repositorio.

## Estructura

```
SKILL/
├── AGENTS.md      # contexto y convenciones para el agente que trabaja acá
├── README.md      # este archivo
├── CHANGELOG.md   # historial de cambios (se actualiza en cada alta/modificación/baja)
├── backend/       # skills de backend (8 activas)
├── frontend/      # skills de frontend (7 activas)
└── devops/        # reservada
```

## Catálogo

### backend/

| Skill | Dispara con | Qué resuelve |
|---|---|---|
| [`arquitectura-backend`](backend/arquitectura-backend/SKILL.md) | "estructura del proyecto", "dónde va este archivo", "separación de capas" | Árbol canónico, tabla de dependencias por capa, estructura única (dominio vs capa), verificación de imports violados |
| [`autenticacion-jwt-backend`](backend/autenticacion-jwt-backend/SKILL.md) | "JWT", "login", "token", "refresh token", "proteger ruta" | Middleware verifyToken, tokens cortos, refresh rotation, almacenamiento seguro, hash de passwords |
| [`base-datos-conexion-backend`](backend/base-datos-conexion-backend/SKILL.md) | "conexión a la base", "pool", "migraciones", "transacción" | Pool único, capa de acceso a datos, transacciones con release, migraciones versionadas |
| [`config-env-backend`](backend/config-env-backend/SKILL.md) | "agregar variable de entorno", "JWT_SECRET", ".env.example" | Módulo único de config con validación fail-fast, `.env.example` como fuente de truth, cero secretos |
| [`errores-respuestas-backend`](backend/errores-respuestas-backend/SKILL.md) | "error 500", "error handler", "404", "try catch" | Handler central + 404 catch-all, controllers que lanzan y no responden, shape única de error |
| [`logging-ops-backend`](backend/logging-ops-backend/SKILL.md) | "logger", "no loguea", "console.log en prod", "health check" | Un logger por proyecto, niveles correctos, health check real, logs fuera del repo |
| [`testing-backend`](backend/testing-backend/SKILL.md) | "test", "jest", "vitest", "supertest", "mock", "cobertura" | Pirámide de tests, BD de test aislada, fixtures centralizados, mocking correcto, CI |
| [`validacion-entrada-backend`](backend/validacion-entrada-backend/SKILL.md) | "validar body", "Joi", "Zod", "400 bad request" | Middleware de validación en el 100% de rutas con input, errores 400 con details |

### frontend/

| Skill | Dispara con | Qué resuelve |
|---|---|---|
| [`arquitectura-frontend`](frontend/arquitectura-frontend/SKILL.md) | "estructura del frontend", "dónde va este archivo", "por feature" | SPA canónica, tabla de dependencias por capa, cliente HTTP por feature |
| [`auth-frontend`](frontend/auth-frontend/SKILL.md) | "login", "guard de ruta", "sesión", "recuperar contraseña" | Paquete completo de auth: 8 flujos, guards declarativos, refresh/expiración |
| [`config-env-frontend`](frontend/config-env-frontend/SKILL.md) | "VITE_", ".env.example", "secretos en el front" | Variables públicas con prefijo y cero secretos en el bundle |
| [`data-fetching-frontend`](frontend/data-fetching-frontend/SKILL.md) | "tanstack query", "fetch", "cache", "cliente http" | Cliente por feature, query keys consistentes, un solo data layer |
| [`estados-toast-frontend`](frontend/estados-toast-frontend/SKILL.md) | "loading", "empty state", "toast", "reintentar" | 4 estados de vista + toast global accesible (ARIA) |
| [`formularios-frontend`](frontend/formularios-frontend/SKILL.md) | "formularios", "validación", "zod", "errores inline" | Schema Zod sincronizado con el contrato, errores inline, trampas de fechas |
| [`ui-bloques-frontend`](frontend/ui-bloques-frontend/SKILL.md) | "paginación", "design tokens", "tema oscuro", "changelog" | Paginación en URL, tokens/tema, formateo Intl, modal de info accesible |

## Formato de una skill

Cada skill es una carpeta kebab-case con un `SKILL.md`:

```markdown
---
name: nombre-de-la-skill          # = nombre de la carpeta
description: Qué hace. Use when ... Triggers: "disparo 1", "disparo 2".
---

# Título — frase de regla
**Regla:** principio rector en 1-2 líneas.

## Cuándo usarla
## Checklist de ejecución        # último paso: Verificación (obligatoria)
## Anti-patrones (cómo detectarlos)
## Solapamiento                   # solo skills de este repo
```

Regla de oro: **skills genéricas y autocontenidas** — nada de referencias a proyectos o
repositorios externos. Detalle completo en [`AGENTS.md`](AGENTS.md).

## Uso

Las skills se cargan bajo demanda a través de la configuración de skills de opencode
(`skills.paths` en `opencode.json`, o el directorio de skills de tu configuración:
`~/.config/opencode/skills/` o `.opencode/skills/` del proyecto).

Para usar este repo: apuntar `skills.paths` a `SKILL/backend` (o a `SKILL/` si en el futuro
las categorías llevan `SKILL.md` propias), o copiar las carpetas individuales que necesites.

## Agregar una skill

1. Crear `<categoría>/<nombre-kebab-case>/SKILL.md`.
2. Seguir el formato y las convenciones de [`AGENTS.md`](AGENTS.md) (frontmatter, secciones,
   genérica, ≤ 150 líneas).
3. Correr la verificación de `AGENTS.md` (sin referencias externas, `name` = carpeta, tamaño).
4. Agregar la fila al catálogo de este README y al de `AGENTS.md`.
5. Registrar el cambio en [`CHANGELOG.md`](CHANGELOG.md) siguiendo su **Formato EXIGENTE**
   (`Added` / `Changed` / `Fixed` / `Removed` / `Maintenance`, en `## [Unreleased]`).

> **Regla:** agregar, modificar o eliminar una skill → actualizar también el changelog,
> en el mismo gesto (detalle en [`AGENTS.md`](AGENTS.md) → Convenciones).
