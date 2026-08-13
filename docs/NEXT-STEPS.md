# Saskia — Clear following steps

**Last updated:** 2026-08-12  
**Based on:** WhatsApp kickoff (2026-08-05) + meeting audio (2026-08-12)  
**Repos:** https://github.com/Ai-Whisperers/saskia  
**Ops / Excels:** `Ai-Whisperers/saskia-personal-context` → `04_foodbiz-management-system/`  
**Doc index:** [README.md](README.md) · [INFO-FROM-SASKIA.md](INFO-FROM-SASKIA.md) · [DELIVERABLES.md](DELIVERABLES.md)

---

## Goal (what “done for now” means)

Saskia can **produce, sell via WhatsApp, and register numbers** without Hermes randomly rewriting her admin files — and the team has a **priced backlog** for what comes next (site / admin panel).

---

## Do this in order

### Step 0 — Align package (this week)

| # | Action | Owner | Done when |
|---|---|---|---|
| 0.1 | Confirm she buys **narrow P0** (seal + recipes/inventory/planning + WhatsApp), not full Gs. 2.5M “setup + constitution + Marga + site” | K.W. | Written yes/no on package scope |
| 0.2 | Send `docs/intake/Cuestionario-Saskia.docx` (or mark answered from 2026-08-12 audio) | K.W. | Answers in repo or WhatsApp summary filed under `docs/intake/` |
| 0.3 | Confirm business hub: cow email + Google Drive | Saskia | Email/Drive URL noted in session note (no secrets in git) |

**Out of Step 0:** legal/constitution, full admin panel, own domain (unless she asks).

---

### Step 1 — Inventory what already exists (1–2 sessions)

| # | Action | Owner | Done when |
|---|---|---|---|
| 1.1 | Clone/open `saskia-personal-context` locally | Team | Repo available for Friday-style tour |
| 1.2 | List live workbooks under `04_foodbiz-management-system/data/` and who last changed them | Team | ~~Short inventory~~ → [analysis/existing-assets.md](analysis/existing-assets.md) (git inventory done; Drive map TBD) |
| 1.3 | Map “what Saskia did in Hermes” → files/Drive/Sheets vs git | Team + Saskia | Gaps list: missing / duplicated / only-on-Drive |
| 1.4 | Note Hermes context-bleed issue (company topics in her chat) | Team | Decision logged: isolate later vs leave for now |

**Deliverable:** `docs/analysis/existing-assets.md` + effort estimate per gap.

---

### Step 2 — Seal Hermes / protect admin files (P0)

| # | Action | Owner | Done when |
|---|---|---|---|
| 2.1 | Reaffirm hard rules: Python regenerates xlsx; no free hand-edits by agents | Team | Rules visible in personal-context `AGENTS.md` (already) + short “how Saskia edits” one-pager in this repo |
| 2.2 | Define **allowlist** of what Hermes may update later (production rows, waste, stock) vs **never without OK** (prices, recipes, identity) | Team + Saskia | Draft ready: [operations/hermes-edit-policy.md](operations/hermes-edit-policy.md) — confirm with her |
| 2.3 | Implement gate: agent changes via PR / preview / approval (not silent overwrite) | Team | Documented procedure; at least one dry-run |
| 2.4 | Give Saskia a single “open here to work” path (Drive and/or local Excel + link to repo backup) | Team | She can open and edit without asking where files are |

**Done for Step 2:** she works in one place; Hermes cannot silently rewrite sealed sheets.

---

### Step 3 — Recipes, inventory, planning operational (P0)

| # | Action | Owner | Done when |
|---|---|---|---|
| 3.1 | Load real ingredient prices after her shopping (or guided session) | Saskia (+ team assist) | Inventory prices filled for items she buys |
| 3.2 | Confirm recipe catalog she will sell first (subset OK) | Saskia | Named list of v1 products |
| 3.3 | Production planner usable for one real week | Saskia + team | One week planned in workbook/tool |
| 3.4 | Simple way to register sales numbers (even manual tab) | Team | She can log a sale and see basic profitability signal |

**Done for Step 3:** she can cook from the book, know stock/cost, plan a week, log sales.

---

### Step 4 — WhatsApp Business path (P0)

