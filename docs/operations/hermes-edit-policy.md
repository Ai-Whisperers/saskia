# Hermes edit policy (Saskia foodbiz)

**Last updated:** 2026-08-12  
**Status:** Draft — confirm allow/deny with Saskia  
**Applies to:** Agents (Hermes / Erebus / Cursor) touching her admin files  
**Workbook home:** `Ai-Whisperers/saskia-personal-context` → `04_foodbiz-management-system/`

---

## Goal

Hermes must **not** freely rewrite her admin files, but may **propose / apply approved updates** later for production and sales.

---

## Never without explicit OK (denylist)

| Area | Examples |
|---|---|
| Selling prices | Retail / wholesale / margins |
| Ingredient costs | BulkPrice, pkg qty once she set them |
| Recipe formulas & quantities | Recipe_* ingredient rows, yields she locked |
| Product names / catalog identity | Renames, deletes of recipes |
| Personal / banking / identity | Anything under `00_identity`, `01_banking-accounts` |
| Silent overwrite of sealed xlsx | Direct binary replace without PR/preview |

---

## Allowed later with rules (allowlist)

Only after Step 2 gate is live and she opts in:

| Area | Notes |
|---|---|
| Production planner rows | New day / batch entries |
| Waste / merma rows | One event per row |
| Stock counts after shopping | Inventory quantities she confirms |
| Sales log rows | New sale entries |
| **Proposals** for price changes | Diff only — she approves before apply |

---

## Hard technical rules (already in personal-context)

1. **Python is source of truth** for workbook structure/formulas — regenerate, don’t hand-edit calculated cells.  
2. Sheet protection on calculated cells.  
3. Guaraní **integers**, no `.00` decimals.  
4. No WhatsApp bot implementation inside personal-context without explicit scope change.  
5. No live customer PII in workbooks committed to git.

---

## Approval gate (procedure)

1. Agent prepares change as: JSON/CSV patch **or** branch PR on personal-context.  
2. Human (Saskia or K.W.) reviews preview.  
3. Apply only after OK (message / checkbox / PR merge).  
4. Session note under `02_sessions/` (personal-context) or `docs/sessions/` (this repo).  

**Forbidden:** agent overwrites `data/*.xlsx` on `main` without review.

---

## Context bleed

Her Hermes chats may share company context (e.g. unrelated clients). That is an **agent-chat** issue, not a product to sell her.

**Dedicated Hermes on a private VPS is John’s SKU.** Do not quote “Wesley-style isolated Hermes” to Saskia. Her paid product is the **local RMS on her PC**.

---

## Confirmation checklist (with Saskia)

- [ ] Denylist OK  
- [ ] Allowlist OK (or “nothing automatic yet”)  
- [ ] Approval channel: WhatsApp / PR / both  
- [ ] Who can approve: Saskia only / Saskia + K.W.  
