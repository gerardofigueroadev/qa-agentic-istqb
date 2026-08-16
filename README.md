# QA Agéntico + ISTQB

Workflow de QA agéntico para [Claude Code](https://claude.com/claude-code),
fundado en el proceso de prueba **ISTQB CTFL 4.0**. Convierte un bug ticket de
Jira en evidencia trazable: análisis → diseño → test rojo → evidencias →
verificación del fix → cierre con reporte.

**La idea central:** el agente no improvisa — ejecuta las actividades del
proceso de prueba del estándar, empaquetadas como skills versionables en git.
Cada skill es una actividad ISTQB; cada regla cita su sección del syllabus.
Lee la [guía ISTQB del workflow](docs/guia-istqb.md) para el mapeo completo.

## Las skills

| Skill | Actividad ISTQB | Qué hace |
|---|---|---|
| `/qa-flujo <TICKET>` | El proceso completo (§1.4) | Orquesta las 5 etapas en orden, con los 7 principios (§1.3) como reglas |
| `/qa-analisis <TICKET>` | Análisis de prueba | Duplicados → ticket → Sentry → reproducir → condiciones de prueba |
| `/qa-diseno <TICKET>` | Diseño de prueba | Casos por partición de equivalencia (§4.2.1) y error guessing (§4.4) |
| `/qa-ejecucion <TICKET>` | Implementación y ejecución | El spec nace **rojo** (demuestra el defecto) con la regresión en verde |
| `/qa-evidencias <TICKET>` | Gestión de defectos (§5.5) | Sube video/screenshots/trace de Playwright al ticket de Jira |
| `/qa-completitud <TICKET>` | Completitud (§2.2.3, §5.3) | Confirmation + regression testing, cierre, reporte de ciclo |

Cada etapa se puede usar suelta (ej. `/qa-evidencias PROJ-42` cualquier día que
quieras colgar evidencias a un ticket) o dejar que `/qa-flujo` las recorra todas.

## Requisitos

- [Claude Code](https://claude.com/claude-code) instalado (`npm install -g @anthropic-ai/claude-code`).
- Un proyecto con **Playwright** funcionando (`npm test` corre specs de `tests/`).
- Cuenta de **Jira** (Atlassian Cloud) y de **Sentry** conectadas a tu app.
- Opcional: **Notion** para el tablero del equipo y el reporte de ciclo.

## Instalación paso a paso

### 1. Copia las skills a tu proyecto

```bash
git clone https://github.com/gerardofigueroadev/qa-agentic-istqb.git
cp -r qa-agentic-istqb/.claude/skills tu-proyecto/.claude/skills
```

(También puedes copiar solo las skills que quieras — cada archivo es
independiente. Versiónalas en el git de tu proyecto: son texto.)

### 2. Configura los servidores MCP

Copia [.mcp.json](.mcp.json) a la raíz de tu proyecto (o fusiona su contenido
si ya tienes uno). Declara los conectores oficiales de Notion, Atlassian,
Sentry y Playwright.

Luego, en una terminal **dentro de tu proyecto**:

```bash
claude
```

Acepta los servidores del proyecto cuando pregunte, y ejecuta `/mcp` para
autenticar: `atlassian` y `sentry` (y `notion` si lo usas) abren el navegador
para el OAuth. Es una sola vez por máquina.

### 3. Crea el `.env`

```bash
cp qa-agentic-istqb/.env.example tu-proyecto/.env
```

Completa las variables de Jira — se usan para **subir archivos adjuntos** al
ticket, lo único que el MCP de Atlassian no cubre:

- `JIRA_BASE_URL`: tu sitio, ej. `https://tuorg.atlassian.net`
- `JIRA_EMAIL`: el email de tu cuenta Atlassian
- `JIRA_API_TOKEN`: créalo en <https://id.atlassian.com/manage-profile/security/api-tokens>

Asegúrate de que `.env` esté en tu `.gitignore`.

### 4. Escribe el `CLAUDE.md` de tu proyecto

Las skills leen las convenciones de tu equipo desde el `CLAUDE.md` del
proyecto: naming de specs, selectores, estructura de tests, datos de prueba.
Mínimo recomendado:

```markdown
## Convenciones para tests
- Framework: Playwright. Los specs viven en `tests/`.
- Naming: `tests/<feature>-<ticket>.spec.js`
- Selectores: SOLO `data-testid`. Nunca clases CSS ni texto.
- Estructura: comentarios `// Given / When / Then`.
- Un spec por bug + regresión de los flujos hermanos.
- Datos de prueba: emails/usuarios ficticios, nunca datos reales.
```

Los templates de [bug ticket](templates/bug-ticket.md) y
[test case](templates/test-case.md) también son punto de partida — ajústalos.

### 5. Pruébalo

```bash
claude
```

```
/qa-flujo TU-TICKET-123
```

El flujo lee el ticket, lo cruza con Sentry, reproduce el bug, escribe el spec
(que **debe fallar** con el bug vivo), sube las evidencias al ticket y, cuando
el fix esté disponible, verifica y cierra con trazabilidad completa.

## Qué esperar (y qué no)

- **El test nace rojo.** Si el spec pasa con el bug vivo, el flujo lo reescribe
  — un test que no demuestra el defecto no sirve (Principio 1 del ISTQB).
- **El flujo puede terminar temprano, y eso es éxito**: cerrado por duplicado
  (paso 0), o "no reproducible, documentado" con matriz de intentos y preguntas
  al reporter. Nunca inventa una repro.
- **El fix se verifica, no se cree.** Si el fix no arregla o rompe un vecino,
  el ticket rebota al dev con el spec rojo como criterio de aceptación
  ejecutable.
- **Costo en tokens:** pagas por *crear* testware, no por correrlo. El spec
  resultante corre gratis en CI para siempre; el agente es autor de tests, no
  ejecutor.

## Estructura del repo

```
.claude/skills/   las 6 skills del workflow (markdown, versionables)
.mcp.json         conectores MCP: Notion · Atlassian · Sentry · Playwright
.env.example      variables para la subida de evidencias a Jira
templates/        bug ticket mínimo viable · test case enriquecido
docs/             guía ISTQB: cómo el workflow aplica el syllabus
```

## Licencia

[MIT](LICENSE) — úsalo, adáptalo y compártelo. Si lo mejoras, un PR es bienvenido.
