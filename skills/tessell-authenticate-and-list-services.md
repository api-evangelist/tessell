---
name: Authenticate and list Tessell DB services
description: >-
  Exchange a tenant API key for a bearer token, then enumerate the DB services in a Tessell
  tenant with pagination and expansion flags.
api: openapi/tessell-openapi-original.json
generated: '2026-07-21'
method: generated
operations:
  - authorizeApiKey
  - getTessellServices
  - getTessellService
---

# Authenticate and list Tessell DB services

1. **Get a bearer token** — call `authorizeApiKey` (`POST /iam/authorize`) with headers
   `tenant-id: <your tenant id>`, `content-type: application/json`, and your API key in the
   request body. The response contains the access token. There is no OAuth flow; the only
   security scheme is `BearerAuth` (HTTP bearer).
2. **Send the token on every call** — `Authorization: <token>` against the documented endpoint
   `https://api.console.tessell.com`.
3. **List services** — call `getTessellServices` (`GET /services`). Page with `page-size`
   (default 10) and `page-offset` (default 0); filter with `name`, `statuses`, `engine-types`;
   control payload size with the boolean expansion flags `load-instances` (default true),
   `load-databases`, and `load-updates-info`. Timestamps honor the `time-zone` parameter
   (default UTC).
4. **Fetch one service** — call `getTessellService` (`GET /services/{id}`) with the service UUID.

## Rules

- There is **no idempotency-key contract** — do not retry non-GET calls blindly; check state
  first (see `conventions/tessell-conventions.yml`).
- Every error returns the common `ApiError` envelope (`code`, `message`, `resolution`,
  `timestamp`, `contextId`, `sessionId`, `tessellErrorCode`) — log `contextId` when reporting
  failures (see `errors/tessell-problem-types.yml`).
