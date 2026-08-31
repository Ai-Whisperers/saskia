# Session notes — 2026-09 — prototype lineage (Flask → workbook → RMS)

## Context

Future-you (or any agent reading this repo cold) should know the chain
of artifacts that produced the current engagement. There are **three
distinct products** in the Saskia story, in chronological order:

## 1. Prototype — `IvanWeissVanDerPol/Saskia` (2025-07)

The Flask app Ivan built in July 2025. Single commit, ~70 files,
targets `localhost:5000`, sample ingredients (café, leche, azúcar,
harina, huevos — generic, not her real recipes), pre-Asunción San
Lorenco framing. **No real data, no bank info, no PII of any kind.**

**Status:** archived 2025-07-04. Public, single commit, zero forks,
zero stars, zero external references.

**Not in the engagement.** The 70-hour dev plan
(`docs/plans/2026-08-31-rms-fase-1-dev-plan.md`) §2 explicitly says:
*"Reuse archived `IvanWeissVanDerPol/Saskia` Flask bakery as the
product → No — wrong era; use as **reference only** if useful."*

**Audit (2026-09):** PII scan returned zero matches for home address,
bank data, CI, WhatsApp number, email. Safe to leave archived.

## 2. Workbook system — `saskia-personal-context/04_foodbiz-management-system/` (2026-07)

The HEREBUS Excel workbook system. Built across July 2026 in
`saskia-personal-context` (private-by-design, public-by-decision).
5 xlsx files, 11 Python reproducer scripts, 8 recipe docs, 11 process
docs. **This is what Saskia has actually been working in** since
mid-July 2026.

**Source-of-truth rule** (from `04_foodbiz-management-system/AGENTS.md`):
Python regenerates xlsx, sheet protection on calculated cells,
Guaraní integers (no `.00` decimals), no WhatsApp bot in the repo.

**Inventory:** 63 unique ingredientes en español, 20 Recipe_* tabs
(7 legacy + 10 new + 3 sub-recetas), 11 process docs, 6 tab docs,
8 recipe docs. Yields empty — Saskia fills as she cooks. Prices
empty — Saskia fills after shopping.

**This is what the new RMS app imports as its v1 catalog.**

## 3. New RMS app — `saskia/app/` (2026-08-31 plan, build pending)

The fase 1 deliverable for the signed quote (Gs. 17.500.000 / 24
cuotas, no hosting). FastAPI + SQLite + openpyxl, Spanish (vos)
UI, integer Gs., runs at `127.0.0.1:8765`. **Local on Saskia's PC,
no public URL, no login, no monthly hosting.**

**Code home:** `saskia/app/` (in this engagement repo). **NOT in
`saskia-personal-context`** — the personal-context Python scripts
must NOT silently replace her live SQLite.

**Replaces (1) and (2):** both stay in place for v1 catalog import
and audit, but going forward the SQLite DB + browser UI is the
operational system.

## Status

- (1) `IvanWeissVanDerPol/Saskia`: archived, no action needed
- (2) `saskia-personal-context/04_foodbiz-management-system/`: live,
  source for v1 catalog
- (3) `saskia/app/`: planned, 70 hours, not started (gated on quote
  signoff + first cuota + named PC + Drive access)

[reconstructed session note: written from commit metadata + repo
hierarchy. Operator should verify dates and details.]
