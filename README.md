# SKILL — Repositorio de Agent Skills

Colección independiente de **agent skills** (formato `SKILL.md`) organizadas por categoría.
Proyecto autocontenido: no depende de ningún otro repositorio.

## Estructura

```
SKILL/
├── AGENTS.md      # contexto y convenciones para el agente que trabaja acá
├── README.md      # este archivo
├── backend/       # skills de backend (5 activas)
├── frontend/      # reservada
└── devops/        # reservada
```

## Catálogo

### backend/

| Skill | Dispara con | Qué resuelve |
|---|---|---|
| [`arquitectura-backend`](backend/arquitectura-backend/SKILL.md) | "estructura del proyecto", "dónde va este archivo", "separación de capas" | Árbol canónico, tabla de dependencias por capa, estructura única (dominio vs capa), verificación de imports violados |
| [`config-env`](backend/config-env/SKILL.md) | "agregar variable de entorno", "JWT_SECRET", ".env.example" | Módulo único de config con validación fail-fast, `.env.example` como fuente de truth, cero secretos |
| [`errores-respuestas`](backend/errores-respuestas/SKILL.md) | "error 500", "error handler", "404", "try catch" | Handler central + 404 catch-all, controllers que lanzan y no responden, shape única de error |
| [`logging-ops`](backend/logging-ops/SKILL.md) | "logger", "no loguea", "console.log en prod", "health check" | Un logger por proyecto, niveles correctos, health check real, logs fuera del repo |
| [`validacion-entrada`](backend/validacion-entrada/SKILL.md) | "validar body", "Joi", "Zod", "400 bad request" | Middleware de validación en el 100% de rutas con input, errores 400 con details |

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
