# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este repo

FlowSync es el proyecto de un ejercicio del curso (el `README.md` es la lección, generada: no se edita a mano). Es un monorepo TypeScript de punta a punta con dos apps independientes, cada una con su propio `package.json` y `node_modules`:

- `backend/`: API en AdonisJS 7 (Lucid + SQLite, VineJS, auth por access tokens). **Ya existe y en este ejercicio no se modifica**: se lee para saber qué exige.
- `frontend/`: React 19 + Vite, todavía con la plantilla por defecto. Aquí es donde se trabaja. El ticket pide shadcn/ui, pero aún no están instalados ni Tailwind ni shadcn ni un router.

Requiere **Node.js 24+** (con versiones anteriores el backend falla con `Unknown file extension ".ts"`).

## Comandos

Backend (desde `backend/`):

```bash
npm install && cp .env.example .env && node ace generate:key   # primera vez
node ace migration:run      # crea tmp/db.sqlite3 y regenera database/schema.ts
npm run dev                 # node ace serve --hmr, en http://localhost:3333
npm test                    # node ace test (suites unit y functional de Japa)
node ace test --files tests/functional/foo.spec.ts   # un solo archivo
node ace test --tests "nombre del test"              # un solo test
npm run lint | npm run typecheck | npm run format
```

Aún no hay tests: las suites esperan `tests/unit/**/*.spec.ts` y `tests/functional/**/*.spec.ts` (la funcional arranca el servidor HTTP).

Frontend (desde `frontend/`, en otra terminal):

```bash
npm run dev       # Vite en http://localhost:5173
npm run build     # tsc -b && vite build (también hace el type-check)
npm run lint      # oxlint
```

El frontend no trae formateador ni tests.

## Arquitectura del backend

Flujo de una petición: `start/routes.ts` → controlador (`app/controllers`) → validador VineJS (`app/validators`) → modelo Lucid (`app/models`) → transformer (`app/transformers`) → `ctx.serialize(...)`.

- **Rutas** bajo `/api/v1`:
  - `POST /auth/signup` y `POST /auth/login` (públicas): devuelven `{ data: { user, token } }`.
  - `GET /account/profile` y `POST /account/logout`: requieren `Authorization: Bearer <token>` (los tokens empiezan por `oat_`).
- **Lo que el backend exige y el ticket de producto no dice** (`app/validators/user.ts`):
  - signup: `email` (válido, ≤254, único), `password` (8–32 caracteres), **`passwordConfirmation` obligatorio e igual a `password`**, `fullName` opcional/nullable.
  - login: `email` y `password`.
- **Forma de las respuestas**: `providers/api_provider.ts` añade `ctx.serialize`, que envuelve todo en `{ data: ... }`. El transformer de usuario solo expone `id, fullName, email, createdAt, updatedAt, initials` (nunca el hash).
- **Errores**: `force_json_response_middleware` fuerza JSON en todas las respuestas. Los fallos de validación (incluido el email ya registrado) llegan como 422 con `{ errors: [{ message, field, rule }] }`; las credenciales inválidas, como error `E_INVALID_CREDENTIALS`.
- **Auth**: el guard por defecto es `api` (access tokens en la tabla `auth_access_tokens`). Existe un guard `web` de sesión que no se usa en las rutas.
- **CORS**: en desarrollo acepta cualquier origen, así que el frontend puede llamar directamente a `http://localhost:3333`.
- **Imports**: se usan los subpath imports de `package.json` (`#controllers/*`, `#models/*`, `#validators/*`, etc.), no rutas relativas.
- **Código generado, no editar a mano**: `database/schema.ts` (lo regenera `migration:run` a partir de las migrations; los modelos extienden de él, p. ej. `User extends compose(UserSchema, ...)`) y `.adonisjs/` (índice de controladores `#generated/controllers` y registro de rutas de Tuyau, generados por los hooks de `adonisrc.ts`). Los cambios de esquema van siempre en una migration nueva.

## Entregables del ejercicio

Se entregan en la rama `harness-<iniciales>`, con un PR contra `LIDR-academy/flowsync-ai4devs-202609-seniors-2`: `prompts.md` (en la raíz; los prompts se pegan tal cual se lanzaron, con modelo y herramienta, incluidos los que fallaron) y `docs/harness/comparacion.md`.
