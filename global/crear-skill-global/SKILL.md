---
name: crear-skill-global
description: Create, edit, or improve agent skills (SKILL.md) in this repository following its strict convention. Use when adding a new skill, rewriting an existing one, fixing a broken skill, or writing a skill description with triggers. Triggers: "crear skill", "nueva skill", "agregar skill", "skill de X", "editar skill", "mejorar skill", "SKILL.md", "description de la skill", "triggers".
---

# Crear Skill — intención, borrador, verificar, iterar

**Regla:** una skill nueva o editada no está terminada hasta pasar la suite de verificación
(frontera `name`=carpeta, cero refs externas, ≤150 líneas, secciones fijas, CHANGELOG).
El borrador se escribe después de entender **para qué** dispara, no antes.

## Cuándo usarla

- Crear una skill nueva en `backend/`, `frontend/`, `devops/` o `global/`.
- Reescribir o parchear una skill existente.
- Escribir/afinar la `description` (disparadores) de una skill.
- Alguien pide "esto repetilo en una skill".

## Checklist de ejecución

### 1. Capturar intención (antes de escribir)

Cuatro preguntas, respondidas (por el usuario o del contexto):

1. **¿Qué habilita?** — el resultado concreto cuando dispara.
2. **¿Cuándo dispara?** — las frases exactas que tipea el usuario (futuros Triggers).
3. **¿Formato de salida?** — si el output tiene forma fija, definir la **plantilla** ahora.
4. **¿Criterio de éxito?** — qué comando/estado prueba que funcionó.

### 2. Borrador con la estructura fija

Orden de secciones (nunca otro):

1. `# Título — frase de regla`
2. `**Regla:**` en 1-2 líneas (el principio que aplica)
3. `## Cuándo usarla` (bullets de disparo)
4. `## Checklist de ejecución` (pasos numerados; el último: **Verificación (obligatoria)**)
5. `## Anti-patrones (cómo detectarlos)` (tabla Anti-patrón | Detección)
6. `## Solapamiento` (solo hermanas de ESTE repo)

Reglas de contenido:

- **Gotchas van en el cuerpo**, no en archivo aparte — si el agente no lo lee antes de
  encontrar la trampa, no existe. No se usa `references/` a esta escala.
- **Explicar el porqué** detrás de cada regla; si todo es `MAYÚS`/`PROHIBIDO` sin razón,
  el agente lo cumple de letra y viola el espíritu.
- Omitir lo que el agente ya sabe (qué es HTTP, qué es Express); solo lo no obvio:
  convenciones, trampas y criterios de verificación.
- Formato de salida esperado → **plantilla concreta** (ejemplo real), no prosa descriptiva.

### 3. Escribir la description (el mecanismo de disparo)

- Frontmatter: `name` = **nombre de la carpeta** (kebab-case) y `description`.
- La description solo **dispara**: qué hace + `Use when...` + `Triggers: "frase", "otra"`.
  Con palabras exactas que tipea el usuario, en su idioma (mezclar es).
- **Description-trap**: la description **nunca resume el flujo/workflow** de la skill — si
  resume el proceso, el agente cree que ya la conoce y saltea el cuerpo entero.
- Ser "pushy": listar los contextos aunque no nombren la skill ("even if they don't
  explicitly ask...").

### 4. Iterar con uso real

- Probar con 2-3 prompts realistas (frases que diría un usuario, no las nuestras).
- Si el agente **no dispara**: afinar Triggers en la description, no alargar el cuerpo.
- Si dispara pero hace otra cosa: el cuerpo no explica el porqué o el checklist es ambiguo.
- Generalizar: la skill se usará mil veces; un fix para un caso puntual que la estrecha es
  un retroceso. Preferir metáfora/criterio nuevo antes que reglas rígidas de a una.

### 5. Verificación (obligatoria)

```bash
# 1) name = carpeta
rg "^name:" <categoria>/<carpeta>/SKILL.md        # debe imprimir la carpeta
# 2) cero referencias externas (rutas ajenas al repo)
rg -i "regla[s]/|patrone[s]/|br[ai]n/|proy[e]ctos/|portafoli[o]|openbr[ai]n|vizt[a]|vau[l]t" <carpeta>
# 3) tamaño (objetivo ≤150, absoluto 500)
rg -c '^' <carpeta>/SKILL.md
# 4) secciones fijas presentes
rg "^## " <carpeta>/SKILL.md
# 5) changelog registra el alta/modificación en el mismo gesto
rg "<nombre-de-la-skill>" CHANGELOG.md
```

Falla cualquiera → corregir antes de dar por terminada (la suite se corre siempre al final,
después de iterar con uso real).

## Anti-patrones (cómo detectarlos)

| Anti-patrón | Detección |
|---|---|
| `name` ≠ nombre de carpeta | `rg "^name:" <carpeta>/SKILL.md` vs el nombre de la carpeta |
| Description que resume el workflow (trap) | description con pasos/orden ("first... then...") en vez de `Use when` |
| Sin `Triggers:` en español/ingles del usuario | `rg "Triggers:" <carpeta>/SKILL.md` → sin match |
| Referencias externas al repo | `rg -i "openbr[ai]n\|br[ai]n/\|proy[e]ctos/" <carpeta>` |
| Pasados de 150 líneas | `rg -c '^' <carpeta>/SKILL.md` → > 150 |
| Verificación ausente o sin comandos `rg` | última sección del checklist sin bloque bash |
| Solapamiento con skills ajenas al repo | `rg "Solapamiento" -A5 <carpeta>` con nombres no listados en AGENTS.md |
| Alta sin entrada en CHANGELOG | `rg "<skill>" CHANGELOG.md` → sin match |

## Solapamiento

- `changelog-global` — toda alta/modificación de skill entra al CHANGELOG en el mismo
  gesto, con su formato exigente; esta skill solo dice **que** se registra, **cómo** lo
  define aquella.
