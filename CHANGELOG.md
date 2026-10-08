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

- **Added**: **Skill `generador-estimaciones-global`**. Nueva skill en la categoría `global/` que genera documentos HTML autocontenidos de estimación de esfuerzo por rol (`estimacion_backend.html`, `estimacion_frontend.html`, `estimacion_qa.html` y `estimacion_resumen.html`): encuesta inicial (proyecto, stack, equipo, módulos con tareas por rol), horas **prellenadas con la propuesta del agente** para que el usuario ajuste en el HTML o pidiendo regenerar, estructura portada → fases (infra/scaffolding/módulos) → tabla Tarea × Horas → resumen con subtotales, total y proyección en semanas (total ÷ 8 ÷ 5), colores fijos por rol (`#1a5276`/`#196f3d`/`#7d3c98`/`#2c3e50`), CSS inline con `@page` A4 listo para imprimir a PDF desde el navegador, verificación por `rg` (cero CSS externo, subtotales, semanas, cero modelos de IA en el documento). Se agregó al árbol y catálogo de `AGENTS.md` y `README.md`, y a la nota "Global (ambos lados)". [2026-10-08]
  * **Files (Archivos)**: `global/generador-estimaciones-global/SKILL.md`, `AGENTS.md` (árbol y catálogo `### global/`), `README.md` (árbol, catálogo, nota de orden de lectura), `CHANGELOG.md`. [2026-10-08]
- **Added**: **Categoría `global/` con la skill `changelog-global`**. Nueva carpeta `global/` para skills transversales que aplican por igual a backend y frontend, empezando por `changelog-global`: formato exigente del CHANGELOG de un proyecto (5 categorías `Added`/`Changed`/`Fixed`/`Removed`/`Maintenance`, plantilla simple y expandido con `Problema`/`Solución`/`Files`, fecha al final de la línea, orden inverso, sin emojis, doble registro cuando el cambio cruza front y back), con checklist de verificación por `rg` y tabla de anti-patrones detectables (incluye código mergeado sin entrada vía `git diff --name-only main...HEAD`). Se registró la categoría en los árboles y catálogos de `AGENTS.md` y `README.md`, y la nota "Global (ambos lados)" en el orden de lectura. [2026-10-08]
  * **Files (Archivos)**: `global/changelog-global/SKILL.md`, `AGENTS.md` (frase de categorías, árbol, catálogo `### global/`), `README.md` (árbol, catálogo `### global/`, nota de orden de lectura), `CHANGELOG.md`. [2026-10-08]
- **Changed**: **Cierre de la cadena de dependencias entre skills frontend**. Se completaron los eslabones faltantes del flujo `arquitectura → config → auth → data-fetching → formularios → estados-toast → ui-bloques` en las secciones `## Solapamiento`: `arquitectura-frontend` ahora incluye `config-env-frontend` en su lista de hermanas base y aclara que allí solo vive el módulo de config (punto de entrada); `auth-frontend` referencia `config-env-frontend` (la base de endpoints de auth sale de `VITE_API_URL`, los tokens no son env) y `data-fetching-frontend` referencia `config-env-frontend` (`VITE_API_URL` se lee una sola vez en config y se inyecta al cliente, sin `import.meta.env` directo en componentes), cerrando la simetría que faltaba con la arista inversa que ya tenía esa hermana. [2026-10-08]
  * **Files (Archivos)**: `frontend/arquitectura-frontend/SKILL.md`, `frontend/auth-frontend/SKILL.md`, `frontend/data-fetching-frontend/SKILL.md`. [2026-10-08]
- **Changed**: **Cierre de la cadena de dependencias entre skills backend**. Se completaron los eslabones faltantes del flujo `arquitectura → config → auth → BD → validación → errores → logging → testing` en las secciones `## Solapamiento`: `autenticacion-jwt-backend` ahora referencia `base-datos-conexion-backend` (usuarios y refresh tokens se persisten vía la capa `db/`), `base-datos-conexion-backend` referencia de vuelta a `autenticacion-jwt-backend` (login/register consumen el pool y rotan el token en transacción), `testing-backend` referencia `logging-ops-backend` (logger en nivel `silent` en tests + spy de `logger.error`) y `validacion-entrada-backend` documenta su límite con `base-datos-conexion-backend` (no valida contra BD: unicidad/FK son regla de negocio de services). Además se eliminó la referencia colgante a la inexistente `autorizacion-rbac`, ahora descrita como autorización fuera del repo. [2026-10-08]
  * **Files (Archivos)**: `backend/autenticacion-jwt-backend/SKILL.md`, `backend/base-datos-conexion-backend/SKILL.md`, `backend/testing-backend/SKILL.md`, `backend/validacion-entrada-backend/SKILL.md`. [2026-10-08]
- **Added**: **Orden de lectura recomendado en README**. Nueva sección `## Orden de lectura recomendado` con las cadenas de dependencias de ambos lados: backend (`arquitectura-backend` → `config-env-backend` → `autenticacion-jwt-backend` → `base-datos-conexion-backend` → `validacion-entrada-backend` → `errores-respuestas-backend` → `logging-ops-backend` → `testing-backend`) y frontend (`arquitectura-frontend` → `config-env-frontend` → `auth-frontend` → `data-fetching-frontend` → `formularios-frontend` → `estados-toast-frontend` → `ui-bloques-frontend`), con nota de que las conexiones exactas viven en `## Solapamiento` de cada skill. [2026-10-08]
  * **Files (Archivos)**: `README.md` (sección insertada entre el catálogo y "Formato de una skill"). [2026-10-08]

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
