---
name: Provision a Tessell DB service and track it to ready
description: >-
  Provision a new database service (Oracle, PostgreSQL, MySQL, SQL Server, MongoDB, Milvus, or
  Apache Kafka engine) and poll its timeline until it is ready, then manage start/stop.
api: openapi/tessell-openapi-original.json
generated: '2026-07-21'
method: generated
operations:
  - authorizeApiKey
  - provisionTessellService
  - getTessellService
  - getTessellServiceTimeline
  - startTessellService
  - stopTessellService
  - resizeDBService
  - deleteTessellService
---

# Provision a Tessell DB service and track it to ready

1. **Authenticate** with `authorizeApiKey` (see the authenticate-and-list skill).
2. **Provision** — call `provisionTessellService` (`POST /services`) with the service payload
   (engine type, subscription, compute/storage shape, network, availability settings).
   Provisioning is **asynchronous**: the call is accepted and the service moves through states.
3. **Track progress** — poll `getTessellService` for `status`, and `getTessellServiceTimeline`
   (`GET /services/{id}/timeline`) for the ordered activity feed, until the service reaches READY.
4. **Operate** — `stopTessellService` (`PATCH /services/{id}/stop`) and `startTessellService`
   (`PATCH /services/{id}/start`) control runtime; `resizeDBService` (`PATCH /services/{id}/resize`)
   changes compute/storage.
5. **Tear down** — `deleteTessellService` (`DELETE /services/{id}`) removes the service.

## Rules

- Long-running operations are **async tasks** — never treat the initial 2xx as completion; poll
  the service status/timeline.
- **No idempotency keys**: if a provision call times out, list services by `name` before
  re-submitting to avoid duplicates.
- Errors arrive in the `ApiError` envelope; `resolution` often carries the remediation hint and
  `tessellErrorCode` the stable code.
