---
name: qa-completitud
description: Cierre del ciclo de un bug — confirmation testing del fix, regresión, verificación en Sentry, cierre del ticket con trazabilidad completa. Usar cuando el fix está disponible (ej. "/qa-completitud SCRUM-1") o como etapa 5 de qa-flujo.
---

# QA Completitud — confirmar, cerrar, trazar

> **Fundamento:** confirmation testing y regression testing (ISTQB CTFL 4.0
> §2.2.3); completitud de prueba (§1.4): archivar testware, reportar, trazar.

El argumento es la clave del ticket. Si no viene, pregunta cuál.

## Pasos

### 1. Confirmation testing (§2.2.3)
Con el fix disponible (rama del dev): cámbiate a esa rama, reinicia la app y
re-ejecuta **el mismo spec que demostró el defecto**. El caso que nacía rojo
ahora debe pasar. Pega la salida.

### 2. Regression testing (§2.2.3)
En la misma corrida, los casos de regresión (particiones hermanas) deben seguir
verdes. Un fix que rompe un vecino no es un fix (P4: los defectos se agrupan —
también los que introduce un arreglo).

### 3. Sentry en silencio
Re-ejecuta el flujo que reventaba y verifica en Sentry que el issue **no
registra eventos nuevos**. Márcalo como resuelto. Esta es la evidencia de que
el fix funciona en el mundo real, no solo en el test.

### 4. Cierre con trazabilidad
- Transiciona el ticket de Jira a done/cerrado con comentario final: qué se
  arregló, spec que lo cubre, evidencias, link a Sentry resuelto.
- Actualiza el test case en la base del equipo (Notion): estado verde,
  automatizado ✓, ruta del spec.
- El testware queda en el repo: el spec de este bug es la regresión del futuro
  (P5: renuévalo cuando deje de ejercitar lo que importa).

### 5. Reporte de ciclo (§5.3)
Genera/actualiza el **reporte de completitud** en Notion (página "📊 Reporte de
ciclo — <fecha>" bajo el hub del equipo), con datos reales de Jira, Sentry y la
base de test cases — nunca inventados. **Formato visual, no ensayo**:

- **Fila de KPIs**: callouts en columnas con el número grande (bugs abiertos,
  resueltos en el ciclo, test cases, issues vivos en Sentry).
- **El titular**: UN callout con la lectura de negocio en 2-3 líneas, con links
  a tickets y Sentry (para quien no va a abrir Jira).
- **Riesgos**: UN callout con lo abierto y su impacto.
- **UN gráfico** (el plan gratuito de Notion permite un solo chart): donut de
  **defectos por estado** como vista de la base — se actualiza solo con los
  datos, y es el que cuenta la historia del ciclo (abiertos → cerrados). No
  agregar más charts ni duplicarlo con otros formatos.

Texto mínimo: si una sección necesita más de 3 líneas, va como link al detalle,
no como párrafo.

## Desvío: el fix no arregla

Si la corrida sobre la rama del fix no queda toda verde, distingue el sub-caso:

- **(a) El caso del bug sigue rojo** → fix incompleto. Atención si el síntoma
  *cambió* (ej. la validación ya pasa pero el checkout sigue en 500): repórtalo
  explícitamente — el fix mató la mitad del bug y movió el síntoma.
- **(b) El caso del bug pasa pero una regresión cayó** → el fix rompió un
  vecino (P4: los defectos se agrupan, incluidos los que introduce un arreglo).
  El hermano roto es un defecto nuevo causado por el fix.

En ambos sub-casos: **NO cierres el ticket.** Comenta la salida del test (qué
caso falla y por qué), adjunta el evento nuevo de Sentry si lo hay, y devuelve
el ticket a In progress asignado al dev. El spec rojo queda en el repo tal
cual: es el **criterio de aceptación ejecutable** — el dev no recibe "no
funciona", recibe "haz que `npm test` pase". Cuando llegue el fix v2,
re-invoca solo esta skill.

## Compuerta de salida

Ticket cerrado, spec verde en la rama del fix, Sentry resuelto y sin eventos
nuevos, test case actualizado. El ciclo queda trazable de punta a punta:
ticket ↔ Sentry ↔ spec ↔ evidencias ↔ reporte.
