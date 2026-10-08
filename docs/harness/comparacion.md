# Comparación: el mismo ticket (FLOW-1) con harness y sin harness

Mismo modelo en los dos lados (Opus 5.5, medium, Claude Code). Se comparan los dos **planes**; ninguno se aplicó.
Harness montado hasta la **pieza 5** de 8: `CLAUDE.md` (/init), MCP de Atlassian, skill `/priority-ticket`, skill `/commit`, subagente `adversarial-reviewer`. Sin montar: hook de Prettier, reglas de proceso, `AGENTS.md`.

| Casilla | Con harness | Sin harness |
| --- | --- | --- |
| **1. Archivos que propone tocar** | 9 modificados, 9 nuevos + los `ui/*` de shadcn (5 componentes), y elimina `App.css` y `assets/*`. Incluye `index.html`, `package-lock.json` y un `lib/errors.ts` propio para los mensajes. | 7 modificados, 8 nuevos + los `ui/*` de shadcn (5 componentes). No lista eliminados (vacía `App.css`). Sin `errors.ts`: los mensajes van dentro de cada pantalla. |
| **2. Convenciones respetadas / no** | Backend no se toca: sí. `passwordConfirmation` obligatorio: sí. Respuesta envuelta en `{ data: ... }` (escrito en `CLAUDE.md`): sí, lo da por sabido. Alcance: se ciñe al ticket y deja fuera logout y `fullName` explícitamente. Cada paso dice qué criterio cubre (lo pide la skill): sí, con tabla. Rama nueva antes de tocar código: no la menciona (esa regla no llegó a montarse). | No había ninguna convención escrita. Aun así: backend no se toca: sí (lo leyó en el README). `passwordConfirmation`: sí (leyó el validador). Envoltura `{ data }`: **no la conoce**, describe `{ user, token }` y deja un paso de `curl` para averiguarlo. Alcance: añade logout y campo nombre, que el ticket no pide. Propone rama nueva por su cuenta. |
| **3. Veces que intervine** | 0 durante el encargo. (Al crear la skill sí respondí 2 preguntas: qué hacer tras mover el ticket y cómo desempatar.) | 0. Llegó a Jira por el conector Atlassian Rovo de mi cuenta de Claude, no por una conexión del proyecto. |
| **4. Qué arreglaría a mano antes de enseñarlo** | Poco: comprobar con una petición real que el login con credenciales malas responde 400, porque el plan lo afirma y el CLAUDE.md solo nombra el error. Y decidir si quiero botón de cerrar sesión, que dejó fuera. | Quitar lo que no pide el ticket (botón de logout y campo nombre) o aceptarlo a propósito. Resolver antes de codificar la forma de la respuesta del backend, que el plan deja pendiente de un curl. Sacar los mensajes de error a un solo sitio. |

## Parte B: las tres líneas

1. Llegué hasta la pieza 5. La que me costó más tiempo fue conectar con Jira (pieza 2), por todo el lío de salir, volver a entrar, aprobar el servidor y autorizar en el navegador. Las demás fluyeron bastante rápido.
2. La primera diferencia que vi es que la copia pelada se puso a leer el README. Lo vi en la terminal, mientras trabajaba. Me dio la impresión de que la primera, con harness, demoró un poco más pero trabajó más fino.
3. En el prompt de Jira escribí "sabes que terminaste cuando puedes leer el ticket FLOW-1", y el agente dio la pieza por terminada sin haberlo leído, porque faltaba mi autorización.
