# Changelog

**Desarrolladores:** Eduardo Moreno

Cambios notables de este repositorio de skills.

**Regla:** toda **alta, modificación o eliminación** de una skill (o de las convenciones/docs
del repo) se registra acá **en el mismo gesto** — ver `AGENTS.md` → Convenciones.

## Guía de uso

Para mantener el historial ordenado, utilizaremos las siguientes categorías en cada versión:

- **Added (Nuevo)**: Para nuevas funcionalidades.
- **Changed (Cambio)**: Para cambios en funcionalidades existentes.
- **Fixed (Corrección)**: Para corrección de errores (bugs).
- **Removed (Eliminado)**: Para funcionalidades eliminadas.
- **Maintenance (Mantenimiento)**: Tareas de infraestructura, refactorización y limpieza.

## Formato EXIGENTE de cada entrada

Cada cambio debe seguir ESTRICTAMENTE esta estructura:

````
- **{Categoría}**: {Título descriptivo del cambio}. {Descripción completa: QUÉ se hizo, POR QUÉ (opcional si es obvio), CÓMO se implementó. Mencionar nombres de archivos, funciones, endpoints, tablas, schemas, etc.} [{YYYY-MM-DD}]
  * **Files (Archivos)**: {lista de rutas de archivos modificados, con detalles de líneas si es relevante}. [{YYYY-MM-DD}]

Reglas:
1. La descripción debe ser AUTO-CONTENIDA: quien lea el changelog debe entender el cambio sin tener que abrir el código.
2. Si el cambio es complejo o tiene contexto importante, usar el formato expandido con subtabla explicativa:
   ```
   - **{Categoría}**: {Título}. {Resumen}. [{YYYY-MM-DD}]
     * **Problema**: {Qué fallaba o qué motivó el cambio}.
     * **Solución**: {Cómo se resolvió}.
     * **Files (Archivos)**: {rutas de archivos}. [{YYYY-MM-DD}]
   ```
3. La categoría va en NEGRITA, seguida de dos puntos y espacio, luego el título en NEGRITA.
4. La fecha `[{YYYY-MM-DD}]` va al FINAL de la línea de descripción principal (o al final de la última línea del bloque expandido).
5. La sección `Files (Archivos)`: opcional pero RECOMENDADA para cambios que tocan múltiples archivos.
6. Las entradas se ordenan cronológicamente inverso (más reciente primero) dentro de `## Unreleased`.
7. NO usar emojis. NO usar viñetas anidadas que no sean `*` para subtablas.
8. Si el cambio afecta frontend Y backend, se documenta en AMBOS changelogs con el mismo título y categoría, pero con enfoque en los archivos de cada lado.
````

## [Unreleased]

## [0.1.0] - 2026-10-08

- **Added**: **Repositorio inicial de skills con convenciones y documentación**. Se creó la base del proyecto autocontenido de agent skills (formato `SKILL.md`) organizado por categoría (`backend/`, `frontend/`, `devops/`): `AGENTS.md` con el contexto del agente (convenciones de escritura, verificación obligatoria con `rg`/`git` y catálogo vigente) y `README.md` con estructura, catálogo de disparadores, formato de skill y modo de instalación vía `skills.paths`. Se establece la regla de oro: skills genéricas y autocontenidas, sin referencias a proyectos o repos externos. [2026-10-08]
  * **Files (Archivos)**: `AGENTS.md`, `README.md`, `CHANGELOG.md`. [2026-10-08]
