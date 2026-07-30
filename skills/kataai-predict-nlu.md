---
name: Predict entities with an NLU model
description: Call a deployed Kata.ai NL Studio model to extract entities from a sentence.
api: openapi/kataai-nlu-prediction-openapi.yml
operations: [predict]
---

# Predict entities with an NLU model

Serve a trained NL Studio model to any application through the NL Prediction API (base URL `https://geist.kata.ai`).

## Steps

1. **Get a token** — In NL Studio, open Settings and copy the API token. Send it as `Authorization: Bearer <token>`.
2. **Predict** — `predict` (`POST /nlus/{nluId}/predict`) with `{ "text": "<sentence>" }`, using your model's `nluId`.
3. **Read the result** — The response is `{ result: { <entityName>: [ { type, start, end, score, value, label?, belongsTo?, resolved? } ] } }`. `type` is one of `trait | dict | phrase`; `dict` entities may include a `resolved.dictKey`; related entities carry `belongsTo` linking value to another entity by name/index.

## Rules
- The model must be trained and deployed in NL Studio; `nluId` identifies it.
- Confidence is in each entity's `score` (0..1); apply your own threshold before acting on low-confidence extractions.
