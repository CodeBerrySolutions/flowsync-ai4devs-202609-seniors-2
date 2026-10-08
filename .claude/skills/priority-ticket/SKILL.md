---
name: priority-ticket
description: Trae de Jira (proyecto FLOW) el ticket de mayor prioridad asignado al usuario en "Por hacer", resume sus criterios de aceptación, propone un plan de implementación en plan mode y, cuando el usuario lo aprueba, mueve el ticket a "En curso" e implementa el plan.
disable-model-invocation: true
---

# priority-ticket

Flujo para empezar a trabajar en el siguiente ticket de FLOW. Usa el servidor MCP `atlassian` (definido en `.mcp.json`). Sigue los pasos **en orden** y no te saltes ninguno.

## 0. Preparación

1. Si las herramientas `mcp__atlassian__*` no están cargadas, cárgalas con ToolSearch (`select:mcp__atlassian__getAccessibleAtlassianResources,mcp__atlassian__searchJiraIssuesUsingJql,mcp__atlassian__getJiraIssue,mcp__atlassian__getTransitionsForJiraIssue,mcp__atlassian__transitionJiraIssue`).
2. Obtén el `cloudId` con `getAccessibleAtlassianResources`. Si hay más de un sitio, usa el que tenga el proyecto FLOW; si no queda claro, pregunta.
3. Si la conexión falla o no hay autenticación, para y pide al usuario que ejecute `/mcp` para conectarse a Atlassian.

## 1. Traer el ticket de mayor prioridad

Busca con `searchJiraIssuesUsingJql`:

```
project = FLOW AND assignee = currentUser() AND status = "Por hacer" ORDER BY priority DESC, created ASC
```

- Pide al menos los campos `summary`, `status`, `priority`, `created`, `description`, `issuetype`.
- El primero del resultado es el elegido: mayor prioridad y, si hay empate, **el más antiguo** (fecha de creación ascendente).
- Si no hay resultados, dilo y termina aquí. No busques en otros estados ni proyectos.
- Trae el ticket completo con `getJiraIssue` y muestra al usuario: clave, título, prioridad, tipo y enlace.

## 2. Resumir los criterios de aceptación

- Localiza los criterios de aceptación: una sección de la descripción (p. ej. "Criterios de aceptación", "Acceptance criteria", "AC") o un campo personalizado dedicado.
- Resúmelos como lista numerada, fiel al ticket: no añadas criterios que no estén escritos.
- Si el ticket no tiene criterios de aceptación explícitos, dilo claramente y señala qué habría que aclarar con producto antes de planificar.

## 3. Plan mode: proponer la implementación

1. Entra en plan mode con `EnterPlanMode` (cárgala con ToolSearch si hace falta).
2. Explora el código en modo solo lectura (consulta `CLAUDE.md`: el backend no se modifica en este ejercicio; se lee para saber qué exige).
3. Propón un plan que cubra cada criterio de aceptación (indica qué paso cubre cada criterio), con los archivos a crear o modificar y cómo verificarlo.
4. Presenta el plan con `ExitPlanMode` para que el usuario lo apruebe.

**No toques el ticket en este paso.** Si el usuario rechaza el plan o pide cambios, ajústalo y vuelve a presentarlo; el ticket sigue en "Por hacer".

## 4. Tras la aprobación: mover a "En curso" e implementar

Solo cuando el usuario haya aprobado el plan:

1. Obtén las transiciones disponibles con `getTransitionsForJiraIssue` y localiza la que lleva al estado **"En curso"**. No inventes IDs de transición. Si no existe esa transición, para y pregunta al usuario.
2. Ejecuta la transición con `transitionJiraIssue`.
3. Verifica con `getJiraIssue` que el estado es ahora "En curso" y confírmalo al usuario.
4. Implementa el plan aprobado.

No hagas ningún otro cambio en Jira (comentarios, asignaciones, campos) salvo que el usuario lo pida.
