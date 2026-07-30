---
name: Connect a messaging channel
description: Attach a messaging channel (LINE, Telegram, WhatsApp, etc.) to a deployed Kata.ai bot environment.
api: openapi/kataai-platform-openapi.yml
operations: [login, listEnvironments, createChannel, listChannels]
---

# Connect a messaging channel

Once a bot is deployed to an environment, attach a messaging channel so end users can talk to it. Base URL `https://api.kata.ai`; bearer-token auth; JSON only.

## Steps

1. **Authenticate** — `login` (`POST /login`); use the returned token as `Authorization: Bearer <token>`.
2. **Find the environment** — `listEnvironments` (`GET /projects/{projectId}/environments`) and choose the target `environmentId`. Paginate with `limit` and `page`; read `page/limit/total/data`.
3. **Create the channel** — `createChannel` (`POST /projects/{projectId}/environments/{environmentId}/channels`) with a `Channel`: `name`, `type` (one of `generic, line, fbmessenger, telegram, twitter, slack, spark, bbm, qiscus, whatsapp`), `url`, and channel `options` (tokens/secrets for that platform). The response includes a generated `webhook` URL (e.g. `https://kanal.kata.ai/receive_message/<id>`) to configure on the messaging platform.
4. **Verify** — `listChannels` (`GET .../channels`) to confirm the channel is attached and read its `rpmLimit`.

## Rules
- Configure the returned `webhook` URL on the upstream messaging platform to route inbound messages to the bot.
- Each channel carries its own `rpmLimit`; the platform-wide API limit is 100 requests/minute.
