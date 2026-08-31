# Fase 1 prep — operator-side docs

> **For Ivan, Kiki, and any agent reading the saskia engagement before kickoff.** Bundle of operator-side documents produced 2026-09 as part of the comprehensive improvements review. Not for Saskia as-is.
>
> **Source:** `/opt/data/profiles/ivan/.hermes/plans/2026-08-31_192054-saskia-comprehensive-improvements.md`
>
> **Cross-references:**
> - `docs/plans/2026-08-31-rms-fase-1-dev-plan.md` — the locked build plan
> - `docs/CURRENT-CONTEXT.md` — locked product + price
> - `docs/PACKAGE-AND-PRICING.md` — locked package
> - `docs/operations/import-mapper.md` — v1 catalog column spec
> - `docs/operations/herbus-discovery-prompt.md` — OpenCode inventory spec

---


---

# PRE SIGNOFF CHECKLIST

_Source: original at `saskia-preflight/PRE-SIGNOFF-CHECKLIST.md`_

# Pre-signoff checklist (operator)

> **For Ivan only.** Not for Saskia. Walks through the MUST-FIX items from the 2026-09 comprehensive improvements review before the quote goes out.
>
> **Source:** `/opt/data/profiles/ivan/.hermes/plans/2026-08-31_192054-saskia-comprehensive-improvements.md` §1

## A. Hub quote verification (15 min)

The locked numbers in `docs/CURRENT-CONTEXT.md` must match the Word/PDF Saskia will sign at `Company-Information/docs/clients/2026-08-18-saskia-weiss-vander.md`. The CURRENT-CONTEXT is internal; the Word is what she signs.

- [ ] **Total: Gs. 17.500.000** (not 17,500,000 with thousands sep, not 17.5M with M suffix, not 17,5M with Spanish decimal)
- [ ] **24 cuotas of Gs. 729.167** (last cuota Gs. 729.159 to make the math exact)
- [ ] **No hosting / monthly ops** line item
- [ ] **No planning assistant** line item (Gs. 9.500.000 is parked internally, not on the PDF)
- [ ] **No website** line item (out of scope; she can ask later)
- [ ] **No WhatsApp bot / SET / tables / KDS** line item
- [ ] **No dedicated Hermes server** (that's John's SKU)
- [ ] **Titular: Kyrian Weiss van der Pol, CI 5991039, WhatsApp 0985 724 135** — confirm this is still YOU. If AIW now has a Paraguayan company bank account, update BEFORE signing.

If any line mismatches: fix the **Word/PDF**, not the CURRENT-CONTEXT. CURRENT-CONTEXT is correct; the Word is the one we control at signature.

## B. ERP dangling cross-ref (5 min)

`IvanWeissVanDerPol/SaskiaPersonal` returns 404 per cursor-git-analysis from 2026-08-11. `saskia-personal-context/README.md` cross-references it.

Three options:
- **(a)** Recreate from original data — operator decision, requires old backup
- **(b)** Find another source (the cursor-git-analysis mentioned it had 384 transactions) — operator decision, needs search
- **(c)** Document as lost + remove cross-ref from `saskia-personal-context/README.md` — code-level fix, this checklist owns it

If (c): separate commit "docs: remove dangling ERP cross-ref". One-line edit.

If (a) or (b): leave the cross-ref, mark the README with a [verify] tag.

## C. OS confirmation (1 WhatsApp msg to Saskia)

**Before kickoff**, send a single message asking: *"¿La compu donde va a funcionar el sistema es Windows o Mac? Necesito confirmarlo antes de empezar."*

This is the OS decision from §1.1 of the improvements review. Building for Windows then discovering Mac wastes ~20h.

## D. Task 0.0 pre-flight (added to dev plan §13)

The dev plan §0 says clock pauses until first cuota + Drive + PC named. The **install session** (Task 9) happens at her location, but cuotas are by bank transfer (1-3 day gap). During that gap, we can do unpaid pre-work.

