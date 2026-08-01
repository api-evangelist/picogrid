---
name: Dispatch a tasking command to an entity
description: Discover an entity's registered commands and dispatch one over MQTT, then poll task status. High-consequence — commands act on physical systems.
api: Legion Platform API v3
base_url: https://legion-prod.picogrid.com
operations:
  - GET /v3/tasking/entity/{entityId}/commands
  - POST /v3/tasking
  - GET /v3/tasking/{taskId}
---

# Dispatch a tasking command to an entity

Use this skill to command a physical entity through Legion tasking.

> SAFETY: Tasking dispatches real-world commands to physical/unmanned systems.
> Treat as high-consequence: require human-in-the-loop confirmation, use a
> short-lived least-privilege token (`org:operator`+), and never auto-dispatch
> stop/dispatch/control commands without explicit approval.

## Auth
- OAuth 2.0 bearer token from the Keycloak `legion` realm; the token must carry
  the entity's owning organization context with an operator-level permission.

## Steps
1. **Discover commands** — `GET /v3/tasking/entity/{entityId}/commands` to list the
   commands registered for the entity and their payload schemas. (Commands and
   their MQTT topics are registered via `POST /v3/tasking/entity/{entityId}/commands`
   and `POST /v3/tasking/entity/{entityId}/mqtt-topic`.)
2. **Validate payload** — build a payload that matches the schema for the chosen
   command; a mismatch returns a 422-class validation error.
3. **Dispatch** — `POST /v3/tasking` with the entity, the MQTT topic, and the
   payload. The command must already be registered.
4. **Track** — poll `GET /v3/tasking/{taskId}` for the task's current status.

## Errors
- Custom envelope `{status, code, message, category, timestamp, trace_id}`.
- `403 FORBIDDEN` → the token lacks operator permission on the entity's org.
- Always record the `trace_id` for audit of dispatched commands.
