# AGENTS.md — Contexto del repositorio de skills

## Qué es este proyecto

Colección **independiente** de *agent skills* (formato `SKILL.md`) organizada por categoría
(`backend/`, `frontend/`, `devops/`, `global/`). Es un proyecto **separado de todo lo demás**: no depende
del vault OpenBrainCode, ni de otros repos, ni de sus notas/reglas/proyectos.

**Consecuencia obligatoria:** las skills de este repo son **autocontenidas y genéricas**.
Nunca referencien proyectos externos, vaults, `Brain/`, `Reglas/`, otras skills ajenas ni
rutas de otros repositorios. Las únicas relaciones válidas son con las **skills hermanas de
este repo**.

## Estructura

```
SKILL/
├── AGENTS.md              # este archivo (contexto para el agente)
├── README.md              # documentación del repo
├── CHANGELOG.md           # historial de cambios (obligatorio actualizar)
├── backend/               # skills de backend (8)
│   ├── arquitectura-backend/SKILL.md
│   ├── autenticacion-jwt-backend/SKILL.md
│   ├── base-datos-conexion-backend/SKILL.md
│   ├── config-env-backend/SKILL.md
│   ├── errores-respuestas-backend/SKILL.md
│   ├── logging-ops-backend/SKILL.md
│   ├── testing-backend/SKILL.md
│   └── validacion-entrada-backend/SKILL.md
├── frontend/              # skills de frontend (7)
│   ├── arquitectura-frontend/SKILL.md
│   ├── auth-frontend/SKILL.md
│   ├── config-env-frontend/SKILL.md
│   ├── data-fetching-frontend/SKILL.md
│   ├── estados-toast-frontend/SKILL.md
│   ├── formularios-frontend/SKILL.md
│   └── ui-bloques-frontend/SKILL.md
├── devops/                # reservada (vacía)
└── global/                # skills globales: aplican a backend y frontend (2)
    ├── changelog-global/SKILL.md
    └── generador-estimaciones-global/SKILL.md
```

## Convenciones al crear o editar una skill

1. **Formato:** carpeta kebab-case que contiene `SKILL.md`. Frontmatter con `name`
   (**igual al nombre de la carpeta**) y `description`.
2. **Description = qué + cuándo + triggers.** Imperativa, en tercera persona, con las palabras
   exactas que el usuario tipea, terminando en `Triggers: "palabra", "otra palabra"`.
   Ejemplo: `description: ... Use when ... Triggers: "agregar variable de entorno", "JWT_SECRET".`
3. **Genérica:** 0 menciones a proyectos, vault, incidentes pasados ni rutas ajenas.
   Los anti-patrones van con columna **Detección** (comando `rg`/`git` reproducible), nunca
   con evidencia de un proyecto concreto.
4. **Estructura de secciones** (orden fijo):
   1. `# Título — frase de regla`
   2. `**Regla:**` en 1-2 líneas (el principio que aplica la skill)
   3. `## Cuándo usarla` (bullets de disparo)
   4. `## Checklist de ejecución` (pasos numerados; **el último paso es siempre
      "Verificación (obligatoria)" con comandos exactos**)
   5. `## Anti-patrones (cómo detectarlos)` (tabla Anti-patrón | Detección)
   6. `## Solapamiento` (solo skills de ESTE repo: hermanas con las que se cruza)
5. **Tamaño:** ≤ 150 líneas por skill (máximo absoluto 500) y < 5k tokens. Si algo no cabe,
   separamos en otra skill; no se usa `references/` a esta escala.
6. **Comandos concretos y estables:** `rg`/`git` copy-pasteables. Nada de rutas de proyectos
   concretos (cambian); sí rutas convencionales de ejemplo (`src/config`, `src/middlewares`).
7. **Omitir lo que el agente ya sabe** (qué es HTTP, qué es Express); incluir solo lo no
   obvio del dominio: convenciones, trampas y criterios de verificación.
8. **Changelog obligatorio:** toda **alta, modificación o eliminación** de una skill — o cambio
   de convenciones/docs del repo — se registra en [`CHANGELOG.md`](CHANGELOG.md) **en el mismo
   gesto**, siguiendo su **Guía de uso** y su **Formato EXIGENTE** (categorías `Added` /
   `Changed` / `Fixed` / `Removed` / `Maintenance`; categoría y título en negrita, fecha
   `[{YYYY-MM-DD}]` al final de la línea, sección `Files (Archivos)` recomendada). La sección
   activa es `## [Unreleased]`; al liberar se crea la entrada con fecha.

## Verificación antes de dar por terminada una edición

```bash
# 1) sin referencias externas ni proyectos
rg -i "Reglas/|Patrones/|Brain/|Proyectos/|portafolio|OpenBrain" <carpeta-de-la-skill>
# 2) name = carpeta
rg "^name:" <carpeta>/SKILL.md
# 3) tamaño
wc -l <carpeta>/SKILL.md    # objetivo ≤ 150
# 4) changelog registra el cambio
rg "<nombre-de-la-skill>" CHANGELOG.md
```

## Catálogo vigente

| Skill | Categoría | Para qué |
|---|---|---|
| `arquitectura-backend` | backend | Estructura de carpetas, capas y regla de dependencias unidireccionales |
| `autenticacion-jwt-backend` | backend | Middleware JWT, refresh rotation, almacenamiento seguro, hash de passwords |
| `base-datos-conexion-backend` | backend | Pool único, capa de acceso a datos, transacciones, migraciones versionadas |
| `config-env-backend` | backend | Env único validado al arranque con fail-fast; `.env.example` como truth |
| `errores-respuestas-backend` | backend | Error handler central + 404 catch-all; controllers que lanzan, no responden |
| `logging-ops-backend` | backend | Logger único, niveles, health check real, higiene de logs en el repo |
| `testing-backend` | backend | Pirámide de tests, BD aislada, fixtures, mocking correcto, CI |
| `validacion-entrada-backend` | backend | Middleware Joi/Zod en el 100% de rutas con input |

### frontend/

| Skill | Categoría | Para qué |
|---|---|---|
| `arquitectura-frontend` | frontend | Estructura SPA canónica, capas/imports, cliente HTTP por feature |
| `auth-frontend` | frontend | Paquete completo de auth: 8 flujos, guards, refresh/expiración |
| `config-env-frontend` | frontend | Prefijo `VITE_`, `.env.example`, cero secretos en el bundle |
| `data-fetching-frontend` | frontend | Cliente por feature + TanStack Query, un solo data layer |
| `estados-toast-frontend` | frontend | 4 estados de vista + toast global accesible |
| `formularios-frontend` | frontend | Validación Zod sincronizada con el contrato, errores inline |
| `ui-bloques-frontend` | frontend | Paginación en URL, tokens/tema, Intl, modal changelog+manual |

### global/

Skills transversales: aplican por igual a backend y frontend.

| Skill | Categoría | Para qué |
|---|---|---|
| `changelog-global` | global | Formato exigente del CHANGELOG: 5 categorías, entradas auto-contenidas, fecha al final, Unreleased |
| `generador-estimaciones-global` | global | Docs HTML por rol (backend/frontend/QA/resumen) con horas prellenadas, subtotales, total y proyección en semanas |

## Qué NO hacer

- No crear notas vacías ni skills sin `description` con triggers.
- No agregar enlaces o rutas hacia fuera de este repo.
- No duplicar contenido entre hermanas: si dos skills se pisan, una declara el solapamiento
  y la otra ejecuta.
- No renombrar `name` sin renombrar la carpeta (deben coincidir).