This is documented in dev plan §13 (newly added). Read it before kickoff.

## E. Out-of-scope pre-work that I (Hermes) will do before kickoff

These are operator-side, no clock impact:

- [x] Auto-backup spec (1h) — written as `app/docs/auto-backup-spec.md` (operator-side, becomes code at Task 9)
- [x] WAL + bind-127 + healthz spec — written into Task 1 of dev plan via patch
- [x] Money rounding + unit enum spec — written into Task 2 via patch
- [x] Self-service import spec — written into Task 6 via patch
- [x] Spanish copy bank request — drafted in `saskia/docs/operations/copy-vos-request.md` (asks Kiki or Saskia to fill)
- [x] FAQ for Saskia — drafted in `saskia/docs/operations/faq-saskia.md`
- [x] Lifecycle doc — `saskia/docs/operations/lifecycle.md`
- [x] Voided-packages archive — `saskia/docs/archive/2026-08-12_voided-packages.md`
- [x] Code-review checklist — `saskia/docs/operations/code-review-checklist.md`

## F. What I will NOT do

- ❌ Will not bump the 70h quote (commercial decision, Ivan's call)
- ❌ Will not write the Spanish copy (Paraguayan Spanish, not my language)
- ❌ Will not pre-build the `app/` skeleton (clock-pause rule)
- ❌ Will not investigate `IvanWeissVanDerPol/SaskiaPersonal` (your personal account)

---

*Generated 2026-09 by Hermes. Cross-references: `/opt/data/profiles/ivan/.hermes/plans/2026-08-31_192054-saskia-comprehensive-improvements.md`*


---

# dev plan addendum task 0.0

_Source: original at `saskia-preflight/dev-plan-addendum-task-0.0.md`_

# Dev plan addendum — Task 0.0 (pre-flight)

> **For the delivery team.** Additive to `docs/plans/2026-08-31-rms-fase-1-dev-plan.md`. Adds §13 clarifying the unpaid pre-flight work that happens between quote signature and first cuota arrival. Does NOT change the 70h quote, the acceptance criteria, or the scope lock.
>
> **Author:** Hermes. **Date:** 2026-09.
> **Source:** comprehensive improvements review §1.2.

---

## §13. Task 0.0 — Pre-flight (unpaid, ~15 min)

The dev plan §0 says clock pauses until first cuota + Drive access + PC named. The install session (Task 9) happens at her location, but cuotas are by bank transfer (1-3 day gap). During that gap, the unpaid pre-flight below can be done.

### What's in Task 0.0

- [ ] **OS confirmation** — single WhatsApp message to Saskia: *"¿La compu donde va a funcionar el sistema es Windows o Mac?"* Building for Windows and discovering Mac at install wastes ~20h of rework. **Block: do not start the build until answered.**
- [ ] **Python availability check** — she may already have Python 3.x installed; verify before installing a second copy. Log the version found.
- [ ] **Antivirus / firewall check** — confirm that `127.0.0.1:8765` is not blocked by corporate AV (rare for home laptops but not impossible).
- [ ] **Drive file accessibility** — confirm she can reach her Drive folder from the install target PC. Log the file path she'll hand off (or the URL she'll use for self-service import).

### What Task 0.0 is NOT

- Not code-writing
- Not design decisions
- Not commercial negotiation
- Not clock-starting

### Why this is unpaid

The clock starts when **first cuota is in + Drive access + PC named**. Until all three are true, we are pre-work. The pre-flight items above take ~15 min total and reduce Day-1 friction by hours.

### What if Task 0.0 reveals blockers?

If OS is Mac: **stop and report to operator**. The dev plan §3 says "Windows first (confirm Mac at kickoff)" but the build plan is materially different for Mac. Don't fake-Windows-build on a Mac — flag and renegotiate.

If Python is unavailable and she can't install: **stop and report**. Task 9 install session assumes Python is installed (or can be installed via official installer).

