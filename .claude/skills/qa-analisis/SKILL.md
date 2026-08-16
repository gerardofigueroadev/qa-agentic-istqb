---
name: qa-analisis
description: Análisis de prueba de un bug ticket — leer el defect report en Jira, cruzar con el error real en Sentry, reproducir el defecto y definir las condiciones de prueba. Usar al iniciar la investigación de un bug (ej. "/qa-analisis SCRUM-1") o como etapa 1 de qa-flujo.
---

# QA Análisis — ¿qué hay que probar?

> **Fundamento:** análisis de prueba (ISTQB CTFL 4.0 §1.4) sobre la base de
> prueba; lectura del defect report (§5.5); confirmación del defecto con
> testing exploratorio (§4.4).

El argumento es la clave del ticket. Si no viene, pregunta cuál.

## Pasos

### 0. ¿Ya se reportó? (chequeo de duplicados)
Antes de invertir en reproducir, tres detectores (§5.5 — un duplicado
desperdicia el ciclo completo):

- **Sentry (el automático):** Sentry agrupa por fingerprint — si el error ya
  ocurría, el issue existente lo delata con su **First seen** y su contador de
  eventos. Reporte de hoy + issue con historial viejo = bug conocido. Dos
  tickets apuntando al mismo issue de Sentry = duplicados seguros.
- **Jira (el funcional):** JQL por módulo y palabras clave, incluyendo
  cerrados: `project = <X> AND text ~ "<síntoma>"`. Un duplicado de un ticket
  *cerrado* no se descarta: es posible regresión — el bug volvió.
- **Notion (el semántico):** buscar el síntoma en la base de bugs del equipo,
  por si el vocabulario del reporter no coincide con el del ticket original.

**Si es duplicado:**
1. En Jira: linkear ambos tickets (link type "Duplicate"), comentar en el nuevo
   qué ticket lo cubre y qué issue de Sentry los agrupa, y cerrarlo como duplicado.
2. En Notion: registrar el bug con **Estado = "Duplicado"** y la relación
   **"Duplicado de"** apuntando al bug original — la bandera queda visible en el
   tablero para que nadie vuelva a invertir en él.

El flujo termina aquí — legítimamente.

### 1. Leer el defect report
Trae el ticket de Jira (MCP de Atlassian). Un buen reporte trae (§5.5): síntoma,
pasos, esperado vs actual, entorno, severidad, y — la clave — el link a Sentry.
Lo que falte no bloquea: se reconstruye en los pasos siguientes y se completa
el ticket al final.

### 2. Cruzar con Sentry
Abre el issue de Sentry linkeado (o búscalo por fecha/endpoint/error en el
proyecto). Extrae: tipo de error, stack trace, endpoint, frecuencia. Esto te da
la causa raíz *técnica* que el reporte describe en términos de *usuario*.

### 3. Reproducir el defecto
Con la app corriendo, reproduce como usuario (browser) y/o por API (curl).
Sesión exploratoria corta (§4.4): confirma el síntoma exacto, y sondea el
alcance — ¿pasa con otros datos? ¿otras rutas del mismo input? ¿los flujos
vecinos funcionan? Captura lo que veas.

### 4. Definir las condiciones de prueba
Salida de esta etapa — una lista explícita de **qué se debe probar**:
- La condición que demuestra el defecto.
- Las condiciones hermanas que deben seguir funcionando (P4: los defectos se
  agrupan — los vecinos del bug son sospechosos).
- Los errores manejados que deben seguir siendo manejados.

## Compuerta de salida

Defecto reproducido con evidencia + condiciones de prueba listadas. Sin esto,
no se diseña ningún caso (P3: testing temprano — entender antes de escribir).

## Desvío: no se reproduce

Si tras agotar las variantes (datos exactos del ticket, encodings, mayúsculas,
rutas alternativas, browser y API) el defecto no aparece:

1. **Compara contra el evento de Sentry**: release, entorno, user-agent,
   payload. Una diferencia de contexto es un hallazgo en sí (P6) — repórtala.
2. **NO avances a diseño.** Un test sobre una repro imaginada nace verde y no
   demuestra nada (viola el P1).
3. **El entregable cambia de forma**: comenta en el ticket la matriz de
   intentos (qué probaste, con qué datos, qué observaste) + 2-3 preguntas
   concretas al reporter.
4. **Devuelve el ticket** (transición a needs info / al reporter). Si Sentry no
   registra eventos del error hace semanas, propone cierre por obsoleto con
   monitoreo.

"No reproducible, documentado" es un resultado QA de primera clase — el flujo
terminó bien, en esta etapa.
