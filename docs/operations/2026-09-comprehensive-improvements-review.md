# Saskia engagement — comprehensive improvements review

> **For Hermes:** Read first. This is the parallel doc to `docs/plans/2026-08-31-rms-fase-1-dev-plan.md` and `docs/operations/*`. It is **not** a replacement for the dev plan; it's a backlog of improvements to **negotiate** before signing or during fase 1.
>
> **Status:** Draft 1 (2026-09). Operator review pending. Categories: **MUST-FIX** (breaks the engagement if not addressed), **SHOULD-FIX** (high impact, low cost), **NICE-TO-HAVE** (quality of life), **OUT-OF-SCOPE-DEFERRAL** (parked, doesn't belong in fase 1).
>
> **Sources:** Walked all live `.xlsx` files in `saskia-personal-context/04_foodbiz-management-system/data/` (commit 37a475d); re-read `CURRENT-CONTEXT.md`, `PACKAGE-AND-PRICING.md`, dev plan §1–12, all 5 operations docs, all 10 process docs, architecture.md, blank template. Cross-checked with the 12 "concrete fixes" from the prior 7-hat critique.

---

## Section 1 — Critical (MUST-FIX before signing the quote)

These are items that **will** break the engagement if not addressed at sign-off. Each is a known gap between the locked docs and what actually needs to happen on Day 0.

### 1.1 The OS decision is undefined — and it changes everything

**Where:** dev plan §3 says *"Windows first (confirm Mac at kickoff)."* But §9 Task 0 already lists "Windows vs Mac" as a kickoff item.

**Problem:** The 70-hour build is materially different for Mac:
- `run.bat` doesn't exist; need `run.sh`
- `%LOCALAPPDATA%` doesn't exist; need `~/Library/Application Support/AIW-Saskia/`
- Windows Task Scheduler → `launchd` plist
- Edge / Chrome default vs Safari default (Safari has stricter localStorage rules)
- File dialogs (`tkinter.filedialog`) behave differently
- Console window: Windows shows one always; Mac can use `pythonw` for no console

If we build for Windows and discover at install that her laptop is Mac, we either rebuild (~20h of rework) or ship something broken.

**Fix:** **Add a pre-kickoff Task 0.0: "Confirm OS."** Single question on WhatsApp. No work begins until answered.

### 1.2 The clock-pause rule has a soft exception nobody defined

**Where:** dev plan §0: *"Production does not start until first cuota is in + Drive access + work PC named."* §9 Task 0 lists "Quote signed, first cuota in (production may start)" as the first checkbox.

**Problem:** The install session (Task 9) happens at her location, on her PC. That cannot be the same day as "first cuota in" if cuotas are by bank transfer — money doesn't move instantly. There's a 1-3 day gap where she's signed + paid but we haven't installed. During that gap, do we do prep work? Pre-build skeleton? Walk the workbooks?

**Fix:** Define a **Task 0.0 (unpaid, but quick)**:
- Confirm OS (from 1.1)
- Confirm Python availability on her PC (she might already have it)
- Confirm one Drive file accessible
- Confirm `127.0.0.1` not blocked by any corporate AV

This is 15-30 minutes of pre-work, **not billed**, done in the 1-3 day transfer window. Reduces Day-1 friction massively.

### 1.3 The hub quote PDF can't be verified from this sandbox

**Where:** `PACKAGE-AND-PRICING.md` says: *"Sendable (hub): Company-Information `docs/clients/2026-08-18-saskia-weiss-vander.md`."*

**Problem:** From this sandbox, `Ai-Whisperers/Company-Information` returned no content (private or empty). I cannot verify the locked numbers (Gs. 17.500.000 / 24 cuotas / no hosting / planning out) match the Word/PDF she'll actually sign.

**Risk if not fixed:** Saskia signs a Word doc that says Gs. 18.5M with a hosting clause, and we have no recourse because "the hub quote wins if this file and the Word disagree" (CURRENT-CONTEXT.md line 19). The CURRENT-CONTEXT is internal; the Word is what she signs.

**Fix:** Operator (Ivan) must verify the hub quote PDF manually before sending. Add to the kickoff checklist:

```
[ ] Hub quote at Company-Information/docs/clients/2026-08-18-saskia-weiss-vander.md 
    matches CURRENT-CONTEXT.md:
    [ ] Gs. 17.500.000 (not 17,500,000 or 17.5M)
    [ ] 24 cuotas of Gs. 729.167 (last Gs. 729.159)
    [ ] No hosting/monthly fee
    [ ] No planning assistant line item
    [ ] No website line item
    [ ] Titular = Kyrian Weiss van der Pol, CI 5991039 (or updated if AIW has a company account)
```

### 1.4 The Tier 2 items from prior turn are still open

- 2.1: Hub quote PDF verification (above, 1.3)
- 2.2: Confirm titular/CI hasn't changed (above, 1.3)
- 2.3: `IvanWeissVanDerPol/SaskiaPersonal` ERP fate — file is 404 per cursor-git-analysis from 2026-08-11. Three options: recreate from backup, find another source, or document as lost and remove cross-ref from `saskia-personal-context/README.md`. **Currently the README cross-ref points at a 404 on the live repo.**

**Fix:** Operator decision needed. If "document as lost" is the call, that's a one-line edit to `saskia-personal-context/README.md` that should land as its own commit before any further churn.

---

## Section 2 — High-impact, low-cost (SHOULD-FIX, adds ~12-15h to quote)

These items are not strictly broken today, but they will become broken within weeks of go-live. The cost of fixing them now is 12-15 hours. The cost of fixing them after Saskia is using the app daily is much higher (downtime, data quality issues, trust erosion).

### 2.1 Auto-backup to Drive is non-negotiable

**Where:** dev plan §3 says *"backups: Excel export she copies to Drive."* Manual step.

**Problem:** Manual backups don't happen. The day SQLite corrupts (power loss, Windows update, disk full), she loses every sale since the last export. There is **no automatic backup.** This is the #1 most likely failure mode of the entire engagement.

**Fix (1 hour):**
- `app/services/auto_backup.py` — runs on app startup
- If last backup > 24 hours ago, export `rms.sqlite` → timestamped `.xlsx` → save to a configured folder (e.g., `~/Documents/AIW-Saskia/backups/`)
- If last backup > 7 days ago, also push a notification toast: "Hace más de 7 días que no exportás. ¿Querés exportar ahora?"
- Configuration: `app/config.py` reads backup folder path from a `~/.config/aiw-saskia/config.toml`
- Task 9 install session: pre-create the backup folder, drop a `.gitkeep` if needed

**Cost: ~1h.** Pays for itself the first time she has a power loss.

### 2.2 SQLite WAL mode + bind-127.0.0.1-only + healthz endpoint

**Where:** dev plan §3 mentions `127.0.0.1:8765`. §5 data model. §10 team split.

**Problem:** The plan doesn't specify:
- `PRAGMA journal_mode=WAL` — without this, every sale entry locks the entire DB. Concurrent reads stall. Power loss mid-write silently corrupts the file.
- Bind to `127.0.0.1` only — uvicorn defaults can be changed; if `run.bat` ever passes `0.0.0.0` (e.g., "let me try from my phone"), every machine on her Wi-Fi can hit the app. No PII, but cost data + sales data.
- Health check — `/healthz` returning `{"db": "ok"|"locked", "server": "ok"}`. Without it, debugging "why is my browser showing a blank page" is 10 minutes of fumbling.

**Fix (1 hour):**
- `app/rms/db.py`: on engine creation, run `PRAGMA journal_mode=WAL; PRAGMA synchronous=NORMAL; PRAGMA foreign_keys=ON;`. Document in `app/README.md`.
- `app/rms/main.py`: `assert host == "127.0.0.1"` at startup; refuse to bind otherwise.
- `app/routers/health.py`: `/healthz` and `/healthz/db` (the latter actually opens a connection and runs `SELECT 1`).

**Cost: ~1h.** Eliminates 3 classes of bugs before they ship.

### 2.3 Self-service import (file picker, no USB hand-off)

**Where:** dev plan §6: *"import from a **copy** of Drive files she provides."*

**Problem:** "She provides a copy" is a multi-step ritual:
- Saskia opens Drive
- Finds the folder
- Downloads the file
- Hands it to us (USB? Airdrop? Email?)
- We import
- She deletes the copy (per the OPSEC note I added in 2.1.5)

This won't happen naturally. The "she runs it without us in the room" goal (dev plan §1) requires **self-service import.**

**Fix (2 hours):**
- `app/routers/excel_io.py`: add `POST /import` accepting a file upload
- UI: button "Importar desde archivo" → opens native file picker (Windows / Mac) → she picks her Excel → app imports
- Confirmation modal: "Esto puede pisar recetas e inventario. ¿OK?"
- Result modal: "Importaste 63 ingredientes, 20 recetas, 0 errores." (or with errors if any rows skipped)
- No copy-of-Drive-files ritual. No us-with-USB.

**Cost: ~2h.** Removes the only blocker to "she runs it without us."

### 2.4 Stock-out alert + monthly "stock-out" report

**Where:** dev plan §1.5 says "low-stock alert." §7 widget "Avisos: low stock."

**Problem:** The plan allows negative stock (correct: "kitchen reality > accounting purity"). But it doesn't say **what happens when stock is negative.** The dashboard will show the row in red, but she won't notice until she's out of an ingredient mid-recipe.

**Fix (1 hour):**
- `app/routers/dashboard.py`: alert widget shows **flashing red** when `stock_qty < 0` (vs. just yellow when `0 <= stock_qty < min_stock_qty`)
- `app/services/reports.py`: monthly report "Ingredientes bajo mínimo o negativo" — one row per ingredient, days-below-zero count, suggested reorder quantity
- This becomes the **monthly-close** process step (mirrors `processes/monthly-pnl-review.md` from the workbook system)

**Cost: ~1h.** The dashboard lie about negative stock is the biggest business risk of fase 1.

### 2.5 Spanish (vos) copy bank

**Where:** dev plan §1 — *"UI copy: Spanish, vos."* But no copy bank exists.

**Problem:** Whatever Kiki writes, Saskia (Paraguayan) will say "we don't say that here." Without a copy bank, every UI screen is a debate.

**Fix (1 hour):**
- Create `app/docs/copy-vos.md` (or `saskia/docs/operations/copy-vos.md` if you want it in the engagement repo) with **all UI strings** as a flat list
- Examples that need Paraguayan (not Argentine) Spanish:
  - Guardá (correct: Paraguayan vos), not "Salvá" (Argentine)
  - Stock bajo (correct: standard)
  - Venta registrada (correct)
  - Cantidad: with explicit "número entero" hint (because they use commas as thousands separators in PY)
  - Money: "Gs. 729.167" with period-as-thousands-separator (correct: Paraguayan convention) vs comma
- Get Saskia to review the bank during the kickoff, not during Task 3

**Cost: ~1h.** Eliminates 5+ rounds of "no, we say it like this" during the build.

### 2.6 Money rounding + unit enum

**Where:** dev plan §2 says *"round **half up** to integer at the last step only."* §5 data model.

**Problem:** 
- Intermediate vs final rounding: "Recipe cost = Σ (line.qty × ingredient.price)" — if line.qty is 333g and ingredient.price is Gs. 2.500/kg, line cost = 832.5 Gs. Round to 833? Or accumulate and round once at the end?
- Unit typing: dev plan §5 has `unit` field on `ingredient` and `recipe` but doesn't constrain values. Free-text "g" vs "gramos" vs "gram" is a bug factory.

**Fix (1 hour):**
- `app/rms/money.py`:
  ```python
  from decimal import Decimal, ROUND_HALF_UP
  def to_int_gs(value: Decimal) -> int:
      """Round to nearest integer Gs., half up. Use only at persistence."""
      return int(value.quantize(Decimal('1'), rounding=ROUND_HALF_UP))
  ```
- `app/rms/units.py`:
  ```python
  from enum import Enum
  class Unit(Enum):
      G = "g"
      KG = "kg"
      ML = "ml"
      L = "l"
      UNIT = "und"
      # Add others only if explicitly needed
  ```
- Validation in Pydantic models: reject anything not in the enum
- Test: muffin batch 12, cost 24_000 Gs. → unit cost 2_000 Gs. (no off-by-one from 1999.9999)

**Cost: ~1h.** Eliminates a class of rounding bugs before they ship.

### 2.7 Backup-before-reimport + skip-with-count on import

**Where:** dev plan §6: *"Re-import: default additive / match by name. Never wipe sales. Confirm on screen."*

**Problem:** What's the recovery if she clicks "Yes, overwrite" and regrets it 5 seconds later? Currently nothing — the import is atomic.

**Fix (1 hour):**
- Before import, copy `rms.sqlite` → `rms.sqlite.bak.<timestamp>` in the data dir
- If she cancels mid-import (rare but possible), restore from bak
- If a row's ING-NNN doesn't exist in Inventory, **skip** the row but **count** the skips
- Show in the result modal: "Importaste 63 ingredientes (0 skipped), 20 recetas (3 skipped — see docs/sessions/2026-09-XX_import-skipped.md)"

**Cost: ~1h.** Recovery from mistakes is the difference between "one click of regret" and "call support."

### 2.8 Lifecycle doc: workbooks become read-only after fase 1

**Where:** Currently, both the Excel workbooks and the new SQLite are operational. After fase 1 ships, the workbooks are still there but should be the **output** of the app's export, not the **input**.

**Problem:** Without a written lifecycle, she'll keep editing the Excel AND the app, and they'll drift. At month 6 the workbooks are wrong and the app is wrong in different ways.

**Fix (30 min):**
- Add `saskia/docs/operations/lifecycle.md`:
  ```
  ## Before fase 1 (now)
  - Workbook system (`saskia-personal-context/04_foodbiz...`) is the operational truth
  - Drive Excels are working copies
  - App doesn't exist

  ## After fase 1 ships (week 6+)
  - App is the operational truth
  - Drive Excels are auto-generated exports (or hand-edited, then re-imported)
  - Workbooks in personal-context are READ-ONLY mirrors of the app
  - To "edit": edit in the app, then export to Excel if you want a copy in Drive

  ## Migration
  - Week 6-8: parallel run. App + workbook both updated.
  - Week 9+: app only. Workbook = export.
  - Day 90: workbooks archived. Personal-context repo switches to "history only."
  ```

**Cost: ~30min.** Manages expectations. Future-you (or future-Kiki) won't have to reconstruct this.

### 2.9 Test suite minimum (money, costing, import roundtrip)

**Where:** dev plan §4 lists `tests/test_costing.py`, `tests/test_stock_drop.py`, `tests/test_import_roundtrip.py`. §9 Task 1 says "tests/test_costing.py exists with one failing test for 'batch cost / yield' (write the test first)."

**Problem:** Three tests for a 70-hour build is **not enough**. Bugs in costing are the most expensive (every sale entry uses them). Bugs in import are the most silent (wrong data, no error).

**Fix (2 hours):**
- `tests/test_money.py`: integer rounding, decimal precision, no float drift
- `tests/test_costing.py`: already in plan, expand to 10+ tests (yield boundary cases, missing prices, sub-recipes)
- `tests/test_stock_drop.py`: already in plan, expand to 5+ tests (negative stock, void restoration, multi-ingredient recipes)
- `tests/test_import_roundtrip.py`: already in plan, expand to 3+ tests (full mini.xlsx, partial overlap, conflict resolution)
- `tests/test_units.py`: unit enum validation
- Run pytest before each `git commit`. Local pre-push hook. **No CI** (overkill for this scope).

**Cost: ~2h.** Catches regressions before Saskia does.

### 2.10 Pre-written answers to questions she'll ask

**Where:** Future WhatsApp messages from Saskia.

**Problem:** She will definitely ask:
- "Can I open this on my phone?"
- "Can my sister Kiki log in too?"
- "What if I get a new laptop?"
- "What if my internet goes down?"
- "Can I share this with my contador?"

Without prepared answers, you'll either say "yes" (scope creep) or "no, that's another quote" (which she perceives as pushback). **With** prepared answers, you have a script.

**Fix (1 hour):**
- `saskia/docs/operations/faq-saskia.md` with 10 pre-written Q&A
- Tone: warm, concrete, no jargon
- Each answer includes: "Right now: X. If you want Y later, it's a separate quote (Gs. 250.000/h)."

**Cost: ~1h.** Defends the quote against scope creep.

### 2.11 Inventory_filter.gs and the Sheets-side plan

**Where:** `saskia-personal-context/04_foodbiz-management-system/Inventory_filter.gs` (Apps Script). `processes/out-of-stock.md` mentions Sheets-side catalog.

**Problem:** There's a Google Apps Script helper and a Sheets-side catalog design that **doesn't map to the new app.** If she continues using Sheets (some users prefer phones), the workflow diverges.

**Fix:** During the install session, decide:
- Option A: she uses **only** the new app for inventory. Old Sheets scripts archived.
- Option B: she uses **both**. App writes to SQLite; export-to-Sheets goes through Drive. The Apps Script becomes a read-only mirror.
- Option C: she uses **only** Sheets (the app exports to Sheets, Sheets is the dashboard).

**Recommendation:** Option A for v1. Option B as a future enhancement. Document the choice in `app/README.md`.

**Cost: ~30min** to decide + document.

### 2.12 Vendor lock-in via Google Drive

**Where:** Everything assumes Google Drive + cow email.

**Problem:** If Google changes Drive API quotas, breaks OAuth, or she migrates to another provider, the export workflow breaks.

**Fix:** The export target should be a **local folder**, not Drive directly. Drive sync (Drive File Stream, OneDrive, iCloud, etc.) handles the upload. The app doesn't know about Drive. **Document this in `app/README.md`.**

**Cost: ~30min.** Future-proofs the export.

---

## Section 3 — Quality of life (NICE-TO-HAVE, defer to fase 1.5 or 2)

These are improvements that don't block the engagement but would make it nicer. Bundle them into a "fase 1.5" mini-quote after the main 70h if she has budget.

### 3.1 Recipe versioning

**Problem:** If she changes a recipe (drops an ingredient, changes quantity), the historical sales that used the old recipe will report costs against the new recipe. Same lie as the cost-variance issue.

**Fix:** Add `recipe.version` and `recipe_line.valid_from` / `valid_to`. **Out of scope for fase 1** but design the schema to allow adding these columns later.

### 3.2 Per-month close automation

**Problem:** The `processes/monthly-close.md` doc says "Saskia: 30 min, Iván: 20 min." That's 50 min/month, recurring. Forever.

**Fix:** A "Close month" button in the app that:
1. Generates the month's P&L from SQLite
2. Exports to Excel with the standard format
3. Optionally emails to a configured address (operator-only)
4. Logs the close in `import_batch` (or a new `monthly_close` table)

**Cost: 4-6h.** Saves 50 min/month. Pays back in 6 months.

### 3.3 Supplier-side automation

**Problem:** The `HEREBUS_Comparacion_Proveedores.xlsx` has 252 rows of comparison. It's never updated because updating requires manual entry of 4 supplier prices × 63 ingredients every price change.

**Fix:** When she updates `ingredient.purchase_price_gs` in the app, also let her tag a supplier. Show price history per ingredient. The supplier comparison becomes a **report**, not a workbook.

**Cost: 6-8h.** Out of fase 1 but natural for fase 1.5.

### 3.4 Recipe unit-test for new-product onboarding

**Problem:** `processes/new-product-onboarding.md` describes a 1-2 hour workflow involving the blank template, then renaming, then entering ingredients. **The plan should have a corresponding UI flow in the app.**

**Fix:** When the app supports recipe creation (Task 3), the UI should mirror the blank template structure: 14 ingredient slots, packaging, channel margins, auto-pricing. That way the "from blank template to new recipe" workflow is one UI step, not 6.

**Cost: ~2h on top of Task 3.** Document in Task 3.

### 3.5 Multi-currency support

**Problem:** All money is Gs. If she starts buying from Paraguay AND from abroad (e.g., spices from NL), multi-currency becomes real.

**Fix:** Add `currency` field to `ingredient.purchase_price_gs` → rename to `purchase_price` + `purchase_currency`. FX rate as a separate config field. **Not fase 1**, but design schema to allow.

### 3.6 Sub-recipe self-reference

**Problem:** `processes/sub-recipes-suppliers-analisis-wishlist.md` introduces 3 sub-recipes (Masa choux, Masa de hojaldre rápida, Crema pastelera) that are **ingredients in other recipes**. The new app's data model has `recipe_line.ingredient_id` pointing to `ingredient.id`, but sub-recipes are `recipe.id`, not `ingredient.id`. The import mapper flags this gap.

**Fix:** Either (a) flatten sub-recipes into ingredients (lose structure) or (b) make `recipe_line.ingredient_id` polymorphic (referencing either ingredient.id or recipe.id). **Out of scope for fase 1**, but design the schema to allow option (b).

### 3.7 Print-friendly dashboard

**Problem:** She'll want to print the dashboard for the monthly review meeting.

**Fix:** Add `?print=1` query parameter that renders a print-friendly CSS layout. **Cost: 1h.** Defer.

---

## Section 4 — Out-of-scope but should be on the radar

### 4.1 The 38h planning-assistant module (parked at Gs. 9.5M)

**Where:** CURRENT-CONTEXT.md says *"Planning: she asked; do not put it in the signature PDF."* PACKAGE-AND-PRICING.md says *"Internally Gs. 9.500.000 — parked; she asked 20 Aug; not in the PDF."*

**Status:** Not in fase 1. The `06_calendar-planner/` FastAPI MVP exists from earlier work but is not wired into the new app.

**When to revisit:** After fase 1 ships (week 6+) and she's been using the app for 4-6 weeks. She'll feel the absence of "what should I bake tomorrow" / "what do I need to shop for."

### 4.2 The Gs. 9.5M figure needs re-derivation

**Where:** Pinned at Gs. 9.5M with "38 h" estimate. The number is from the 2026-08-20 audio meeting.

**Problem:** That number was set before the 70h fase 1 budget was finalized. The planning module should be sized **after** fase 1 ships and we see how much of the dashboard data she's actually using.

**Recommendation:** Re-derive the planning module quote at week 8 of fase 1, with fresh data on her actual usage.

### 4.3 Public website (deferred to "after local is running")

**Where:** CURRENT-CONTEXT.md: *"Website: Later, when the local is running — not in the signed quote."*

**Status:** Out of scope. But: the `processes/delivery-zones.md` and `processes/out-of-stock.md` docs both reference a future WhatsApp bot + website handoff. **Those should NOT influence fase 1 design.** The app doesn't need to know about the website.

**When to revisit:** After fase 1 ships, if she asks.

### 4.4 "WhatsApp Business + catalog" (Option B in `whatsapp-mvp.md`)

**Where:** `whatsapp-mvp.md` says *"Recommendation for P0: A, then add B once V1 products/prices exist."*

**Status:** P0. **But it's not in the quote.** This is a future engagement, separate from fase 1.

**Recommendation:** Once fase 1 ships and she has v1 products in the app, set up WA Business + catalog in 4-6 hours. Quote: Gs. 1.000.000-1.500.000 (4-6h × Gs. 250.000/h).

### 4.5 Company constitution / Marga manual / SET invoices

**Where:** Deferred by her per intake answers. Multiple "out of scope" markers in CURRENT-CONTEXT.md.

**Status:** Out of scope. Don't surface these unless she asks.

### 4.6 Multi-machine sync

**Where:** Currently single-PC, single-user. If she gets a second laptop, or wants Kiki to see the dashboard, sync becomes a question.

**Status:** Out of scope. Phase 2+ if she ever asks.

---

## Section 5 — Cross-cutting improvements to the documentation itself

The engagement repo's docs are good but have these gaps.

### 5.1 The "round 2" review plan is vague

**Where:** dev plan §9 Task 10: *"Round 1: she uses it 3–5 days; WhatsApp list of fixes in scope. Round 2: same. Cosmetic only if hours remain."*

**Problem:** "3-5 days" is undefined. "Cosmetic only if hours remain" suggests round 2 is whatever's left over, not a real review.

**Fix:** Define each round explicitly:
- **Round 1** (week 6-7): functional. She uses the app for her daily work for 5 days. Sends WhatsApp list of bugs/missing features that are **in scope** (anything in dev plan §1 acceptance).
- **Round 2** (week 8): copy review. She reviews the Spanish copy bank (`app/docs/copy-vos.md`). Sends the list of changes. Implementation in this round is **only copy** changes.
- **Optional round 3** (week 9, billed extra): UX issues she noticed but weren't bugs.

### 5.2 The "team split" section doesn't define handoffs

**Where:** dev plan §10 lists 4 roles. §12 lists handoff artifacts.

**Problem:** When Kiki finishes the build, what does Ivan do? When Ivan finishes the install, what does Saskia do? The handoff artifacts list is incomplete.

**Fix:** Make handoffs explicit:
- Kiki → Ivan: code is in `saskia/app/`, tests green, README written.
- Ivan → Saskia: install is on her PC, app opens to `/`, she's registered a test sale, she's seen the dashboard.
- Saskia → Ivan: "I want X" (X is captured for next round).

### 5.3 The architecture doc in `04_foodbiz-management-system/docs/architecture.md` is reusable

**Where:** That doc is detailed and useful for the workbook system. **A parallel doc should exist for the new app.**

**Fix:** Create `app/docs/architecture.md` mirroring the workbook doc:
- Data flow (Drive Excels → SQLite → Excel export)
- Sources of truth (one: SQLite; the workbooks are output not input)
- Update paths (how a recipe change propagates)
- Verification matrix (tests + manual checks)

**Cost: ~1h.** Future-self thank-you.

### 5.4 The "open questions" doc is workbook-only

**Where:** `saskia-personal-context/03_data-quality/open-questions.md` has 7 open questions about the workbook data.

**Problem:** When the app ships, there will be new open questions (e.g., "what do we do about negative stock?" or "how do we handle currency formatting on phones?").

**Fix:** Add `saskia/docs/operations/open-questions-app.md` as the app-side counterpart. Maintain both.

### 5.5 The "12 Aug notes are void" rule should be auditable

**Where:** AGENTS.md rule #8 says "12 Aug notes are void." The voided notes are the Gs. 2.5M website package, hosting Gs. 150k/mo, etc.

**Problem:** Rule says "void" but doesn't say **where** to find the voided notes (for context) or **how** to prove they're void (vs. accidentally resurrected).

**Fix:** Create `saskia/docs/archive/2026-08-12_voided-packages.md` with the original text of the voided notes, clearly marked as historical. Anyone tempted to re-quote them sees this archive and the void stamp.

**Cost: ~30min.** Audit trail.

---

## Section 6 — Things I'd push back on in code review

If I were reviewing PRs against the dev plan, these would be my "needs discussion" comments.

### 6.1 The "agents regenerate" assumption for formulas

**Where:** `how-saskia-edits.md` says agents regenerate formulas; she only edits inputs.

**Problem:** Phase 2+ will have Hermes / Erebus / Cursor agents touching the data. If agents can regenerate the SQLite, the "no silent overwrite" rule is violated.

**Fix:** Document the agent boundary clearly. Agents can propose changes (diff + preview), never apply without OK. The local app is the agent's user interface.

### 6.2 The "void" feature is technically harder than it sounds

**Where:** dev plan §5: *"Fase 1 must support void; otherwise a typo wrecks stock."*

**Problem:** Void reverses `sale_stock_move` rows. But what if between the original sale and the void, she restocked (changes `ingredient.stock_qty` directly via Inventory edit)? The reversal is wrong — it puts stock back, but stock was already adjusted by her restock.

**Fix:** The void should only reverse the **last applied stock move**, not blindly add. Or: void = negative sale (record a new sale with negative qty, let costing recompute). Latter is cleaner. Document the chosen approach in §5.

### 6.3 The "matches by name" import rule is fuzzy

**Where:** dev plan §6: *"Re-import: default additive / match by name. Never wipe sales."*

**Problem:** "Match by name" is undefined. Match by what name? Ingredient name as printed in Excel? Normalized (lowercase, accent-stripped)? ING-ID if present?

**Fix:** Define the matcher explicitly: try ING-ID first, fall back to normalized name (lowercase, accent-stripped, whitespace-collapsed). Document in `services/import_xlsx.py` docstring. Test cases for "Harina de Trigo" vs "harina de trigo" vs "Harina Trigo."

### 6.4 The "calendar month" period is timezone-sensitive

**Where:** dev plan §7 widget "Period toggle: hoy / semana / mes (calendar month)."

**Problem:** Paraguay is UTC-4, no DST. But Python `datetime.now()` uses local time, which depends on the OS setting. If her laptop is set to a different timezone (default on new Windows is Pacific Time in some regions), the dashboard "today" is wrong.

**Fix:** Hardcode timezone in the app:
```python
from zoneinfo import ZoneInfo
ASUNCION = ZoneInfo("America/Asuncion")
now = datetime.now(ASUNCION)
```
Or simpler: require the OS timezone to be set to Asunción, with a startup check. **30 min of build time.**

### 6.5 The "review round 1" feedback channel is undefined

**Where:** dev plan §9 Task 10: *"WhatsApp list of fixes in scope."*

**Problem:** WhatsApp is informal. List can get lost. No version control.

**Fix:** Create `saskia/docs/sessions/round-N-feedback.md` as a markdown file she fills in. Each item: "(severity) (description) (steps to reproduce)." She sends it to Ivan via WhatsApp when done; Ivan commits it to the repo.

**Cost: ~30min to set up; saves hours of "where was that bug again?"**

---

## Section 7 — Risk-matrix summary

| Item | Severity | Cost to fix | When |
|---|---|---|---|
| 1.1 OS decision undefined | High | 0 (1 WhatsApp msg) | Before kickoff |
| 1.2 Clock-pause exception | Medium | 30 min | Before kickoff |
| 1.3 Hub quote unverified | High | 15 min (operator) | Before sending quote |
| 1.4 ERP cross-ref dangling | Low | 5 min (operator decision) | This week |
| 2.1 Auto-backup | Critical | 1h | Task 9 (install) |
| 2.2 WAL + bind + healthz | High | 1h | Task 1 or 2 |
| 2.3 Self-service import | High | 2h | Task 6 |
| 2.4 Stock-out alert | Medium | 1h | Task 7 |
| 2.5 Spanish copy bank | Medium | 1h | Before Task 3 |
| 2.6 Money rounding + unit enum | Medium | 1h | Task 2 |
| 2.7 Backup-before-reimport | Medium | 1h | Task 6 |
| 2.8 Lifecycle doc | Low | 30 min | Before Task 9 |
| 2.9 Test suite | Medium | 2h | Tasks 1-6 |
| 2.10 FAQ for Saskia | Low | 1h | Before Task 0 |
| 2.11 Inventory_filter.gs decision | Low | 30 min | Before Task 0 |
| 2.12 Drive-vendor lock-in | Low | 30 min | During Task 6 |
| 3.1 Recipe versioning | Low | schema design only | Post-fase 1 |
| 3.2 Monthly close button | Medium | 4-6h | Fase 1.5 |
| 3.3 Supplier automation | Medium | 6-8h | Fase 1.5 |
| 3.4 New-product onboarding UI | Low | 2h (on Task 3) | During Task 3 |
| 3.5 Multi-currency | Low | schema design only | Post-fase 1 |
| 3.6 Sub-recipe self-reference | Low | schema design only | Post-fase 1 |
| 3.7 Print-friendly | Low | 1h | Post-fase 1 |
| 4.1 Planning module | Out of scope | 38h estimate | Post-fase 1 |
| 4.2 Re-derive 9.5M | Out of scope | 1h | Week 8 |
| 4.3 Public website | Out of scope | new quote | If she asks |
| 4.4 WA Business + catalog | Out of scope | 4-6h new quote | After fase 1 |
| 4.5 Legal/constitution | Out of scope | n/a | If she asks |
| 4.6 Multi-machine sync | Out of scope | n/a | If she asks |
| 5.1 Round 2 review plan | Low | 30 min | Before Task 10 |
| 5.2 Handoff definitions | Low | 30 min | Before Task 0 |
| 5.3 Architecture doc for app | Low | 1h | After Task 6 |
| 5.4 Open-questions app doc | Low | 30 min | After Task 6 |
| 5.5 Voided-packages archive | Low | 30 min | This week |
| 6.1 Agent boundary | Medium | 30 min doc | Before Task 6 |
| 6.2 Void semantics | Medium | 30 min design | Task 5 |
| 6.3 Name-matching rule | Medium | 30 min design | Task 6 |
| 6.4 Timezone | Low | 30 min | Task 1 |
| 6.5 Feedback channel | Low | 30 min | Before Task 10 |

**Total MUST-FIX: ~3 hours** (mostly operator decisions)
**Total SHOULD-FIX: ~12 hours** (fits within the 70h quote as enhancements)
**Total NICE-TO-HAVE: ~25-30 hours** (fase 1.5 territory)
**Total OUT-OF-SCOPE: defer or new quote**

---

## Section 8 — Recommended order of operations

If I were running this engagement, here's the order I'd do things:

### Before signing (operator-only)

1. **Verify hub quote** against CURRENT-CONTEXT (1.3, 15 min)
2. **Confirm titular/CI** is still Ivan (1.4 / 2.2, 5 min)
3. **Decide IvanWeissVanDerPol/SaskiaPersonal fate** (1.4 / 2.3, 5 min)
4. **Send WhatsApp to Saskia**: confirm OS, WhatsApp MVP option, V1 product list, Drive folder, "send photo of next shopping trip receipts"
5. **Get the voided-packages archive** committed (5.5)
6. **Land the Tier 2 changes** to personal-context README (1.4)

### At kickoff (Day 0, no clock yet)

7. **Install Task 0.0 pre-flight**: OS confirm, Python check, AV check, Drive file accessibility (1.1, 1.2)
8. **Get her Spanish copy bank** approved before Task 3 (2.5)
9. **Decide Inventory_filter.gs fate** (2.11)

### During build (clock running, week 1-8)

10. **Tasks 1-2 first**: scaffold + costing engine. Land WAL + bind-127 + healthz + money/unit helpers early (2.2, 2.6). Tests too (2.9).
11. **Task 3-4**: Spanish copy bank governs the UI strings. Customer review round mid-week.
12. **Task 5**: void semantics decided up front (6.2).
13. **Task 6**: self-service import (2.3), backup-before-reimport (2.7), importer tests (2.9), name-matching rule (6.3), timezone (6.4), Drive-vendor-independence (2.12).
14. **Task 7**: stock-out alert + monthly report (2.4). Lifecycle doc (2.8). Architecture doc (5.3).
15. **Task 9**: install session. Includes auto-backup setup (2.1). FAQ walkthrough (2.10).

### After fase 1 ships (week 8+)

16. **Re-derive planning module quote** at Gs. 9.5M or whatever the new number is (4.2)
17. **Propose fase 1.5 mini-quote** for monthly close automation + supplier automation + recipe versioning + print-friendly (3.1-3.7)
18. **Propose fase 2 quote** for WA Business + catalog (4.4)
19. **Schedule the 15-day PAT rotation cron** output to remind you

---

## Section 9 — Things I will NOT do without explicit operator OK

These came up in the research but require a call from you:

- ❌ **Will not bump the quote from 70h to 100h.** The 70h is what she signed for. Adding ~15-20% of SHOULD-FIX items is at your discretion.
- ❌ **Will not propose fase 1.5 pricing without your input.** The "monthly close" button etc. is a future quote.
- ❌ **Will not touch `IvanWeissVanDerPol/SaskiaPersonal`.** That's a personal-account repo, not for me to investigate.
- ❌ **Will not write the Spanish copy bank myself.** I don't speak Paraguayan. Kiki or Saskia must own that.
- ❌ **Will not start any of the build work** before she signs and the clock starts. Dev plan §0 is explicit.

---

*Document version: 2026-09 (draft 1)*
*Author: Ivan / Hermes*
*Source documents walked: `saskia/CURRENT-CONTEXT.md`, `saskia/PACKAGE-AND-PRICING.md`, `saskia/docs/plans/2026-08-31-rms-fase-1-dev-plan.md`, `saskia/AGENTS.md`, all 5 `docs/operations/*.md`, `docs/INFO-FROM-SASKIA.md`, `docs/intake/answers-from-meetings.md`, `docs/analysis/admin-panel-estimate.md`, `docs/analysis/existing-assets.md`, `saskia-personal-context/04_foodbiz-management-system/docs/architecture.md`, all 10 `04_foodbiz-management-system/processes/*.md`, `04_foodbiz-management-system/recipes/BLANK_TEMPLATE.md`. Plus the live xlsx walk that produced IMPORT-MAPPER.md.*

*Cross-references:*
- `docs/operations/import-mapper.md` (just-pushed, the v1 catalog column map)
- `docs/operations/herbus-discovery-prompt.md` (just-pushed, the OpenCode-on-her-PC inventory spec)
- `docs/plans/2026-08-31-rms-fase-1-dev-plan.md` (the locked build plan — unchanged)
- This document is **proposed improvements**, not a replacement for the dev plan.