If AV blocks 127.0.0.1: **stop and report**. This is rare on home laptops; if it happens, the install session becomes a Windows Defender troubleshooting session, which is out of quote scope.

### When does the actual clock start?

After Task 0.0 passes AND:
- First cuota is in (per current §0)
- Drive read access exists (per current §0)
- Work PC named (per current §0)

All three must be true. Task 0.0 helps de-risk the third (PC + OS) but doesn't replace any of the three.

---

*End of addendum. Operator (Ivan) should review and either adopt or reject before next kickoff attempt.*


---

# faq saskia

_Source: original at `saskia-preflight/faq-saskia.md`_

# FAQ — questions Saskia will ask + pre-written answers

> **For Ivan.** Pre-written answers to questions that will come up after she starts using the app. Tone: warm, concrete, no jargon. Each answer either resolves the question OR clearly says "that's a separate quote."
>
> **Source:** improvements review §2.10.

---

## Q1: ¿Puedo abrir esto desde el celular?

**No, no en fase 1.** El sistema corre solo en tu computadora. Si querés verlo desde el celular u otra compu, eso es una segunda fase: deployamos el sistema en un servidor chiquito con login y HTTPS. Hoy no está en el presupuesto.

**Workaround:** podés tomar un screenshot del tablero y mandártelo por email. O exportar la planilla de Excel a Drive y abrirla en Sheets desde el celular (read-only).

---

## Q2: ¿Puede Kiki (mi hermana) entrar a ver?

**No, no en fase 1.** El sistema no tiene login. Cualquier persona que se siente en tu compu y abra el navegador ve todo: ventas, costos, clientes, márgenes. Si querés que otra persona tenga acceso, eso es una segunda fase: agregar login (probablemente un PIN de 4 dígitos al abrir el navegador) cuesta unas 4-6 horas extra. Hoy no está en el presupuesto.

**Workaround:** exportás la planilla a Excel y se la mandás por email o Drive.

---

## Q3: ¿Qué pasa si compro una compu nueva?

**Migración: ~2 horas a Gs. 250.000/h.** El sistema vive en tu compu actual. Si comprás una nueva, hay que instalar Python, instalar el sistema, copiar tu `rms.sqlite`, y verificar que todo funcione. Documentado como "out of quote" en el dev plan.

**Cómo evitarlo:** exportá a Excel regularmente (cada 1-2 semanas). Si perdés la compu, tenés un Excel con todo hasta esa fecha. El backup automático a Drive (que se configura en la instalación) ayuda mucho acá.

---

## Q4: ¿Qué pasa si se corta internet?

**No pasa nada.** El sistema corre 100% local. Internet solo se usa para:
- Descargar Python (solo la primera vez)
- Actualizar openpyxl / FastAPI (opcional)
- Sincronizar con Google Drive (si tenés Drive File Stream)

Si se corta internet durante una venta, registrás la venta igual. La sincronización a Drive vuelve cuando vuelve internet.

---

## Q5: ¿Puedo compartir esto con mi contador?

**Solo si exportás a Excel.** El sistema no tiene función "compartir con tercero". Lo que podés hacer:
1. Exportar a Excel al final del mes (botón en la app)
2. Subir el Excel a Drive
3. Compartir el archivo de Drive con tu contador

Para acceso directo (login + ver el sistema remotamente), eso es una segunda fase.

---

## Q6: ¿Cuánto sale agregar el sitio web? / ¿Cuánto sale el bot de WhatsApp?

**No están en este presupuesto.** El presupuesto actual (Gs. 17.500.000 en 24 cuotas) es solo por el sistema local. Trabajos adicionales:

- **Sitio web público** (1-2 páginas, carta de productos, link a WhatsApp): ~Gs. 2.500.000 (10-15 horas a Gs. 250.000/h)
- **WhatsApp Business + catálogo de productos**: ~Gs. 1.000.000 (4-6 horas)
- **Bot de WhatsApp que responda automáticamente**: ~Gs. 5.000.000+ (20+ horas, requiere deploy)
- **Planning assistant** (qué cocinar mañana, qué comprar): ~Gs. 9.500.000 (38 horas)

