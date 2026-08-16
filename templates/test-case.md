# Template — Test Case (lo que el agente devuelve al ticket)

- **ID:** TC-<ticket>-<n> (ej. TC-JRA-42-01)
- **Título:** comportamiento esperado en positivo
- **Precondiciones:** estado inicial (app corriendo, datos, usuario)
- **Pasos:** numerados, uno por acción del usuario
- **Resultado esperado:** lo que el usuario debe ver
- **Resultado actual (si es bug):** síntoma + error técnico
- **Tipo:** Regresión / Funcional / Smoke
- **Automatizado:** ✅/❌ + ruta del spec (ej. `tests/checkout-jra-42.spec.js`)
- **Evidencias:** video, screenshots, trace (en `test-results/`)
- **Sentry:** issue vinculado + estado post-fix (0 eventos nuevos)
