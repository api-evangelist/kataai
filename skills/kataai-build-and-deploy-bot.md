---
name: Build and deploy a Kata.ai bot
description: Create a Kata Platform project, push a bot revision, cut a deployment version, and bind it to an environment.
api: openapi/kataai-platform-openapi.yml
operations: [login, createProject, createBotRevision, createDeploymentVersion, createEnvironment]
---

# Build and deploy a Kata.ai bot

Use the Kata Platform Public API (base URL `https://api.kata.ai`) to stand up a bot from scratch. All requests send `Content-Type: application/json` and `Accept: application/json`. Authenticate with a bearer token; the API rate limit is 100 requests/minute (HTTP 429 when exceeded).

## Steps

1. **Authenticate** — `login` (`POST /login`) with `{ username, password }`. Save `Token.id` and send it as `Authorization: Bearer <token>` on every subsequent call.
2. **Create a project** — `createProject` (`POST /projects/`) with a `name` and `options` (`bot`, `cms`, `nlu`, `timezone`, `nluVisibility`, `nluLang`). One project holds one Bot, CMS, and/or NLU. Keep the returned `id` as `projectId`.
3. **Push a bot revision** — `createBotRevision` (`POST /projects/{projectId}/bot/revisions/`) with the `Bot` definition (flows, intents, states, nlus). Updating a bot always creates a new revision; a duplicate version is rejected.
4. **Create a deployment version** — `createDeploymentVersion` (`POST /projects/{projectId}/deployment/versions`) with `{ version, botRevision }` referencing the revision hash from step 3.
5. **Bind an environment** — `createEnvironment` (`POST /projects/{projectId}/environments`) with `{ depId, depVersion, name, slug }` to point a named environment (e.g. Development) at the deployment version.

## Rules
- Bots and deployments are immutable-by-version: iterate by creating new revisions/versions, not in-place edits. There is no idempotency-key header, so guard retries against duplicate version errors.
- A `403` means the token lacks the RBAC permission (e.g. `create_own_projects`); a `400` means a malformed request.