Si te interesa alguno, me decís y armamos un presupuesto aparte.

---

## Q7: ¿Y si me arrepiento y quiero volver al Excel?

**Eso es lo mejor del sistema local.** Todo es tuyo. Si querés volver al Excel mañana:
1. Exportás todo a Excel desde el sistema (botón "Exportar todo")
2. Tu último `rms.sqlite` queda en la compu por si acaso
3. Volvés a usar el Excel como antes

No hay lock-in. No hay "si pagás por 24 meses, no podés salir". Si decidís no seguir, te quedás con todo lo que exportaste y listo.

---

## Q8: ¿Qué pasa si mis precios cambian?

**Actualizás en el sistema.** Andás a Ingredientes → buscás el que cambió → editas el precio en Gs. → Guardá. El sistema recalcula automáticamente el costo de todas las recetas que usan ese ingrediente, y actualiza el margen de todos los productos. La próxima venta usa el precio nuevo.

**Si querés ver el historial de precios**, exportás a Excel y ahí ves todas las ventas con los precios al momento. (El sistema no guarda histórico de precios todavía — eso es una mejora para fase 1.5.)

---

## Q9: ¿Cómo hago el inventario semanal?

**Una vez por semana, ~30 minutos:**
1. Abrís Ingredientes en el sistema
2. Recorrés tu pantry físicamente
3. Para cada ingrediente, contás cuánto hay realmente
4. Comparás con lo que dice el sistema
5. Si la diferencia es chica (<10%), dejás
6. Si la diferencia es grande (≥10%), investigás: ¿se perdió una venta? ¿se perdió una fila de desperdicio? Corregís.
7. Anotás el recuento en una nota (opcional: lo agregamos al sistema después)

**Pro tip:** hacé el inventario el domingo a la noche, antes de la planificación del lunes.

---

## Q10: ¿Cómo registro una venta?

**Tres clicks:**
1. Click en "Nueva venta"
2. Elegís producto del dropdown, ponés cantidad
3. Click en "Registrar venta"

El sistema descuenta automáticamente el stock de los ingredientes, registra la venta, y actualiza el tablero. **Tomá el screenshot o exportá a Excel al final del día** como backup.

Si te equivocás (vendiste 12 muffins pero eran 2): andás a la lista de ventas, encontrás esa fila, click en "Anular". El stock vuelve automáticamente.

---

## Q11: ¿Puedo tener varios usuarios? (cajeros, por ejemplo)

**No, no en fase 1.** El sistema es single-user (vos). Si querés que un cajero ingrese ventas mientras vos gestionás inventario, eso es una segunda fase: agregar login + permisos por usuario.

**Workaround:** vos ingresás todas las ventas al final del día desde un papelito. O le das acceso a la compu al cajero por un rato y le decís qué teclas apretar (no recomendado: rompe el flujo).

---

## Q12: ¿Qué hago con los archivos viejos de Excel cuando empiece a usar el sistema?

**No los borres todavía.** Por 30-60 días, mantené los dos sistemas activos (Excel + sistema). Comparás: ¿los números del tablero del sistema coinciden con los del Dashboard_PL del Excel? Si todo coincide, podés archivar el Excel. Si hay diferencias, las investigamos.

Después de 90 días sin discrepancias, archivamos el Excel (lo movemos a una carpeta "HISTÓRICO" y le ponemos fecha en el nombre).

---

## How to use this FAQ

When Saskia asks any of these questions (or similar ones), paste the relevant Q&A into WhatsApp. Don't paraphrase — use the exact text. The tone and wording have been thought through.

If she asks something not on this list, **add the Q&A to this file** so future-Ivan has the answer ready.

---

*Drafted 2026-09 by Hermes. Operator review pending. Tone and wording: Paraguayan Spanish (vos).*


---

