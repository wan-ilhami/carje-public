# Architecture

```
 browser (Next.js)                  backend (NestJS)                       services
 ┌──────────────────────┐  /api/v1  ┌──────────────────────────────┐
 │ staff pages, per role │◄────────►│ controller  HTTP, DTOs, roles │──► Postgres (row-level security)
 │ customer portal       │ rewrite  │ service     business rules   │──► S3-compatible photo storage
 │ public booking, status│          │ repository  Drizzle, tenant  │──► Redis (rate limits, cache)
 └──────────────────────┘          └──────────────────────────────┘──► mail (SMTP in dev, API in prod)
```

The browser talks to one origin. The frontend rewrites `/api/v1/*` to the backend, so the session cookie is same-site:
no CORS and no tokens in JavaScript. The frontend holds no database access and no auth logic.

## Backend — module per feature

- **controller → service → repository.** Controllers handle HTTP and role checks, services hold the rules,
  repositories are the only code that touches the database. Controllers never import the ORM.
- **Auth** — single sign-on through an OIDC provider (code flow with PKCE), owned by the backend. Sessions are opaque
  cookies; only a hash of the token is stored.
- **Roles** — owner, admin, mechanic and customer. Permissions are a data table checked by one decorator, and a
  route without a permission is refused by default.
- **Pure rules** — totals, tax rounding, due status and stock sums are plain functions with unit tests.

## Tenant isolation — two walls

Every tenant table carries a workshop id. The repository layer always takes a tenant context, and Postgres row-level
security enforces the same rule. Each request runs in a transaction that sets the workshop for the database. The
application connects as a role that cannot bypass row-level security, and composite foreign keys keep related rows in
the same workshop. A bug in one wall does not leak data through the other.

## Data and money

- Hand-written SQL migrations on Postgres, queried through Drizzle.
- Money is stored as whole sen, tax in basis points, with one rounding step per document.
- Stock is an append-only ledger; quantity on hand is a sum, never a stored counter.
- Close, void and receive accept an idempotency key, so a retried request cannot apply twice.

## Reminders and jobs

A secret-protected cron endpoint sends due-service reminders. A per-workshop advisory lock stops the cron and a manual
"send now" from overlapping, and a sent-log prevents repeat emails.

## Quality

Vitest unit and integration tests run on a real Postgres. Playwright journeys drive the app in a browser, including
accessibility checks. Biome handles lint and format. The OpenAPI schema from the backend generates the frontend's
typed API client.
