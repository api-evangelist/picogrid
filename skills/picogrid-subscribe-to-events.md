---
name: Subscribe to Legion events and receive notifications
description: Create an event subscription with a delivery channel, then search events and read the resulting notifications.
api: Legion Platform API v3
base_url: https://legion-prod.picogrid.com
operations:
  - POST /v3/event-subscriptions
  - POST /v3/event-subscriptions/channels
  - POST /v3/events/search
  - GET /v3/notifications
---

# Subscribe to Legion events and receive notifications

Use this skill to set up asynchronous event delivery from Legion.

## Auth
- OAuth 2.0 bearer token from the Keycloak `legion` realm with the correct
  organization context (`org:viewer`+ to read, operator+ to create subscriptions).

## Steps
1. **Create a subscription** — `POST /v3/event-subscriptions` describing which
   events to route.
2. **Attach a delivery channel** — `POST /v3/event-subscriptions/channels` to add
   a channel to the subscription; verify with
   `GET /v3/event-subscriptions/{eventSubscriptionId}/channels/all`.
3. **Browse/search events** — `GET /v3/events` or `POST /v3/events/search` with
   filters + pagination.
4. **Read notifications** — `GET /v3/notifications` for delivered events; inspect
   per-channel deliveries (`GET /v3/notifications/deliveries/{notificationDeliveryId}`)
   and acknowledge per-user receipts
   (`PUT /v3/notifications/{notificationId}/users/{userId}`).

## Notes
- This is Legion's webhook/event surface (see asyncapi/picogrid-events-webhooks.yml).
- Errors use the custom `{status, code, message, category, timestamp, trace_id}`
  envelope; log `trace_id`.
