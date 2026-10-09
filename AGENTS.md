# AGENTS.md — Contexto del repositorio de skills

## Qué es este proyecto

Colección **independiente** de *agent skills* (formato `SKILL.md`, bajo `skills/<categoría>/`)
y de *agentes* en formato portable (markdown, bajo `agents/`), pensada para instalarse en
cualquier sistema de agentes (opencode, Claude Code, Cursor, Qoder, etc.). Es un proyecto
**separado de todo lo demás**: no depende del vault OpenBrainCode, ni de otros repos, ni de
sus notas/reglas/proyectos.

**Consecuencia obligatoria:** las skills y los agentes de este repo son **autocontenidos y
genéricos**. Nunca referencien proyectos externos, vaults, `Brain/`, `Reglas/`, otras skills
ajenas ni rutas de otros repositorios. Las únicas relaciones válidas son con las **skills y
agentes hermanos de este repo**.

## Estructura

```
SKILL/
├── AGENTS.md              # este archivo (contexto para el agente)
├── README.md              # documentación del repo
├── CHANGELOG.md           # historial de cambios (obligatorio actualizar)
├── skills/                # contenedor canónico: el ecosistema (npx skills) descubre SKILL.md acá
│   ├── backend/           # skills de backend (8)
│   │   ├── arquitectura-backend/SKILL.md
│   │   ├── autenticacion-jwt-backend/SKILL.md
│   │   ├── base-datos-conexion-backend/SKILL.md
│   │   ├── config-env-backend/SKILL.md
│   │   ├── errores-respuestas-backend/SKILL.md
│   │   ├── logging-ops-backend/SKILL.md
│   │   ├── testing-backend/SKILL.md
│   │   └── validacion-entrada-backend/SKILL.md
│   ├── frontend/          # skills de frontend (7)
│   │   ├── arquitectura-frontend/SKILL.md
│   │   ├── auth-frontend/SKILL.md
│   │   ├── config-env-frontend/SKILL.md
│   │   ├── data-fetching-frontend/SKILL.md
│   │   ├── estados-toast-frontend/SKILL.md
│   │   ├── formularios-frontend/SKILL.md
│   │   └── ui-bloques-frontend/SKILL.md
│   ├── devops/            # reservada (vacía)
│   └── global/            # skills globales: aplican a backend y frontend (4)
│       ├── changelog-global/SKILL.md
│       ├── crear-skill-global/SKILL.md
│       ├── generador-estimaciones-global/SKILL.md
│       └── tasks-vscode-global/SKILL.md
└── agents/                # agentes portables: formato neutral (name + description + prompt)
    ├── backend.md
    └── frontend.md
```

## Convenciones al crear o editar una skill

> Para crear o editar una skill, seguir
> [`skills/global/crear-skill-global`](skills/global/crear-skill-global/SKILL.md)
> (intención → borrador → iteración → verificación); abajo está el resumen normativo.

1. **Formato:** carpeta kebab-case que contiene `SKILL.md`. Frontmatter con `name`
   (**igual al nombre de la carpeta**) y `description`.
2. **Description = qué + cuándo + triggers.** Imperativa, en tercera persona, con las palabras
   exactas que el usuario tipea, terminando en `Triggers: "palabra", "otra palabra"`.
   Ejemplo: `description: 'Use when ... Triggers: "agregar variable de entorno", "JWT_SECRET".'`
   La description **solo dispara** (*Use when* + triggers, "pushy"): **nunca resume el
   flujo/workflow** de la skill — si la description describe los pasos, el agente cree que ya
   la conoce y saltea el cuerpo entero (description-trap).
   **Valor entrecomillado con comillas simples de YAML** (`description: '...':`): un `:`
   seguido de espacio dentro de un escalar sin comillas (el de `Triggers:`) rompe el
   frontmatter y el ecosistema (`npx skills`) descarta la skill por inválida.
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
6. **Comandos concretos, portables y estables:** `rg`/`git` copy-pasteables. Nada de rutas de
   proyectos concretos (cambian); sí rutas convencionales de ejemplo (`src/config`, `src/middlewares`).
   - **Bash + PowerShell:** los comandos deben correr en ambos shells. Patrones de `rg` que
     matchean una comilla doble se escriben con `\x22` y entre comillas simples
     (`rg 'role=\x22dialog\x22'`): el `"` literal en el argv rompe el paso de argumentos
     nativo de PowerShell 5.1 (lo mangla silenciosamente, sin error). `[\x27\x22]` empareja
     ambos tipos de comilla; `-e` repetido en vez de `|` alternativo cuando conviene;
     pathspecs de git (`git ls-files "*.log"`) en vez de pipiar a `rg`; `rg --files` en vez
     de `find`; `rg -c '^'` en vez de `wc -l`; `sort -u` está permitido. **Prohibidos**
     `find`, `wc`, `tail`, `ls -a`, `basename`, `Measure-Object` y los lookarounds
     (`(?!...)`: el engine default de rg no los soporta — encadenar dos `rg` con `| rg -v`).
   - **Escapes en tablas:** dentro de una celda de tabla, `\|` es escape de markdown (evita
     partir la tabla) y se renderiza como `|`: **al ejecutar un comando copiado de una celda,
     leer `\|` como `|`**. En fences de código va `|` crudo (un `\|` ahí es pipe literal).
