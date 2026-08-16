---
name: qa-evidencias
description: Completa el defect report adjuntando las evidencias de Playwright (video, screenshots, trace de test-results/) al ticket de Jira vía API REST. Usar cuando el usuario pida subir evidencias a un ticket (ej. "/qa-evidencias SCRUM-1") o como etapa 4 de qa-flujo.
---

# QA Evidencias — el defect report completo

> **Fundamento:** gestión de defectos (ISTQB CTFL 4.0 §5.5) — un defect report
> completo incluye resultados reales *objetivos*: logs, capturas, videos.
> "Evidencia o no pasó".

El argumento es la clave del ticket. Si no viene, pregunta cuál.

## Requisitos

Variables en el `.env` del proyecto: `JIRA_BASE_URL`, `JIRA_EMAIL`,
`JIRA_API_TOKEN` (token de https://id.atlassian.com/manage-profile/security/api-tokens).
El MCP de Atlassian no maneja adjuntos — esta pieza va por la API REST.

## Pasos

1. **Reunir las evidencias.** Por defecto, lo que haya en `test-results/`
   (video `.webm`, screenshots `.png`, trace `.zip`). Si el usuario señala
   otros archivos, usar esos. Si no hay nada, decirlo — nunca inventar evidencia.
2. **Nombrar con intención.** Copias renombradas con el patrón
   `<TICKET>-<qué-demuestra>.<ext>` (ej. `SCRUM-1-sintoma1-popup.png`) para que
   en Jira se entienda cada archivo sin abrirlo.
3. **Subir:**
   ```bash
   curl -s -X POST "$JIRA_BASE_URL/rest/api/3/issue/<TICKET>/attachments" \
     -u "$JIRA_EMAIL:$JIRA_API_TOKEN" -H "X-Atlassian-Token: no-check" \
     -F "file=@<archivo1>" -F "file=@<archivo2>"
   ```
   Verificar HTTP 200 y reportar qué se subió.
4. **Dejar rastro.** Comentar en el ticket (MCP de Atlassian): pasos ejecutados,
   resultado real vs esperado, causa raíz con link a Sentry, y qué demuestra
   cada evidencia adjunta.

## Notas

- Adjuntos obsoletos del mismo caso: preguntar antes de borrar
  (`DELETE /rest/api/3/attachment/<id>`).
- Nunca subir archivos con datos reales de usuarios.