- **Added**: **8 skills de backend**. Carpeta `backend/` con: `arquitectura-backend` (estructura de carpetas, capas y dependencias unidireccionales), `autenticacion-jwt-backend` (middleware verifyToken, refresh rotation, almacenamiento seguro, hash de passwords), `base-datos-conexion-backend` (pool único, capa de acceso a datos, transacciones con release, migraciones versionadas), `config-env-backend` (env único validado al arranque con fail-fast), `errores-respuestas-backend` (error handler central + 404 catch-all), `logging-ops-backend` (logger único por proyecto, niveles, health check real), `testing-backend` (pirámide de tests, BD de test aislada, fixtures, mocking, CI) y `validacion-entrada-backend` (middleware Joi/Zod en el 100% de rutas con input). Cada una con frontmatter `name` = carpeta, secciones Cuándo usarla / Checklist (con Verificación obligatoria) / Anti-patrones con columna Detección / Solapamiento. [2026-10-08]
  * **Files (Archivos)**: `backend/*/SKILL.md` (8 carpetas: `arquitectura-backend`, `autenticacion-jwt-backend`, `base-datos-conexion-backend`, `config-env-backend`, `errores-respuestas-backend`, `logging-ops-backend`, `testing-backend`, `validacion-entrada-backend`). [2026-10-08]
- **Added**: **7 skills de frontend**. Carpeta `frontend/` con: `arquitectura-frontend` (SPA canónica, tabla capa→imports, cliente HTTP por feature), `auth-frontend` (paquete completo de auth con 8 flujos, guards declarativos, refresh/expiración), `config-env-frontend` (prefijo `VITE_`, `.env.example`, cero secretos en el bundle), `data-fetching-frontend` (cliente por feature + TanStack Query, query keys consistentes), `estados-toast-frontend` (4 estados de vista + toast global accesible ARIA), `formularios-frontend` (validación Zod sincronizada con el contrato, errores inline, trampas de fechas) y `ui-bloques-frontend` (paginación en URL, tokens/tema oscuro, formateo `Intl`, modal de info accesible). Sufijo `-frontend` unificado con la convención de la categoría backend. [2026-10-08]
  * **Files (Archivos)**: `frontend/*/SKILL.md` (7 carpetas: `arquitectura-frontend`, `auth-frontend`, `config-env-frontend`, `data-fetching-frontend`, `estados-toast-frontend`, `formularios-frontend`, `ui-bloques-frontend`). [2026-10-08]
- **Changed**: **Convención de nombres unificada con sufijo de categoría**. Todas las skills backend pasan a llevar sufijo `-backend` (ej. `config-env` → `config-env-backend`) para quedar simétricas con las `-frontend` y evitar colisiones al instalarlas planas en un directorio de skills compartido; en cada `SKILL.md` se actualizó el frontmatter `name` para igualar la carpeta y se corrigieron las referencias entre hermanas en las secciones `Solapamiento`. [2026-10-08]
  * **Problema**: al renombrar carpetas con el sufijo, el `name` del frontmatter y los enlaces entre skills quedaron apuntando a los nombres viejos (`name` ≠ carpeta = skill inválida y vínculos rotos).
  * **Solución**: normalización masiva por regex con lookaround `(?<![\w-])nombre(?!-)` para no tocar los hermanos con sufijo, sobre los 15 `SKILL.md` más `AGENTS.md` y `README.md`; verificación final: 15/15 `name` = carpeta, 0 nombres viejos.
  * **Files (Archivos)**: `backend/*/SKILL.md` (renombrados y referencias), `AGENTS.md` (árbol y catálogo), `README.md` (árbol y links del catálogo). [2026-10-08]
- **Changed**: **Skills genéricas y autocontenidas sin evidencia de proyectos**. Se eliminaron todas las referencias a proyectos/vault externos (`Reglas/`, `Patrones/`, `Brain/`, `Proyectos/`, notas de incidentes pasados) y los anti-patrones se reescribieron con columna **Detección** usando comandos `rg`/`git` reproducibles en cualquier repo; se quitó el `prefix` del frontmatter y se unificó la estructura de secciones (Título / Regla / Cuándo usarla / Checklist con Verificación obligatoria / Anti-patrones / Solapamiento) con límite ≤150 líneas. [2026-10-08]
  * **Files (Archivos)**: los 15 `SKILL.md` de `backend/` y `frontend/`. [2026-10-08]
