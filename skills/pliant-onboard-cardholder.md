---
name: Onboard a cardholder
description: Create a cardholder either by invitation (app access) or direct registration (API-only), then confirm activation.
api: https://customer-api.getpliant.com
operations: [invite-cardholders, register-cardholders, get-cardholder-details, get-cards]
---

# Onboard a cardholder

Create the person (or technical/travel identity) that cards are issued to.

## Prerequisites
- OAuth 2.0 client-credentials bearer token (`Authorization` header).
- `Pliant-API-Version` header on every request.

## Steps
1. Choose the onboarding path:
   - `invite-cardholders` — sends an invite email; the cardholder registers with
     Pliant and can use the Pliant web/mobile apps. You get a callback when fully
     registered.
   - `register-cardholders` — creates an **active** cardholder directly, without
     Pliant app access (API-only integrations, or a purely technical travel
     cardholder). Wait for the registration-finished callback before use.
2. `get-cardholder-details` — confirm the cardholder is active (poll or react to
   the callback).
3. `get-cards` — list cards once you begin issuing (see the issue-travel-card
   skill).

## Rules
- Registration/invitation completes **asynchronously** — do not issue cards until
  the cardholder is active (a card issued to an invited cardholder is parked and
  auto-issued on activation).
- `USER_ALREADY_EXISTS` indicates the email is already a cardholder;
  `deactivate-cardholder` can re-free an email via a suffix option.
- See `conventions/pliant-conventions.yml` and `errors/pliant-error-codes.yml`.
