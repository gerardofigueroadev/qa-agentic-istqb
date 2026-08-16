# Guía ISTQB del workflow

Cómo este workflow aplica el syllabus **ISTQB CTFL 4.0**, sección por sección.
No es una reinterpretación: cada skill ES una actividad del proceso de prueba
(§1.4), y las reglas que gobiernan el flujo son los principios del capítulo 1.

## El mapeo completo

| Momento del workflow | Fundamento (CTFL 4.0) |
|---|---|
| Chequeo de duplicados antes de invertir | Gestión de defectos §5.5 — un duplicado desperdicia el ciclo completo |
| Leer el defect report mínimo viable | §5.5 — contenido del reporte: pasos, esperado vs actual, entorno, severidad |
| Cruzar con el error real en Sentry | Base de prueba + causa raíz técnica |
| Reproducir antes de escribir tests | Principio 3 (testing temprano) + testing exploratorio §4.4 |
| Derivar casos del dominio de entrada | Partición de equivalencia §4.2.1 · valores límite §4.2.2 · error guessing §4.4 |
| El spec nace rojo | **Principio 1: el testing muestra la presencia de defectos** — el test rojo es la demostración formal |
| Probar los flujos hermanos | Principio 4: los defectos se agrupan |
| Re-test tras el fix | **Confirmation testing §2.2.3** |
| Los hermanos siguen verdes tras el fix | **Regression testing §2.2.3** |
| Evidencias objetivas en el ticket | §5.5 — resultados reales: logs, video, screenshots |
| Cierre con trazabilidad | Completitud de prueba §1.4: ticket ↔ error ↔ spec ↔ evidencia ↔ reporte |
| Reporte de ciclo | §5.3 — reportes de progreso y completitud de prueba |

## Los 7 principios (§1.3) como reglas operativas

1. **El testing muestra la presencia de defectos, no su ausencia** → el test
   debe fallar con el bug vivo antes de valer algo. Un spec que pasa con el
   defecto presente se reescribe.
2. **Las pruebas exhaustivas son imposibles** → se prueban *representantes* de
   cada partición de equivalencia, no todos los valores.
3. **El testing temprano ahorra** → reproducir y analizar antes de escribir
   una línea de test o de reporte.
4. **Los defectos se agrupan** → los vecinos del bug entran siempre a la
   regresión; y un fix también puede introducir defectos nuevos.
5. **Los tests se desgastan (paradoja del pesticida)** → el spec de hoy es la
   regresión de mañana; renuévalo cuando deje de ejercitar lo que importa.
6. **El testing depende del contexto** → las convenciones viven en el
   `CLAUDE.md` de cada proyecto, no dentro de las skills.
7. **La falacia de la ausencia de defectos** → cerrar tickets no es el
   objetivo; el reporte de ciclo traduce el estado a impacto de negocio.

## Técnicas aplicadas en el diseño (§4)

**Partición de equivalencia (§4.2.1):** para el input involucrado en el bug se
dibujan las clases que el sistema debería tratar igual — la partición donde
vive el bug, las particiones válidas hermanas, y las inválidas con error
manejado. Un caso representante por partición.

Ejemplo real de por qué importa: un cupón de descuento llamado `50%OFF` es un
valor *válido de negocio* dentro de una partición que nadie probó (códigos con
carácter especial). El bug no estaba en los valores inválidos — estaba en una
clase válida sin representante.

**Error guessing (§4.4):** los sospechosos de siempre entran al diseño aunque
ningún requisito los mencione: caracteres especiales, vacíos, espacios,
mayúsculas/minúsculas, encodings.

## Los finales que no llegan al cierre (y por qué son éxito)

El proceso ISTQB no promete cerrar todo ticket — promete **decisiones con
evidencia**:

- **Cerrado por duplicado** (paso 0 de `qa-analisis`): Sentry agrupa errores
  por fingerprint — un reporte nuevo sobre un issue con *First seen* viejo es
  un bug conocido. Se linkea al original y se cierra sin gastar el ciclo.
- **No reproducible, documentado** (desvío de `qa-analisis`): matriz de
  intentos + diferencias de contexto vs el evento de Sentry + preguntas al
  reporter. Nunca se escribe un test sobre una repro imaginada.
- **Fix rebotado** (desvío de `qa-completitud`): si la confirmación falla o la
  regresión cae, el ticket vuelve al dev con el spec rojo como criterio de
  aceptación ejecutable — "haz que `npm test` pase".

## Referencias

- [ISTQB CTFL 4.0 Syllabus](https://www.istqb.org/certifications/certified-tester-foundation-level)
  (§1.3 principios · §1.4 actividades · §2.2.3 confirmación y regresión ·
  §4.2 técnicas de caja negra · §4.4 basadas en experiencia · §5.3 reportes ·
  §5.5 gestión de defectos)
