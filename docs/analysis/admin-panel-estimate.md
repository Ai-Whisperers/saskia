# Admin panel / industrial ops — phased estimate

**Last updated:** 2026-08-12 (banner 31 Aug 2026)  
**Status:** **Historical estimate.** Fase 1 is now the **local RMS** at Gs. 17.500.000 / 24 cuotas — see [CURRENT-CONTEXT.md](../CURRENT-CONTEXT.md). Do not sell from the ranges below.  
**Context:** Meeting audio described many databases/workflows; she wants numbers to prioritize.

---

## Reality check

Much of the “admin panel” already exists as **Excel workbooks + processes** in `saskia-personal-context`.  
A full web admin with separate DBs for every entity is a **different product** (P2+).

---

## Phase map

### Phase 0 — Excel ops (current P0)

Already partially built: inventory, recipes, production, waste, suppliers, analysis, comparison, calendar MVP.

| Work | Effort | Notes |
|---|---|---|
| Seal + policy + one work path | S | Docs + process |
| Fill prices / V1 recipes / one week plan | S–M | Data entry + coaching |
| Simple sales log | S | New tab or sheet |
| WhatsApp MVP A/B | S–M | Config + test |

**Rough team effort:** days–1–2 weeks calendar, not a multi-month build.

---

### Phase 1 — Light website + WA polish

| Work | Effort |
|---|---|
| Landing + menu + CTA WA | S–M |
| Hosting setup | S |
| Catalog sync discipline | S |

Aligns with hosting figures in PACKAGE-AND-PRICING (150–200k/mo or ~1.8M/yr).

---

### Phase 2 — Web admin (modules)

Modules named in the meeting → suggested order:

| Module | Depends on | Effort | Priority for her stated goal |
|---|---|---|---|
| Ingredients / inventory DB | — | M | High (if leaving Excel) |
| Products / recipes DB | Ingredients | M | High |
| Orders | Products + WA | M–L | High |
| Purchases / suppliers | Ingredients | M | Medium |
| Waste | Inventory | S–M | Medium |
| Production / purchase planning | Orders + inventory | L | High |
| Schedules / calendar live | OAuth | M | Medium |
| Invoices | Orders + tax regime | L | Low now (legal deferred) |
| Users / customers | Orders | M | Medium |
| Marketing workflow tooling | Content assets | M | Later |

**Ballpark:** multi-week to multi-month depending on how many modules ship together.  
Present as **à la carte phases**, not one “industrial structure” invoice.

---

### Phase 3 — Agents per workflow (not a Saskia SKU)

Do **not** sell dedicated Hermes / isolated VPS as Saskia’s product. That is **John**. Agent-chat bleed is an ops issue, not this quote.

---

## Suggested quote conversation

1. Close **Phase 0** fixed-scope price (P0 package).  
2. Offer **Phase 1** site + hosting as add-on.  
3. Show **Phase 2** module menu with S/M/L effort — she ranks.  
4. Park **Phase 3** until she asks.

---

## Open estimate inputs

- Exact V1 SKU count  
- Sheets vs Excel vs web preference  
- Whether she keeps Excel forever for kitchen use  
