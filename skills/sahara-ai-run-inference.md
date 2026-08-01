---
name: Run OpenAI-compatible inference on Sahara AI
description: >-
  Discover an available model and provider on the Sahara AI compute network,
  then run an OpenAI-compatible chat completion against it.
api: openapi/sahara-ai-compute-openapi.yml
operations:
  - listModels
  - listProviders
  - getModelDetail
  - createChatCompletion
---

# Run inference on the Sahara AI Compute API

The Sahara AI Compute API is an OpenAI-compatible inference gateway that routes
requests across upstream providers (OpenAI, Lepton, Together, and others).

## Authentication

Obtain an API key from the Developer Portal
(`app.saharaai.com/developer/api`). Send it either as an `x-api-key` header or,
for OpenAI-compatible clients, as a Bearer token in the `Authorization` header.
Never expose the key in public code or repositories.

Base URL: `https://app.saharaai.com/developer/api`

## Steps

1. **Discover models** — call `listModels` (`GET /compute/models`). Optionally
   filter with `?provider={name}`.
2. **Discover providers** — call `listProviders` (`GET /compute/providers`).
   Filter to providers serving a model with `?model={name}`.
3. **(Optional) Inspect a model** — call `getModelDetail`
   (`GET /compute/modelDetail`) for metadata and usage requirements of a
   model-provider pair.
4. **Run inference** — call `createChatCompletion`
   (`POST /compute/chat/completions`) with a `model` and `messages` array.
   Select the upstream provider with the `OpenAI-Organization` header. Set
   `stream: true` for streaming responses.

## Conventions and error handling

- OpenAI-compatible: works with the OpenAI SDK (`base_url` + `api_key`),
  LangChain, and the OpenAI Agents SDK.
- Errors return an OpenAI-style envelope `{ error: { message, type, code } }`.
  `400` = bad request formatting, `404` = unknown pipeline/model, `500` =
  retry then contact support.
- No idempotency key and no pagination are documented; discovery endpoints
  return full collections.
