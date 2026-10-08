---
name: formularios-frontend
description: Form handling and validation (Zod) for React SPAs, synced with the API contract, with inline errors and trap patterns. Use when building forms, validating input, handling submit/errors, or fixing date/range fields. Triggers: "formularios", "validacion", "zod", "errores inline", "submit", "campos controlados", "fechas rango", "schema del formulario".
---

# Formularios Frontend — validación Zod sincronizada con el contrato

**Regla:** todo formulario valida con **Zod** alineado al contrato del backend, muestra errores
**inline** por campo y maneja submit/carga de forma consistente.

## Cuándo usarla

- Crear o refactorizar un formulario.
- La validación está duplicada, dispersa o diverge del backend.
- Errores que no se muestran, se pierden o llegan solo como toast.

## Checklist de ejecución

1. **Schema Zod por formulario** junto al formulario (`<form>.schema.ts`), reflejando el
   contrato del endpoint (campos, tipos, requeridos). Si el backend cambia el contrato, se
   cambia el schema en la misma tarea.
2. **Errores inline por campo**: mensaje bajo cada campo (`aria-describedby` + `aria-invalid`);
   errores de negocio del backend (409, reglas) también inline en el campo o formulario
   relacionado — **no solo toast** (el toast se va antes de leerlo).
3. **Submit consistente**: botón deshabilitado mientras envía, doble submit bloqueado,
   valores no perdidos al fallar.
4. **Patrones con trampa:**
   - **Rangos de fechas**: validar `min ≤ max` en el schema (`.refine`) y, si hay datepicker,
     pasar `minDate`/`maxDate` para que la UI no permita lo inválido.
   - Fechas en formato local vs ISO: parsear/serializar en un solo lugar (helper), no en cada
     campo; cuidar zona horaria.
   - Campos numéricos: distinguir string del input del número validado (coerción de Zod).
   - Selects con "vacío" inicial: un valor sentinela explícito, no `""` que pasa por-default.
5. **Campos controlados de forma consistente**: un patrón en todo el repo (controlled + state
   del form), no mezclar controlado/descontrolado por formulario.
6. **Verificación (obligatoria):**

   ```bash
   # schemas Zod presentes
   rg "\.schema\.ts" src/ -l   # o rg "z\.object" src/
   # errores solo en toast y no inline
   rg "aria-invalid|aria-describedby" src/ -c
   # validación manual duplicada fuera de Zod
   rg "if \(.*\.value\s*===\s*''" src/components
   # doble submit sin bloqueo
   rg "onSubmit" src/ -c  vs  rg "isSubmitting|disabled=\{" src/ -c
   ```

## Anti-patrones (cómo detectarlos)

| Anti-patrón | Detección |
|---|---|
| Validación solo en el submit o con `if` sueltos | `rg "e\.preventDefault" src/` + chequeos ad-hoc |
| Schema divergiendo del backend (acepta lo que el API rechaza) | comparar schema del form vs contrato del endpoint |
| Error de negocio mostrado solo como toast | `rg "toast.*error" src/` sin error inline en el campo |
| Rango de fechas invertido permitido por la UI | datepicker sin `minDate`/`maxDate` |
| Fecha +1 día por zona horaria en la serialización | parse/serialize dispersos: `rg "new Date\(" src/` |
| Doble envío (doble click = doble registro) | submit sin bloqueo `disabled/isSubmitting` |
| Campos no controlados que pierden valores en error | probar fallo de red y revisar que el form conserva lo tipeado |

## Solapamiento

- `data-fetching-frontend` — el submit es una **mutation**; sus errores van a inline/estado.
- `estados-toast-frontend` — define cuándo el error va toast (global) vs inline (campo).
- `auth-frontend` — formularios de login/registro/recuperación aplican este flujo.
- `ui-bloques-frontend` — tokens y helpers de formato (fechas/moneda) compartidos.
