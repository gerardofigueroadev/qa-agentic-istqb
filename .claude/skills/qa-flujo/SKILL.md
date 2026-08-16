---
name: qa-flujo
description: Proceso de prueba completo (ISTQB CTFL 4.0) para un bug ticket — orquesta análisis, diseño, ejecución, evidencias y completitud. Usar cuando llega un bug para investigar de punta a punta (ej. "/qa-flujo SCRUM-1") o el usuario pide correr el flujo QA sobre un ticket.
---

# QA Flujo — el proceso de prueba, orquestado

> **Fundamento:** actividades del proceso de prueba, ISTQB CTFL 4.0 §1.4.
> Cada etapa de este flujo es una actividad del estándar, empaquetada como skill.

El argumento es la clave del ticket (ej. `SCRUM-1`). Si no viene, pregunta cuál.

## Etapas (invocar cada skill en orden)

1. **`qa-analisis`** — leer el defect report, cruzar con Sentry, reproducir,
   definir condiciones de prueba.
2. **`qa-diseno`** — derivar los casos de prueba por partición de equivalencia.
3. **`qa-ejecucion`** — implementar el spec y ejecutarlo: el caso del bug en
   rojo, la regresión en verde.
4. **`qa-evidencias`** — completar el defect report con las evidencias.
5. **`qa-completitud`** — confirmación del fix, regresión, cierre y trazabilidad.

No saltes etapas ni las mezcles: cada una tiene una compuerta de salida y la
siguiente asume que se cumplió.

## Finales legítimos que no llegan al cierre

- El flujo puede **terminar en el paso 0** si el bug ya estaba reportado:
  cerrado por duplicado, linkeado al ticket original y al issue de Sentry que
  los agrupa (ver chequeo de duplicados en `qa-analisis`).
- El flujo puede **terminar en la etapa 1** con "no reproducible, documentado"
  (ver desvío en `qa-analisis`): ticket devuelto con matriz de intentos y
  preguntas al reporter.
- La etapa 5 puede **rebotar el ticket al dev** si el fix no arregla o rompe
  un vecino (ver desvío en `qa-completitud`); el flujo se reanuda con solo
  `qa-completitud` cuando haya fix nuevo.

Un flujo que se detiene con evidencia es un flujo que funcionó. Lo único
prohibido es avanzar sin cumplir la compuerta.

## Principios ISTQB que gobiernan todo el flujo (§1.3)

- **P1 — El testing muestra la presencia de defectos, no su ausencia.** Por eso
  el test nace rojo: es la demostración formal del defecto.
- **P3 — El testing temprano ahorra tiempo y dinero.** Reproduce y analiza antes
  de escribir una línea de test.
- **P4 — Los defectos se agrupan.** Donde hay un bug, prueba los vecinos: los
  flujos hermanos siempre entran en la regresión.
- **P5 — Los tests se desgastan (paradoja del pesticida).** Si la regresión
  nunca falla, revisa si sigue probando lo correcto.
- **P6 — El testing depende del contexto.** Las convenciones del proyecto viven
  en su `CLAUDE.md` — respétalas.

## Regla transversal

**Evidencia o no pasó.** Toda afirmación (se reproduce, falla, pasa, Sentry en
silencio) va acompañada de su prueba: salida de comando, screenshot, video o link.
