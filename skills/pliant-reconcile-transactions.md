---
name: Reconcile transactions and attach receipts
description: Pull new transactions, enrich the accounting view, attach receipts, and mark them synced back to Pliant.
api: https://customer-api.getpliant.com
operations: [get-transactions, get-accounting-transactions-batch, upload-receipt, update-transaction-partner-submission-status-batch]
---

# Reconcile transactions and attach receipts

Sync card spend into your accounting/ERP system and close the loop back to Pliant.

## Prerequisites
- OAuth 2.0 client-credentials bearer token (`Authorization` header).
- `Pliant-API-Version` header on every request.

## Steps
1. `get-transactions` — page through transactions (offset pagination: `limit`,
   `page`, `sortBy`, `sortDirection`, 50/page default). Filter by
   `partnerSubmissionStatus` to fetch only what you have not synced yet.
2. `get-accounting-transactions-batch` — fetch the accounting view (custom fields,
   project, team) for those transaction IDs.
3. `upload-receipt` — attach receipts to a transaction, or use
   `upload-receipt-to-automatcher` to let Pliant match a receipt to the right
   transaction asynchronously.
4. `update-transaction-partner-submission-status-batch` — mark transactions as
   submitted/synced once written to your system.

## Rules
- Only finally-booked movements appear as account entries; pending transactions
  are excluded from statements/account entries.
- Automatching is **asynchronous** — react to the callback or poll
  `get-receipt-automatcher-status`.
- Rate limit 900/5min/IP → 429. See `conventions/pliant-conventions.yml`.
- See `data-model/pliant-data-model.yml` for the transaction ↔ accounting ↔
  receipt relationships.