| # | Action | Owner | Done when |
|---|---|---|---|
| 4.1 | Create/configure WhatsApp Business on business number | Saskia | WA Business profile live |
| 4.2 | Decide MVP: free-text orders vs catalog vs web→WA handoff (no full bot required in v1) | Team + Saskia | Choice written in `docs/operations/whatsapp-mvp.md` |
| 4.3 | Wire “new order → notify Saskia” (WA and/or email on cow account) | Team | Test order received end-to-end |
| 4.4 | Keep bot automation **out** of personal-context repo until scoped | Team | Scope note only; no surprise bot deploy |

**Done for Step 4:** a customer can order; she gets the alert; she can fulfill.

---

### Step 5 — Light website / visual aid (P1, after P0 or parallel if capacity)

| # | Action | Owner | Done when |
|---|---|---|---|
| 5.1 | Agree MVP: landing + menu/prices + CTA to WhatsApp (no payment online) | Team + Saskia | One-page brief |
| 5.2 | Collect logo/photos/texts (or schedule Luana/Marga later) | Saskia | Assets folder or Drive link |
| 5.3 | Build + host (domain optional) | Team | URL live; change process under monthly hosting if sold |
| 5.4 | Quote hosting: monthly Gs. 150–200k vs annual ~1.8M as discussed | K.W. | Offer written; she accepts or declines |

**Done for Step 5:** findable page that sends people to WhatsApp.

---

### Step 6 — Priced backlog for later (analysis deliverable)

| # | Action | Owner | Done when |
|---|---|---|---|
| 6.1 | Estimate admin-panel modules (ingredients, products, purchases, orders, waste, schedules, invoices) | Team | `docs/analysis/admin-panel-estimate.md` with Gs. ranges + phases |
| 6.2 | Estimate isolate Hermes context/server (Wesley-style) | Team | Optional line item |
| 6.3 | Legal/constitution + Marga package as deferred SKUs | Team | Listed as Phase 3+, not blocking P0 |
| 6.4 | Present numbers so she can prioritize | K.W. + Saskia | Call or WhatsApp with ranked choices |

**Done for Step 6:** she has specific numbers; P0 is not blocked by big-system design.

---

## Suggested calendar

| When | Focus |
|---|---|
| **Now** | Step 0 (scope + questionnaire closure from audio) |
| **Next working session** | Step 1 inventory + Step 2 seal policy |
| **Same week / next** | Step 3 data load + Step 4 WA Business MVP |
| **Following week** | Step 5 site brief (if P0 stable) |
| **After analysis** | Step 6 quote review |

---

## Explicitly not next

- Company constitution / legalization  
- Full multi-database admin platform  
- Payment gateway / SET invoicing  
- Separate production server “because Wesley has one” (unless context bleed forces it)  
- Renaming or mixing this client repo with unrelated project labels  

---

## Checklist (print / paste to WhatsApp)

```
[ ] Package scope confirmed (P0 only vs full setup)
[ ] Intake answers filed
[ ] Existing assets inventory written
[ ] Hermes edit policy written + gate agreed
[ ] Saskia has one clear place to open admin files
[ ] Prices loaded for current ingredients
[ ] V1 product/recipe list agreed
[ ] One week production plan done
[ ] Sales log usable
[ ] WhatsApp Business live
[ ] Test order → notification works
[ ] (Optional) Site MVP brief + quote for hosting
[ ] Admin-panel / later phases priced
```

---

## File map for this doc set

See full index: [README.md](README.md).

| Doc | Role |
|---|---|
| `docs/NEXT-STEPS.md` | **This file** — ordered actions |
| `docs/INFO-FROM-SASKIA.md` | What she must provide |
| `docs/DELIVERABLES.md` | What we ship |
| `docs/PACKAGE-AND-PRICING.md` | Packages + Gs. figures |
| `docs/sessions/2026-08-12_meeting-audio-notes.md` | Meeting extract |
| `docs/intake/` | Questionnaire + partial answers |
| `docs/analysis/existing-assets.md` | Asset inventory |
| `docs/operations/` | Hermes policy, edit path, WhatsApp MVP |

---

*Update this file when a step completes or scope changes. Prefer short checkmarks over rewriting history — add a one-line note under the step.*