7. **Omitir lo que el agente ya sabe** (qué es HTTP, qué es Express); incluir solo lo no
   obvio del dominio: convenciones, trampas y criterios de verificación.
   - Explicar el **porqué** de cada regla; un muro de `MAYÚS`/`PROHIBIDO` sin razón se
     cumple de letra y se viola en espíritu.
   - Los **gotchas** van en el cuerpo de la skill (si el agente no los lee antes de caer en
     la trampa, no existen); no se externalizan.
   - Cuando el output tiene forma fija, incluir la **plantilla concreta** (ejemplo real),
     no una prosa que la describa.
8. **Changelog obligatorio:** toda **alta, modificación o eliminación** de una skill — o cambio
   de convenciones/docs del repo — se registra en [`CHANGELOG.md`](CHANGELOG.md) **en el mismo
   gesto**, siguiendo su **Guía de uso** y su **Formato EXIGENTE** (categorías `Added` /
   `Changed` / `Fixed` / `Removed` / `Maintenance`; categoría y título en negrita, fecha
   `[{YYYY-MM-DD}]` al final de la línea, sección `Files (Archivos)` recomendada). La sección
   activa es `## [Unreleased]`; al liberar se crea la entrada con fecha.

## Convenciones al crear o editar un agente

Los agentes viven en `agents/<nombre>.md` en **formato neutral portable**: un solo archivo
markdown cuyo body se usa como system prompt en cualquier sistema de agentes.

1. **Formato:** archivo `agents/<nombre>.md` (nombre kebab-case = `name` del frontmatter).
   Frontmatter mínimo universal: `name` y `description`. **Nunca** `prompt:` en el
   frontmatter — el body del archivo *es* el prompt. Sin campos vendor-specific
   (`mode`, `tools`, `model`, `mcpServers`, `skills`): cada vendor los agrega en su
   destino de instalación (tabla de mapeo en el README), así el archivo canonico sirve
   tal cual en todos.
2. **Description = qué hace + cuándo delegarla.** El agente primario decide la
   delegación leyendo la description; debe ser "pushy" (listar los contextos aunque el
   usuario no nombre al agente) y usar los triggers del idioma del equipo.
3. **Skills hermanas, sin duplicarlas.** El prompt referencia las skills de
   `skills/backend/` o `skills/frontend/` **por nombre** para aplicarlas según el tema;
   jamás copia su contenido (regla anti-duplicación entre hermanos).
4. **Verificación obligatoria en el prompt.** Todo agente de implementación exige correr
   lint + typecheck + tests antes de declarar terminado, y define un contrato de salida
   (qué se hizo, archivos tocados, resultados de los comandos).
5. **Genérico y autocontenido:** mismas reglas que las skills — 0 menciones a proyectos,
   vault ni rutas ajenas.
6. **Tamaño:** ≤ 150 líneas por agente.
7. **Changelog obligatorio:** toda alta o modificación de un agente se registra en
   [`CHANGELOG.md`](CHANGELOG.md) en el mismo gesto, con el mismo formato exigente.

## Verificación antes de dar por terminada una edición

```bash
# 1) sin referencias externas ni proyectos
rg -i "Reglas/|Patrones/|Brain/|Proyectos/|portafolio|OpenBrain" skills/<categoria>/<carpeta>
# 2) name = carpeta
rg "^name:" skills/<categoria>/<carpeta>/SKILL.md
# 3) tamaño
rg -c '^' skills/<categoria>/<carpeta>/SKILL.md    # objetivo ≤ 150
# 4) changelog registra el cambio
rg "<nombre-de-la-skill>" CHANGELOG.md
# 5) agente: name y description presentes, sin prompt: en el frontmatter
rg -e '^name:' -e '^description:' agents/<nombre>.md
rg '^prompt:' agents/<nombre>.md                  # debe fallar (sin match)
# 6) agente: tamaño y changelog
rg -c '^' agents/<nombre>.md                      # objetivo ≤ 150
rg "<nombre-del-agente>" CHANGELOG.md
# 7) el ecosistema descubre la skill (desde la raíz; debe aparecer en la lista)
npx skills add . -l
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
| `crear-skill-global` | global | Alta/edición de skills: intención, borrador con estructura fija, description anti-trap, suite de verificación, iteración |
| `generador-estimaciones-global` | global | Docs HTML por rol (backend/frontend/QA/resumen) con horas prellenadas, subtotales, total y proyección en semanas |
| `tasks-vscode-global` | global | `.vscode/tasks.json` solo de servicios dev: task por servicio (`powershell -NoExit`, `isBackground`, panel propio) y agregadores grupo/ALL con `dependsOn` |

### agents/

Agentes portables en formato neutral (el mapeo de frontmatter/paths por vendor está en el README).

| Agente | Para qué |
|---|---|
| `backend` | Implementa servidor por capas (Express/Node), contract-first + TDD; aplica las skills `backend/*` y no toca UI |
| `frontend` | Implementa la SPA (React/Vite/TanStack), a11y + Core Web Vitals; aplica las skills `frontend/*` y no toca servidor |

## Qué NO hacer

- No crear notas vacías ni skills sin `description` con triggers.
- No agregar enlaces o rutas hacia fuera de este repo.
- No duplicar contenido entre hermanas: si dos skills se pisan, una declara el solapamiento
  y la otra ejecuta.
- No renombrar `name` sin renombrar la carpeta (deben coincidir).
- No poner campos vendor-specific (`mode`, `tools`, `model`, `mcpServers`) en los agentes
  de `agents/`: rompen la portabilidad y pertenecen al destino de instalación.
