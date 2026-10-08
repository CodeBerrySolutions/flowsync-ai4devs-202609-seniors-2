# Prompts

Aquí van **todos los prompts que lanzaste** para hacer el ejercicio, en el orden en que los
lanzaste, con el modelo y la herramienta de cada uno.

Esto no es papeleo. Lo que se revisa es **cómo pediste las cosas**, no solo lo que salió: un
resultado flojo con un prompt bueno y un resultado flojo con un prompt vago necesitan feedback
distinto, y sin este archivo no se distinguen.

## Cómo rellenarlo

- Un apartado `## Prompt N` por cada prompt.
- **Pega el prompt tal cual lo lanzaste**, dentro del bloque de código, aunque ocupe diez líneas
  y aunque tenga faltas. No lo reescribas para que quede bien: el que arreglaste mentalmente
  después no es el que lanzaste.
- Incluye también los que **no funcionaron**. Suelen ser los más útiles de leer.
- `Modelo` y `Herramienta` en todos. Si cambiaste de una a otra a mitad, se nota aquí.

Borra el ejemplo de abajo cuando escribas el primero.

---
## Prompt 1 (pieza 1: archivo de instrucciones)

**Modelo:** Opus 5.5 (medium)
**Herramienta:** Claude Code

```
/init
```

**Qué salió:** 

Funcionó a la primera. Leyó el README del ejercicio

## Prompt 2 (pieza 2: MCP de Atlassian)

**Modelo:** Opus 5.5 (medium)
**Herramienta:** Claude Code

```
Necesito que conectes Claude Code con mi Jira para poder leer y mover los tickets desde la terminal. Usa el servidor MCP de Atlassian, configurado a nivel de proyecto, para el sitio codeberrysolutions.atlassian.net, de modo que la conexión quede guardada dentro del repo como parte del harness. Sabes que terminaste cuando puedes leer el ticket FLOW-1 y me has dicho qué debo autorizar yo. No toques otros archivos ni guardes credenciales en el repo. Si hay más de una forma de conectar con Atlassian, pregunta antes de elegir una.
```

**Qué salió:** creó `.mcp.json` sin preguntar; no pudo leer FLOW-1 hasta que yo salí, volví a entrar, aprobé el servidor y autoricé con `/mcp` en el navegador. Fue la pieza más lenta.

## Prompt 3 (pieza 3: skill /priority-ticket)

**Modelo:** Opus 5.5 (medium)
**Herramienta:** Claude Code

```
Necesito que crees una skill de proyecto llamada priority-ticket que haga estos cuatro pasos, usando la conexión con Jira del espacio FLOW: 1- Traer de Jira el ticket de mayor prioridad asignado a mí que esté en "Por hacer". 2- Resumir sus criterios de aceptación. 3- Entrar en plan mode y proponer cómo implementarlo. 4- Cuando yo apruebe el plan, mover el ticket a "En curso". Sabes que terminaste cuando la skill existe en el repo y se puede invocar con /priority-ticket. Solo tienes que crearla, no ejecutarla ahora; no toques el ticket ni otros archivos. Si dudas de algún paso de la secuencia, pregunta antes de inventarlo.
```

**Qué salió:** antes de crearla hizo dos preguntas (qué hacer tras mover el ticket y cómo desempatar prioridades); respondí «implementar el plan» y «el más antiguo». Funcionó a la primera.

## Prompt 4 (pieza 4: skill /commit)

**Modelo:** Opus 5.5 (medium)
**Herramienta:** Claude Code

```
Crea una skill de proyecto llamada commit que genere un commit con el formato tipo(scope): descripción usando solo los cambios preparados. La skill no debe añadir archivos por su cuenta ni hacer push, y debe preguntarme antes de hacer el commit si duda de qué tipo o scope corresponde, o si no hay cambios preparados. Sabes que terminaste cuando la skill existe en el repo y se puede invocar con /commit. Solo tienes que crearla, no ejecutarla ahora; no toques otros archivos.
```

**Qué salió:** funcionó a la primera, sin preguntas.

## Prompt 5 (pieza 5: subagente adversarial-reviewer)

**Modelo:** Opus 5.5 (medium)
**Herramienta:** Claude Code

```
Crea un subagente de proyecto llamado adversarial-reviewer con este encargo: revisar un pull request intentando romperlo, no aprobarlo. No edita nada: solo lee, y devuelve solo los hallazgos, no todo lo que leyó. Sabes que terminaste cuando el subagente existe en el repo y está disponible para invocarlo. Solo necesitas crearlo, no ejecutarlo ahora; no toques otros archivos. Si dudas de qué permisos o herramientas necesita para revisar sin poder editar, pregúntame antes de decidir.
```

**Qué salió:** hizo dos preguntas (herramientas y si puede ejecutar tests); respondí «Read/Grep/Glob + Bash» y «solo lectura». Avisó de que la prohibición de escribir queda solo en las instrucciones.

## Prompt 6 (el ticket, en la copia con harness)

**Modelo:** Opus 5.5 (medium)
**Herramienta:** Claude Code

```
/priority-ticket
```

**Qué salió:** lanzado tras `/clear`. Leyó FLOW-1, exploró el código y propuso un plan en plan mode. No lo aprobé.

## Prompt 7 (el ticket, en la copia sin harness)

**Modelo:** Opus 5.5 (medium)
**Herramienta:** Claude Code

```
Usando Jira, espacio FLOW, haz estos cuatro pasos:
1- Trae el ticket de mayor prioridad asignado a mí que esté en "Por hacer".
2- Resume sus criterios de aceptación.
3- Entra en plan mode y propón cómo implementarlo.
4- Cuando yo apruebe el plan, mueve el ticket a "En curso".
```

**Qué salió:** lanzado en `flowsync-sin-harness`. Encontró FLOW-1 por el conector de Atlassian de mi cuenta, leyó el README y propuso un plan. No lo aprobé.

---

Piezas 6 (hook de Prettier), 7 (reglas de proceso) y 8 (`AGENTS.md`): no llegué a lanzarlas; corté el montaje en la pieza 5 por el reloj.
