# carje

**A workshop system for car garages.**

carje tracks every car a workshop services, every spare part changed, the parts stock, and when each car's next
service is due. Staff work from a phone or desk in the workshop; customers get a read-only portal with their own car
history and email reminders. One installation serves many workshops, each fully isolated from the others.

## Screenshots

Screenshots are to come.

## What's inside

- **Cars and customers** — a record per customer and car (plate, mileage, history), with a digital service passport
  and a vehicle checkup that predicts the next service and suggests parts.
- **Service jobs** — open, work and close a job with parts and labour lines. Closing a job takes stock and ticks the
  service rules it covered. Mechanics see parts and quantities but never prices.
- **Stock** — an append-only stock ledger, receiving and adjustments, low-stock and negative-stock warnings,
  suppliers, purchase orders and barcode lookup.
- **Next service due** — per-workshop rules by distance or months, a due list, and email reminders that never repeat
  within 30 days.
- **Money** — quotes, invoices and payments with per-workshop tax settings, warranty tracking, reports and CSV export.
  Amounts are whole sen, never floats.
- **Customer side** — a portal with car history, due dates and a self-service mileage update, plus online booking.
- **Workshop tools** — job photos, branches, roles (owner, admin, mechanic, customer), audit log, backups and
  offline-friendly pages.

## Architecture

See [`ARCHITECTURE.md`](./ARCHITECTURE.md).

## What this repository is

This is the **public** showcase of carje. The application code lives in a private repository. This repo holds only
the product description, the architecture overview, the version table and screenshots — no source code, keys,
tokens, internal URLs or personal data.
