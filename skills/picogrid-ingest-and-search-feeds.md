---
name: Ingest and search Legion feed data
description: Push message feed data for an entity into Legion and then query it back by search or latest-value.
api: Legion Platform API v3
base_url: https://legion-prod.picogrid.com
operations:
  - POST /v3/feeds/messages
  - POST /v3/feeds/search
  - GET /v3/feeds/entities/{entityId}/latest
---

# Ingest and search Legion feed data

Use this skill to send sensor/telemetry data into Legion as feed data and read it back.

## Auth
1. Obtain an OAuth 2.0 access token from the Keycloak `legion` realm using the
   client-credentials grant:
   `POST https://auth.legion-prod.picogrid.com/realms/legion/protocol/openid-connect/token`.
2. Send `Authorization: Bearer <token>` on every request. Operations are
   organization-scoped — ensure your client/token carries the correct
   organization context (`org:operator` or higher for writes).

## Steps
1. **Ingest** — `POST /v3/feeds/messages` with the message feed payload referencing
   the target entity and feed definition. (For file payloads use
   `POST /v3/feeds/files` with `multipart/form-data`.)
2. **Read latest** — `GET /v3/feeds/entities/{entityId}/latest` to get the most
   recent feed data across all definitions for an entity, or
   `GET /v3/feeds/entities/{entityId}/definitions/{feedDefinitionId}/data/latest`
   for a specific definition.
3. **Search** — `POST /v3/feeds/search` with structured filters + pagination to
   query historical feed data.

## Conventions & errors
- List/search endpoints support complex filtering and pagination.
- On failure Legion returns a custom JSON envelope
  `{status, code, message, category, timestamp, trace_id}` — log the `trace_id`
  and surface `category` (e.g. `RESOURCE_NOT_FOUND`, `FORBIDDEN`). Errors are not
  RFC 9457.
- `401` → refresh the token; `403` → missing organization permission/scope.
