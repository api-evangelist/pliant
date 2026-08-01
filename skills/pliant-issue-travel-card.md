---
name: Issue a travel virtual card with controls
description: Issue a virtual travel card for a cardholder, apply card controls and a limit, then verify it is active.
api: https://customer-api.getpliant.com
operations: [get-available-cards, issue-card, update-card-controls-by-card, update-card-limit]
---

# Issue a travel virtual card with controls

Use the Pliant Pro API to issue an on-demand virtual card for a trip/booking and
scope its spend. Card issuing is **asynchronous** unless you use the instant
endpoints — rely on the `CARD_ACTIVATED` callback before using the card.

## Prerequisites
- OAuth 2.0 client-credentials bearer token in the `Authorization` header
  (see `authentication/pliant-authentication.yml`). Handle the token flow on a
  secure backend only.
- An active cardholder (`cardholderId`) — see the onboard-cardholder skill.
- Include the `Pliant-API-Version` header on every request.

## Steps
1. `get-available-cards` — list the `cardConfig`s issuable for the organization;
   pick a `TRAVEL` config.
2. `issue-card` — issue the card for the `cardholderId` with the chosen
   `cardConfig` and initial `cardControls`. (Instant/synchronous variants:
   `issue-card-instant`, `issue-card-instant-with-pci` — only if pre-aligned with
   Pliant and PCI-DSS certified.)
3. `update-card-controls-by-card` — set/refine merchant-category, per-transaction
   and other controls. Provide all values you want set; omitted values are removed.
4. `update-card-limit` — set the limit (and any `additionalLimits`). Returns
   `204 No Content`; read state via card details or the `CARD_LIMITS_CHANGED`
   callback.

## Rules
- Wait for the `CARD_ACTIVATED` callback before using the card (async issuing).
- Rate limit: 900 requests / 5 min / IP → HTTP 429. See
  `conventions/pliant-conventions.yml`.
- Errors are string codes (e.g. `ISSUANCE_LIMIT_EXCEEDED`, `NOT_ACTIVE_ORG`,
  `LOCKED_BY_CARD_CONTROL`) — see `errors/pliant-error-codes.yml`.
- Copilots and travel validity periods are `TRAVEL`-only features.
