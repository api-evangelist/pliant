---
name: Subscribe to real-time callbacks
description: Register callback (webhook) subscriptions for card and transaction events, verify signatures, and handle at-least-once delivery.
api: https://customer-api.getpliant.com
operations: [bulk-upsert-subscriptions, get-callback-subscriptions, delete-subscription]
---

# Subscribe to real-time callbacks

Receive push notifications for card lifecycle, balance/limit and statement events.

## Prerequisites
- OAuth 2.0 client-credentials bearer token (`Authorization` header).
- A public HTTPS endpoint to receive `POST` callbacks.

## Steps
1. `bulk-upsert-subscriptions` — register subscriptions for the event types you
   need (e.g. `CARD_ISSUED`, `CARD_ACTIVATED`, `CARD_STATUS_CHANGED`,
   `CARD_BALANCE_UPDATED`, `CARD_LIMITS_CHANGED`, `STATEMENT_CSV_DOWNLOAD_READY`)
   with your callback URL. Multiple subscriptions per event type are allowed when
   URLs differ (max 42 per event type).
2. `get-callback-subscriptions` — confirm what is currently subscribed.
3. `delete-subscription` — remove a subscription by ID when no longer needed.

## Rules
- Delivery is **at-least-once** — deduplicate on `X-Callback-Id` and make your
  receiver idempotent (see `conventions/pliant-conventions.yml`).
- Verify authenticity with the **Ed25519 signature headers** (or enable OAuth 2.0
  callback auth). Callback headers include `X-Organization-Id`, `X-Callback-Id`,
  `X-Callback-Created-At`.
- Ordering is not guaranteed; 95% of callbacks arrive within 10 seconds.
- Some endpoints (e.g. `get-statement-csv`) require an active subscription for the
  relevant event type before they will succeed.
- See `asyncapi/pliant-callbacks-webhooks.yml` for the full webhook surface.
