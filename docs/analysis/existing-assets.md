# Existing assets inventory

**Last updated:** 2026-08-12  
**Sources:** GitHub `Ai-Whisperers/saskia-personal-context` (tree `main` as of 2026-08-10 push) + client repo `Ai-Whisperers/saskia`  
**OPSEC:** No account numbers or statement contents here — only paths and roles.

---

## 1. Repos

| Repo | Role | Visibility |
|---|---|---|
| `Ai-Whisperers/saskia` | Client engagement (this docs set) | Private |
| `Ai-Whisperers/saskia-personal-context` | OPSEC + foodbiz workbooks + calendar | Private |
| `SaskiaWeiss1234/*` | Her bootcamp / learning | Public |
| `IvanWeissVanDerPol/Saskia` | Old Flask bakery app | Public, **archived** |
| `IvanWeissVanDerPol/SaskiaPersonal` | Cited as Finance ERP | **404** — confirm backup |

---

## 2. `saskia-personal-context` folder counts

| Folder | Files (approx) | Role |
|---|---:|---|
| `00_identity/` | 3 | Personal / household (sensitive) |
| `01_banking-accounts/` | 15 | Statements (sensitive) |
| `02_sessions/` | 20 | Session notes |
| `03_data-quality/` | 2 | Open questions |
| `04_foodbiz-management-system/` | 55 | **Admin Excels + Python + docs** |
| `05_research/` | 7 | Roadmaps / ecosystem design |
| `06_calendar-planner/` | 13 | FastAPI calendar MVP |

---

## 3. Workbooks (canonical ops)

Filenames below are **as stored in git** (legacy product label in filename). Treat as Saskia foodbiz admin files.

| File | ~Size | Role |
|---|---:|---|
| `data/HEREBUS_FoodBiz.xlsx` | 89 KB | Main: inventory, recipes, production, waste, P&L |
| `data/HEREBUS_Suppliers.xlsx` | 22 KB | Suppliers, price history, shopping |
| `data/HEREBUS_Analisis.xlsx` | 23 KB | KPI, pricing, risk, wishlist |
| `data/HEREBUS_Comparacion_Proveedores.xlsx` | 30 KB | Supplier comparison |
| `data/RECETARIO_EN_BLANCO.xlsx` | 8 KB | Blank new-recipe form |
| `data/FoodBiz_Management.xlsx` | 23 KB | USD blueprint (reference) |
| `data/originals/*.xlsx` | — | Legacy 7-recipe + empty template |

**Source of truth rule (personal-context AGENTS):** Python scripts regenerate xlsx; do not hand-edit formulas.

---

## 4. Supporting foodbiz assets

| Area | Contents |
|---|---|
| `python/` | Build/verify/port scripts (`build_workbook*.py`, `verify*.py`, suppliers/analisis builders) |
| `recipes/*.md` | Recipe docs (muffin, cheesecake, stroop, ontbijtkoek, carrot cake, frikandel, ketjap, blank) |
| `tabs/*.md` | Tab documentation |
| `processes/*.md` | Daily production, waste, inventory, delivery zones, out-of-stock, etc. |
| `data/*.json` | Legacy / new / sub-recipe extractions |
| `Inventory_filter.gs` | Apps Script filter helper |

---

## 5. Calendar planner

`06_calendar-planner/` — FastAPI + HTML MVP for production goals / hours / Google Calendar. Mock mode noted; OAuth setup was pending.

---

## 6. Client repo (`Ai-Whisperers/saskia`) assets

| Path | Role |
|---|---|
| `docs/NEXT-STEPS.md` | Execution order |
| `docs/intake/` | Questionnaire |
| `docs/sessions/` | Meeting notes |
| `docs/operations/` | Hermes / WA policies |
| `docs/analysis/` | This inventory + estimates |

---

## 7. Gaps (needs Saskia / live session)

| Gap | Effort (guess) | Blocks |
|---|---|---|
| Map “done in Hermes” → exact Drive/Sheets vs these xlsx | 1 session | Seal + single work path |
| Confirm cow email + Drive as hub | 15 min | All ops |
| Ingredient prices empty → fill after shopping | 1–2 sessions | Costing / pricing |
| Yields empty on recipes → fill when cooking | Ongoing | True cost/piece |
| WhatsApp Business not verified in git | Setup session | Orders |
| Local clone of personal-context on team machine | 30 min | Live Excel tour |
| `SaskiaPersonal` 404 / Finance.xlsx whereabouts | Ask Ivan | Personal ERP continuity |
| Hermes context isolation | Later project | Bleed (Sage etc.) |
| Website | P1 | Findability |

---

## 8. Hermes / Cursor access (summary)

See [access-and-tools.md](access-and-tools.md).

- GitHub: yes for both Saskia repos  
- Local Hermes CLI: installed; **0 sessions** about her on this machine  
- Cursor: client docs + API to personal-context; not live Hermes gateway memory  

---

## Next update trigger

After the next live tour with Saskia: fill Gap table with “found / missing” and owners.
