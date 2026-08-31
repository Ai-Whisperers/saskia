# Session notes — 2026-08-31 — 70-hour dev plan locked

## Context

The RMS fase 1 development plan (`docs/plans/2026-08-31-rms-fase-1-dev-plan.md`)
was authored and merged into the new `Ai-Whisperers/saskia` repo. Quote is
ready for signoff; production does not start until first cuota + Drive + PC.

## Locked product

RMS local on Saskia's PC: products, prices, recipes, inventory, sales, margins.
FastAPI + SQLite + openpyxl, Spanish (vos) UI, integer Gs., runs at
`127.0.0.1:8765`. No hosting. No monthly fee. No login. No public URL.

## Locked price

Gs. 17.500.000 in 24 cuotas (Gs. 729.167/month). Extra hours at
Gs. 250.000/h (this client only; AIW list rate stays USD 50/h).
Planning assistant Gs. 9.500.000 is parked — **not** in the signed quote.

## Stack

Python 3.13 · FastAPI · SQLite · openpyxl · server-rendered HTML.
See the plan file for full detail.

## Status

- Quote at `Company-Information/docs/clients/2026-08-18-saskia-weiss-vander.md`
  ready to send.
- Build plan: complete and self-contained (acceptance criteria, 10 tasks,
  hour budget, risks).
- Pre-build blockers: signed quote + first cuota + named PC + Drive access.