# voided packages 2026 08 12

_Source: original at `saskia-preflight/voided-packages-2026-08-12.md`_

# Voided packages archive — 2026-08-12 notes

> **For Ivan and any future agent.** This file preserves the **voided** commercial packages discussed on 2026-08-12, so they don't accidentally get resurrected as live quotes.
>
> **Source:** improvements review §5.5.
>
> **Status rule (AGENTS.md rule #8):** "12 Aug notes are void." These notes are archived for context only. They are NOT for sale, NOT for quoting, NOT for re-activation.

---

## Original 2026-08-12 package discussion

The following packages were discussed during the 2026-08-12 audio meeting and WhatsApp follow-ups. They were **superseded** by the 2026-08-18 / 2026-08-31 quotes that became the locked CURRENT-CONTEXT.md.

### Package A — "Setup completo" (Gs. 2.500.000)

| Item | Amount | Notes |
|---|---|---|
| Setup completo (onboarding + Marga manual + constitución + website + hosting setup) | Gs. 2.500.000 | One-time |
| Hosting mensual | Gs. 150.000–200.000 / mes | ~Gs. 1.800.000 / año |

**Why voided:** Saskia said she mainly needs **onboarding + website setup now**, and legal later. Full "initial setup" package as pitched was larger than her immediate buy. Hosting was also implicitly a recurring commitment she didn't want.

**Superseded by:** RMS local RMS at Gs. 17.500.000 / 24 cuotas, no hosting. See `docs/PACKAGE-AND-PRICING.md`.

---

### Package B — "Wesley-style isolated Hermes" (price not fixed)

| Item | Notes |
|---|---|
| Dedicated Hermes server on private VPS for Saskia | Discussed 2026-08-12 |

**Why voided:** This is **John van der Pol's SKU**, not Saskia's. AGENTS.md rule #1 of `saskia-personal-context/AGENTS.md` (post 2026-09 rewrite) explicitly states: *"Dedicated Hermes on a VPS is John's SKU. Do not quote 'Wesley-style isolated Hermes' to Saskia."*

**Superseded by:** Nothing. Saskia's product is the local RMS, not a dedicated server.

**Note:** the name "Wesley-style" is itself ambiguous. The 2026-08-12 conversation used it loosely to mean "isolated AI agent infrastructure." Don't reuse the term — it's not in anyone's product catalog.

---

### Package C — "Planning assistant" (Gs. 9.500.000)

| Item | Amount | Notes |
|---|---|---|
| Planning assistant (producción + compras + calendario) | Gs. 9.500.000 (38h estimate) | Internal; not in signed PDF |

**Why voided for fase 1:** Saskia asked about it on 2026-08-20 audio. Operator (Ivan) decided: park it. Do not include in the signed quote.

**Status:** Parked. See `docs/CURRENT-CONTEXT.md`: *"Planning: she asked; do not put it in the signature PDF."*

**Revisit:** Post-fase 1 (week 8+), re-derive the figure with her actual usage data. Don't auto-renew the 9.5M number.

---

## What to do if someone tries to re-quote these

1. **Point at this file.** Void for a reason.
2. **If they re-propose** the Gs. 2.5M setup package, that's **a new package**, not a re-quote of voided. Get explicit operator decision.
3. **If they re-propose** "Wesley-style Hermes", point at AGENTS.md rule #1. Different SKU, different client.
4. **If they re-propose** the planning assistant at Gs. 9.5M, that's the **parked** number. Re-derive before quoting.

## Cross-references

- `docs/CURRENT-CONTEXT.md` — locked current quote (Gs. 17.500.000 / 24 cuotas)
- `docs/PACKAGE-AND-PRICING.md` — locked package pricing
- `saskia-personal-context/AGENTS.md` rule #1 — John vs Saskia product split
- `docs/plans/2026-08-31-rms-fase-1-dev-plan.md` §2 — explicitly do not build these

---

*Archived 2026-09 by Hermes. Operator review pending.*


---

# lifecycle doc

_Source: original at `saskia-preflight/lifecycle-doc.md`_

# Lifecycle doc — workbook system vs new app

> **For Kiki and Saskia.** Explains the relationship between the existing workbook system (`saskia-personal-context/04_foodbiz-management-system/`) and the new RMS app (`saskia/app/`).
>
> **Source:** improvements review §2.8.
>
> **Status:** Draft. Operator review pending.

---

## Three phases

### Phase 0 — Now (before fase 1 ships)

| System | Role |
|---|---|
| **Workbook** (`saskia-personal-context/04_foodbiz-management-system/`) | Operational truth |
| **Drive Excels** | Working copies, hand-edited |
| **App** (`saskia/app/`) | Doesn't exist yet |

She edits in Drive or the workbook system. The Python scripts regenerate `.xlsx` from JSON. Source of truth is the workbook.

### Phase 1 — Fase 1 ships (week 6-8)

| System | Role |
|---|---|
| **App** (`saskia/app/`) | Operational truth — every sale, every stock move |
| **Workbook** | Still exists, still useful, but **no longer the source** |
| **Drive Excels** | Auto-generated exports from app (or hand-edited, then re-imported) |

She edits in the app. Drive Excels are read-only mirrors (or backups).

**During fase 1 itself** (week 1-6 of the build), she's likely still using the workbook system. The app doesn't exist yet. Phase 0 continues.

### Phase 2 — Day 90 post-fase 1

| System | Role |
|---|---|
| **App** | Sole operational truth |
| **Workbook** | Archived as historical reference |
| **Drive Excels** | Optional, generated on demand |

She edits only in the app. The workbook in `saskia-personal-context/04_foodbiz-management-system/` gets tagged "HISTORICAL — DO NOT EDIT" in its README. The personal-context repo becomes "history only."

## What this means for daily work

### Day 1 (fase 1 just shipped)

She keeps the workbook system active in parallel for 2-4 weeks while she gets comfortable with the app. Both are valid.

### Day 30 (one month in)

She starts using the app for daily sales. Workbook system still updated weekly (e.g., Sunday inventory reconciliation). The workbook is a "human review" of the app's data.

### Day 60 (two months in)

She stops editing the workbook. Drive Excels are auto-exports from the app. The workbook becomes a snapshot from app export.

### Day 90 (three months in)

The workbook system is frozen. Updates go to the app. If she wants to view historical workbook data, she exports from the app.

## What this means for the data model

The app's SQLite is the **single source of truth** for:
- All sales (with timestamps)
- All stock movements (via `sale_stock_move` table)
- All inventory state (current stock per ingredient)
- All recipes (current versions; see "versioning" caveat below)

The workbook system remains a **parallel data store** for:
- Recipe narratives (`recipes/*.md`)
- Process documentation (`processes/*.md`)
- Tab documentation (`tabs/*.md`)
- Initial seed data (the 63 ingredients, 20 recipes, etc.)

After fase 1 ships, the workbook system becomes **input → output**, not input → truth.

## Recipe versioning caveat

The new app uses **current recipe cost** for sales, not historical. If she changes a recipe in month 3, every sale from month 1 onwards recalculates against the new recipe cost when she looks at the dashboard. This is documented in the dev plan §5 as "Sale COGS = unit_cost × qty at **current** recipe (fase 1: current recipe, not historical cost. Document this; do not build cost layers)."

Recipe versioning (option (a) in §3.1 of the comprehensive improvements review) is a **fase 1.5 / 2 feature**, not fase 1. Until then, the lifecycle assumes she's OK with current-cost recalculation.

## When the lifecycle doc gets updated

Update this file whenever:
- A new phase begins (e.g., fase 1.5 ships, fase 2 ships)
- The workbook system gets a major update
- The app gets a major update
- Recipe versioning lands (then "versioning caveat" goes away)

---

*Drafted 2026-09 by Hermes. Operator review pending.*

