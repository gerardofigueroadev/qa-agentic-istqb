---
name: qa-ejecucion
description: Implementación y ejecución de los casos de prueba como spec automatizado — el caso del bug debe fallar en rojo con el defecto vivo, la regresión debe pasar. Usar cuando los casos ya están diseñados (ej. "/qa-ejecucion SCRUM-1") o como etapa 3 de qa-flujo.
---

# QA Ejecución — el test nace rojo

> **Fundamento:** implementación y ejecución de prueba (ISTQB CTFL 4.0 §1.4);
> Principio 1 (§1.3): el testing muestra la **presencia** de defectos — el test
> rojo es la demostración formal del defecto reportado.

Requiere los casos diseñados (si no existen, corre `qa-diseno` primero).

## Pasos

### 1. Implementar el spec
Un spec por bug, siguiendo el `CLAUDE.md` del proyecto. Convenciones
recomendadas (ajústalas a las de tu equipo):
- Naming: `tests/<feature>-<ticket>.spec.js`.
- Selectores SOLO `data-testid`; estructura `// Given / When / Then`.
- Assertions sobre lo que el usuario ve (popup, toast, modal), complementadas
  con la respuesta HTTP cuando aplique.
- Un test por caso diseñado: el del bug + los de regresión.

### 2. Ejecutar contra el bug vivo
`npm test`. El resultado esperado de esta corrida es **asimétrico**:
- El caso del bug: **FALLA** (rojo). Está demostrando el defecto.
- Los casos de regresión: **PASAN** (verde). Delimitan el alcance.

Pega la salida real de la ejecución.

### 3. Regla inquebrantable
Si el caso del bug *pasa* con el defecto vivo, el test está mal — no está
probando el comportamiento defectuoso. Reescríbelo hasta que falle por la razón
correcta (verifica que el mensaje de error del assert coincida con el síntoma).

## Compuerta de salida

Spec en el repo + salida de ejecución con el rojo/verde esperado + evidencias
generadas en `test-results/` (video, trace, screenshot — salen solas por
`playwright.config.js`, no agregues captura manual).
