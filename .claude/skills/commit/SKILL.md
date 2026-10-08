---
name: commit
description: Crea un commit con el formato tipo(scope): descripción a partir únicamente de los cambios ya preparados (staged). No añade archivos ni hace push, y pregunta antes de commitear si no hay cambios preparados o si duda del tipo o del scope.
disable-model-invocation: true
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git commit:*)
---

# commit

Genera **un** commit con los cambios preparados, con el formato `tipo(scope): descripción`.

## Reglas que no se rompen

- Usa **solo** lo que ya está en el índice. Nunca ejecutes `git add`, `git rm`, `git mv`, `git stash`, `git restore`, `git reset` ni `git commit -a` / `--all`, ni pases rutas a `git commit`.
- **Nunca** hagas `git push`.
- No uses `--amend`, `--no-verify` ni otras opciones que reescriban historia o se salten hooks.
- No toques archivos del working tree.

## 1. Comprobar qué está preparado

```bash
git status --short
git diff --cached --stat
git diff --cached
```

- Si `git diff --cached` está vacío, **no hay cambios preparados**: díselo al usuario, enséñale qué hay sin preparar (si hay algo) y pregúntale qué quiere hacer. No prepares nada tú y no hagas el commit.
- Los cambios sin preparar o sin seguimiento no forman parte del commit: ignóralos al redactar el mensaje (puedes mencionar que existen).

## 2. Decidir tipo y scope

**Tipo** (uno de):

| tipo | cuándo |
| --- | --- |
| `feat` | funcionalidad nueva |
| `fix` | corrección de un error |
| `docs` | solo documentación (README, `prompts.md`, `docs/`, `CLAUDE.md`…) |
| `style` | formato, sin cambio de comportamiento |
| `refactor` | reestructura sin cambiar comportamiento |
| `perf` | mejora de rendimiento |
| `test` | añade o corrige tests |
| `build` | dependencias, `package.json`, configuración de build |
| `ci` | integración continua |
| `chore` | mantenimiento que no encaja en lo anterior (p. ej. configuración de `.claude/`) |

**Scope**: la parte del repo afectada, en minúsculas y corta. Lo habitual aquí: `frontend`, `backend`, `harness` (skills, `.claude/`, `.mcp.json`, `docs/harness/`), o un scope ya usado en el historial (`git log --format=%s -30`, p. ej. `s1`). Prefiere los scopes existentes antes que inventar uno nuevo.

**Pregunta al usuario antes de commitear** (proponiendo tu mejor opción) si:

- los cambios encajan en más de un tipo, o en ninguno con claridad;
- tocan varias áreas sin un scope evidente, o el scope sería uno nuevo que no aparece en el historial;
- los cambios preparados parecen mezclar cosas sin relación (sugiere separarlos, pero no los separes tú).

Si no hay dudas, sigue sin preguntar.

## 3. Redactar el mensaje

- Formato de la primera línea: `tipo(scope): descripción`.
- Descripción en español, en minúsculas, en presente, sin punto final y de 72 caracteres como máximo en total (sigue el estilo del historial, p. ej. `docs(s1): enunciado del ejercicio y plantilla de prompts`).
- Cuerpo opcional, separado por una línea en blanco, solo si aporta el porqué o un detalle no obvio.
- Añade al final las líneas de atribución que indique el entorno (p. ej. `Co-Authored-By`), si las hay.

## 4. Hacer el commit

Usa un heredoc para conservar el formato:

```bash
git commit -F - <<'EOF'
tipo(scope): descripción

cuerpo opcional
EOF
```

Después muestra `git log -1 --stat` y confirma al usuario el hash y el mensaje. Si un hook falla, informa del error y no reintentes con `--no-verify`.
