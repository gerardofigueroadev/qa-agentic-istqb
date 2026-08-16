---
name: qa-diseno
description: Diseño de casos de prueba a partir de las condiciones de prueba de un bug — partición de equivalencia, valores límite y error guessing, con trazabilidad al ticket. Usar tras el análisis de un bug (ej. "/qa-diseno SCRUM-1") o como etapa 2 de qa-flujo.
---

# QA Diseño — ¿cómo se prueba?

> **Fundamento:** diseño de prueba (ISTQB CTFL 4.0 §1.4); técnicas de caja
> negra: partición de equivalencia (§4.2.1) y valores límite (§4.2.2);
> técnicas basadas en experiencia: error guessing (§4.4).

Requiere las condiciones de prueba del análisis (si no existen, corre
`qa-analisis` primero).

## Pasos

### 1. Particionar el dominio de entrada
Para el input involucrado en el bug, dibuja las **particiones de equivalencia**:
clases de valores que el sistema debería tratar igual. Incluye siempre:
- La partición donde vive el bug (ejemplo clásico: un cupón llamado `50%OFF` —
  válido de negocio, pero el carácter especial revienta la técnica).
- Las particiones válidas hermanas (código alfanumérico, sin código).
- Las particiones inválidas con error manejado (código inexistente).

**Error guessing** (§4.4): caracteres especiales, vacíos, espacios, mayúsculas/
minúsculas, encodings — los sospechosos de siempre.

### 2. Derivar los casos
Un caso por partición relevante (representante de la clase), con el formato de
`templates/test-case.md`: ID `TC-<ticket>-<n>`, título en positivo,
precondiciones, pasos numerados, **resultado esperado observable por el usuario**.

### 3. Trazabilidad
Cada caso referencia el ticket que lo origina y (si aplica) el issue de Sentry.
Registra los casos en la base del equipo (Notion si está conectada).

## Compuerta de salida

Casos escritos y trazados: mínimo el caso del bug + los de regresión de las
particiones hermanas. El diseño dice *qué esperar*; todavía no se escribe código.
