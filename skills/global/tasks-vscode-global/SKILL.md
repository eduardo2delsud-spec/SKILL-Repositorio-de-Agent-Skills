---
name: tasks-vscode-global
description: 'Crea y edita tareas de VS Code en .vscode/tasks.json (task runner, problem matchers, variables, dependsOn, inputs, group build) para proyectos backend y frontend. Use when the user asks to add or fix a VS Code task, wire the Tasks: Run Task panel, parse compiler/linter errors in the terminal, chain build/test/lint steps, or edit tasks.json. Triggers: "tasks.json", "tarea de vscode", "agregar tarea", "crear task", "problem matcher", "dependsOn", "task runner", "ejecutar tarea en vscode", "ctrl+shift+b", "compilar en vscode".'
---

# Tasks de VS Code — un tasks.json validable, con matcher y sin rutas de una PC

**Regla:** cada task es portable (rutas solo con `${workspaceFolder}`), declara
`problemMatcher` si imprime errores y se encadena con `dependsOn` — nunca con `&&`.
El archivo queda sin comentarios (JSONC) para poder validarlo con Node.

## Cuándo usarla

- Piden "agregar una tarea de VS Code", editar `.vscode/tasks.json` o arreglar el panel de tasks.
- Errores de compilación/lint que no se subrayan ni aparecen en el panel de Problems.
- Encadenar pasos (lint → test → build) o parametrizar una task con un input.
- Configurar Ctrl+Shift+B, una task de watch o tareas que corren al abrir la carpeta.

## Checklist de ejecución

1. **Leer antes**: si existe `.vscode/tasks.json`, leerlo entero y respetar `version`,
   labels y estilo; si no existe, partir de `{"version": "2.0.0", "tasks": []}`.
2. **Anatomía por task**: `label` único y estable (otros tasks lo referencian en
   `dependsOn`; renombrarlo rompe cadenas), `type: "shell"` solo cuando hay pipes o
   redirecciones, `type: "process"` para invocar un binario directo (evita el quoting
   distinto de cada shell: en Windows `shell` puede ser PowerShell o cmd). `args` como
   array, nunca interpolados en un string.
3. **Variables, no rutas**: `${workspaceFolder}` para `cwd` y rutas; `${env:VAR}` solo
   para valores no secretos; `${input:id}` para parámetros que cambian por ejecución.
   Rutas absolutas rompen la task en la PC de cualquier otro del equipo.
4. **Problem matchers**: sin `problemMatcher` los errores no llegan al panel de Problems.
   Built-in: `$tsc`, `$eslint-compact`, `$eslint-stylish`. En **watch tasks** usar el
   matcher de watch (`$tsc-watch`): el matcher normal nunca detecta el fin de ronda y no
   reporta nada. Para tools sin matcher propio, declarar uno con `owner`, `fileLocation`
   y `pattern` (regex con los capture groups de archivo/línea/mensaje).
5. **Cadenas por `dependsOn`**: `lint` + `test` → `build` con
   `"dependsOrder": "sequence" | "parallel"`. Un `&&` monolítico mezcla todos los
   errores en un solo buffer y anula los matchers de cada paso.
6. **Inputs**: declarar el array `inputs` en la **raíz** del archivo (junto a `tasks`,
   no dentro de la task) con `promptString`/`pickString` + `default`, y referenciar como
   `${input:id}`.
7. **Grupos y atajos**: `group: {"kind": "build", "isDefault": true}` para Ctrl+Shift+B;
   `"group": "test"` para la task del runner de tests.
8. **Vida útil**: watch tasks con `presentation.panel: "dedicated"` (si no, comparten
   buffer con el build y se pisan los errores); `runOptions.runOn: "folderOpen"` solo
   para el watch que siempre debe estar corriendo (cuesta arranque).
9. **Diferencias por SO**: bloques `windows`/`linux`/`osx` para comando o shell distintos
   (`npm` vs `npm.cmd`, `options.shell.executable`); nunca duplicar la task entera.
10. **Plantilla concreta** — tasks.json de referencia (build encadena lint+test):

```json
{
  "version": "2.0.0",
  "inputs": [
    {
      "id": "nombreMigracion",
      "type": "promptString",
      "description": "Nombre de la migracion",
      "default": "add-tabla"
    }
  ],
  "tasks": [
    {
      "label": "build",
      "type": "shell",
      "command": "npm run build",
      "options": { "cwd": "${workspaceFolder}" },
      "group": { "kind": "build", "isDefault": true },
      "problemMatcher": "$tsc",
      "dependsOn": ["lint", "test"],
      "dependsOrder": "parallel"
    },
    {
      "label": "lint",
      "type": "shell",
      "command": "npm run lint",
      "options": { "cwd": "${workspaceFolder}" },
      "problemMatcher": "$eslint-compact"
    },
    {
      "label": "test",
      "type": "process",
      "command": "npx",
      "args": ["vitest", "run"],
      "options": { "cwd": "${workspaceFolder}" },
      "group": "test",
      "problemMatcher": []
    },
    {
      "label": "migracion nueva",
      "type": "shell",
      "command": "npm run migrate:create -- ${input:nombreMigracion}",
      "options": { "cwd": "${workspaceFolder}" },
      "problemMatcher": []
    }
  ]
}
```

11. **Verificación (obligatoria)**:

```bash
# JSON válido y sin comentarios (falla con JSONC)
node -e "JSON.parse(require('fs').readFileSync('.vscode/tasks.json','utf8'));console.log('ok')"
# version presente (debe imprimir 1)
rg -c '\x22version\x22:\s*\x222\.0\.0\x22' .vscode/tasks.json
# rutas absolutas de una PC (debe dar 0)
rg -c -e '[A-Za-z]:[/\\]' -e '/Users/' -e '/home/' .vscode/tasks.json
# encadenamiento con && en vez de dependsOn (debe dar 0)
rg -c '&&' .vscode/tasks.json
# secretos embebidos (debe dar 0)
rg -c -i -e 'password' -e 'secret' -e 'token' .vscode/tasks.json
```

## Anti-patrones (cómo detectarlos)

| Anti-patrón | Detección |
|---|---|
| Rutas absolutas de una PC | `rg -c -e '[A-Za-z]:[/\\]' -e '/Users/' -e '/home/' .vscode/tasks.json` → >0 |
| Comentarios JSONC (VS Code los tolera, `JSON.parse` no) | `node -e "JSON.parse(...)"` → error |
| `&&` encadenando pasos en vez de `dependsOn` | `rg -c '&&' .vscode/tasks.json` → >0 |
| Task sin `problemMatcher` que imprime errores | `rg -c 'problemMatcher' .vscode/tasks.json` → 0 con tasks activas |
| Secretos embebidos en la task | `rg -c -i -e 'password' -e 'secret' -e 'token' .vscode/tasks.json` → >0 |
| Sin `"version": "2.0.0"` | `rg -c '2\.0\.0' .vscode/tasks.json` → 0 |
| Watch task con matcher normal (no detecta rondas) | `rg -c 'tsc-watch' .vscode/tasks.json` → 0 con watch tasks |
| Task duplicada por SO en vez de bloques `windows`/`linux` | `rg -c '\x22label\x22' .vscode/tasks.json` con labels repetidos |

## Solapamiento

- `testing-backend` — la task de test delega en el script npm del proyecto (y en el
  runner definido allí); esta skill decide **cómo se invoca y se encadena**, no el
  comando del runner.
- `changelog-global` — una task nueva de build/test o un cambio de comando altera el
  workflow del equipo: se registra en el `CHANGELOG.md` del proyecto (Maintenance).
