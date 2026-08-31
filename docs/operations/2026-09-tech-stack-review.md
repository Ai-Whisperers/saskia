# Saskia tech-stack review — alternatives + trade-offs

> **For Ivan, Kiki, and any future agent reviewing the locked tech decisions.**
>
> **Date:** 2026-09
> **Source:** `/opt/data/profiles/ivan/.hermes/plans/2026-08-31_192054-saskia-comprehensive-improvements.md` + deep dive on `docs/plans/2026-08-31-rms-fase-1-dev-plan.md` + `docs/operations/2026-09-fase-1-specs.md` + `docs/operations/import-mapper.md`.
>
> **Hat:** SR engineer who has shipped local-first apps before, knows what works and what bites 18 months later.
>
> **Constraint:** the dev plan is **locked**. Quote is Gs. 17.500.000 / 24 cuotas = 70h. We don't have budget to swap stacks mid-build. Most recommendations here are **for fase 2 or operator decision before kickoff**.

---

## The locked stack, summarised

| Layer | Locked choice | Implicit choice |
|---|---|---|
| Language | Python 3.13 | (one of the few she's likely to have or install) |
| Web framework | FastAPI | async-capable, modern, but **we use sync handlers only** |
| ASGI server | uvicorn | single-process, foreground |
| DB | SQLite | (local file, no server) |
| ORM | SQLAlchemy | (sync mode, not async) |
| Excel | openpyxl | (already used in workbook system) |
| Frontend | server-rendered HTML OR HTMX | (not decided which; HTMX adds interactivity) |
| Templates | Jinja2 (implied by FastAPI choice) | |
| Tests | pytest | (no UI tests, no CI) |
| Install | `run.bat` opens browser to `127.0.0.1:8765` | (single PC, single user, no auth) |
| Pkg format | `pyproject.toml` OR `requirements.txt` | (not decided; §4 says "or") |
| Path | `%LOCALAPPDATA%\AIW-Saskia\` on Windows; `~/Library/Application Support/AIW-Saskia/` on Mac | |

Everything else is open. Let me go layer by layer.

---

## 1. Python 3.13 — alternatives

### Locked: Python 3.13

### Alternative 1: Python 3.12 (drop one version)

- **Pros:** 3.13 is only ~10 months old (released Oct 2024). Most production deployments are still on 3.11 or 3.12. The GIL-experimental `free-threaded` mode is 3.13-only; we don't need it. Libraries she might have (numpy for any future cost-calc, pandas for analytics) are rock-solid on 3.12 but may have wheel delays on 3.13.
- **Cons:** Marginal. 3.13 is fine for our use case.
- **Verdict:** **Skip.** The difference doesn't matter for a 70h build that doesn't use any 3.13-specific feature. **But** — see "external-pc install" below — the install step has real risk.

### Alternative 2: Install via Anaconda / pyenv / uv

- **Pros:** uv (the new pip-alternative from Astral) is **10-100× faster** than pip for install + resolve, and is the recommended install for Python projects in 2025+. Anaconda is the "batteries-included" runtime that handles scientific packages.
- **Cons:** Adds a dependency on a toolchain. `uv` requires the user to install `uv` first. Anaconda is a 500MB download. The dev plan says "Python 3.13 installed on PC" — it doesn't specify *how*.
- **Verdict:** **Use `uv` if we're installing from scratch.** The install step becomes `irm https://astral.sh/uv/install.ps1 | iex; uv python install 3.13; uv venv; uv sync` — single PowerShell line, fast, reproducible. **Switch from "official Python installer" to `uv`** in the install session. ~30 min of work; saves 5-15 min on every install + every reinstall.

### Alternative 3: Frozen Docker container (no Python install on her PC)

- **Pros:** `python:3.13-slim` image + `uv` inside = reproducible, no Windows-specific path issues, no version drift.
- **Cons:** Requires Docker Desktop on her PC. Adds 1GB+ install. Overkill for 70h build.
- **Verdict:** **Skip.** Docker on a non-technical user's Windows PC is asking for support tickets.

### My recommendation

Keep Python 3.13, but switch the install story from "official Python installer" to **`uv`-managed venv**. Document this in `installer/README.md` (referenced in dev plan §9 Task 9). Cost: ~30 min to rewrite the install spec. Pays for itself the first time she reinstalls.

---

## 2. FastAPI — alternatives

### Locked: FastAPI + uvicorn

### Alternative 1: Flask

- **Pros:** Smaller surface area. Single dev (Armin Ronacher). She might already have Flask-flavoured code from the old `IvanWeissVanDerPol/Saskia` repo (which I audited earlier — Flask app, abandoned, archived).
- **Cons:** No built-in data validation (need marshmallow or pydantic). No built-in OpenAPI. Smaller ecosystem for typed APIs.
- **Verdict:** **Skip.** FastAPI's Pydantic integration is a major win for the data model we have (7 tables, complex relationships). Switching to Flask means re-implementing validation that FastAPI gives free.

### Alternative 2: Django

- **Pros:** Batteries-included (admin, ORM, auth, migrations). Mature.
- **Cons:** ~10× heavier than FastAPI for what we need. ~3-second startup. Doesn't fit "single-file Python app you run with `run.bat`". Built-in admin would be overkill for single-user.
- **Verdict:** **Skip.** Wrong size.

### Alternative 3: Starlette (no FastAPI)

- **Pros:** Smaller dependency tree. FastAPI is built on it.
- **Cons:** Lose Pydantic integration. Lose automatic OpenAPI. We'd be re-implementing what FastAPI gives.
- **Verdict:** **Skip.** FastAPI IS Starlette + Pydantic. Why strip the value-add?

### Alternative 4: Litestar (formerly Starlite)

- **Pros:** More modern than FastAPI in some ways. Better type-driven routing. Built-in DTOs.
- **Cons:** Smaller ecosystem. Smaller community. Kiki may not know it.
- **Verdict:** **Skip.** The ecosystem cost (smaller community, fewer StackOverflow answers, fewer plugin tutorials) outweighs the marginal feature gain.

### Alternative 5: Replace the whole thing with a static-site generator + browser-side JS

- E.g., **Vite + React + SQLite-via-WASM (sql.js)**. No backend server, no Python on her PC.
- **Pros:** No backend to install. PWA can work offline. Modern UX.
- **Cons:** WASM SQLite is slow for our workload. Lose Excel-as-import-target (we'd have to convert to JSON or CSV). Lose the "Python is source of truth" pattern that already exists in the workbook system.
- **Verdict:** **Skip.** Adds 3-4 days of work to set up the toolchain and writes off the existing Python investment.

### My recommendation

**Keep FastAPI.** But specify **`uvicorn[standard]`** (not bare `uvicorn`) so we get the recommended extras: `httptools`, `uvloop`, `websockets`, `watchfiles` (for the `--reload` mode during dev). Marginal size cost, gets us proper async performance + auto-reload during build. ~5 min of build time.

---

## 3. SQLite — alternatives

### Locked: SQLite

### Alternative 1: DuckDB

- **Pros:** Faster analytical queries (columnar). Better for the dashboard's "ranking by margin" computations. Single-file like SQLite.
- **Cons:** Heavier client library (~50MB vs SQLite's ~5MB). Less mature for transactional writes. She'd need to learn a different mental model.
- **Verdict:** **Skip for fase 1.** SQLite handles 1000 sales easily. DuckDB is for analytical workloads with millions of rows. If fase 2 brings reporting-heavy features, **consider migrating** at that point.

### Alternative 2: PostgreSQL (local)

- **Pros:** "Real" RDBMS. Network-accessible if she ever wants multi-device.
- **Cons:** Requires install + service management. ~200MB. Overkill for single-user local.
- **Verdict:** **Skip.** Violates the "single user, no hosting" principle in CURRENT-CONTEXT.md.

### Alternative 3: JSON file (no DB)

- **Pros:** Zero dependency. Trivial backup (just copy the file).
- **Cons:** No querying. No transactions. No concurrency. Sales are append-only, but recipes/ingredients are updateable. Updating without locks = lost writes.
- **Verdict:** **Strong no.** We have 7 tables with FK relationships. JSON files would mean re-implementing the DB in Python, badly.

### Alternative 4: SQLite + WAL (already in spec)

Already locked in via `2026-09-fase-1-specs.md §A`. **Confirmed.** No change.

### My recommendation

**Keep SQLite + WAL.** Add **`PRAGMA secure_delete = ON`** to the existing pragmas — when a row is deleted (e.g., a voided sale cleanup), overwrite the bytes. Costs nothing; closes a tiny OPSEC hole. **Update spec accordingly.**

---

## 4. SQLAlchemy — alternatives

### Locked: SQLAlchemy (sync mode)

### Alternative 1: SQLModel

- **Pros:** Built on Pydantic + SQLAlchemy. Type-safe. The FastAPI author's other project.
- **Cons:** Newer, smaller ecosystem. Less mature for complex queries. Kiki would need to learn a new ORM.
- **Verdict:** **Worth considering for fase 2.** For fase 1, SQLAlchemy 2.x's declarative + typed Mappings already gives us Pydantic-style validation via FastAPI's dependency injection. Switching to SQLModel adds ~1h of learning curve for marginal gain. Skip for now.

### Alternative 2: Raw SQL with sqlite3 stdlib

- **Pros:** Zero ORM dependency. Direct control. Easier to debug.
- **Cons:** Lose type safety. Lose migrations (well, we'd never have them anyway). Lose query composition.
- **Verdict:** **Skip.** We have 7 tables with FK constraints. Raw SQL becomes spaghetti at that scale.

### Alternative 3: Peewee

- **Pros:** Tiny. Single-file ORM. Less ceremony than SQLAlchemy.
- **Cons:** Smaller ecosystem. Less FastAPI integration. Kiki probably knows SQLAlchemy better.
- **Verdict:** **Skip.**

### My recommendation

**Keep SQLAlchemy 2.x.** Specify **`sqlalchemy[asyncio]` is NOT needed** in `pyproject.toml` — we use sync mode. Document the explicit choice to avoid future-Kiki thinking they should "modernize" to async.

---

## 5. Excel handling — alternatives

### Locked: openpyxl

### Alternative 1: polars

- **Pros:** Faster (Rust-based, ~10× on large files). Has Excel reader/writer. Better for analytics.
- **Cons:** Heavier dependency (~50MB). She'd need to learn polars syntax if we expose it.
- **Verdict:** **Skip for fase 1.** openpyxl handles the 89KB HEREBUS_FoodBiz.xlsx in milliseconds. Polars doesn't help.

### Alternative 2: pandas

- **Pros:** Industry standard for Excel in Python. Lots of tutorials.
- **Cons:** ~50MB dependency for what's effectively tabular I/O. Slower than openpyxl for our use case (openpyxl is designed for Excel, pandas is designed for analysis).
- **Verdict:** **Skip.** Same as polars.

### Alternative 3: xlsxwriter

- **Pros:** Faster writes than openpyxl. Better for generating reports.
- **Cons:** Read-only, doesn't work for the import path. Would need openpyxl for read + xlsxwriter for write = 2 deps.
- **Verdict:** **Skip.** Adds complexity for marginal benefit.

### Alternative 4: pyexcel + pyexcel-xlsx

- **Pros:** Unified API across xlsx/xls/ods/csv.
- **Cons:** Slower than openpyxl. Smaller community. Adds abstraction that hides Excel-specific issues.
- **Verdict:** **Skip.** We have one Excel format.

### My recommendation

**Keep openpyxl.** Already used in the workbook system (`saskia-personal-context/04_foodbiz-management-system/python/`), so reusing it means consistent Excel handling across both repos. **Confirm** in `pyproject.toml` that we pin `openpyxl >= 3.1,< 4` (current 3.1.x, no breaking changes until 4.x).

---

## 6. Frontend — alternatives

### Locked: "server-rendered HTML or HTMX"

### Alternative 1: Pure server-rendered HTML (no JS framework)

- **Pros:** Simplest possible. No build step. Works on every browser. Zero JS to debug.
- **Cons:** Every action = full page reload. Sale form → list page → form again = 3 full reloads. "Real-time" stock alerts would require polling.
- **Verdict:** **Pick this for fase 1.** It matches the "70h budget, single user, simple workflow" constraint. HTMX is fancier but adds a dependency.

### Alternative 2: HTMX

- **Pros:** Drop-in extension to server-rendered HTML. `<button hx-get="/sales/new">` swaps in the form. No React/Vue. ~14KB JS.
- **Cons:** Still requires the user to have JS enabled (default yes in modern browsers). The form-validation UX for the Paraguayan Spanish copy needs more thought (inline error display).
- **Verdict:** **Pick this if there's budget for ~5h of UI polish.** The dashboard widget "sale preview" (dev plan §5: "show predicted stock post-sale + alert if < minimum") is significantly better with HTMX than with full reloads.

### Alternative 3: Vue 3 (single-file components, no build step via CDN)

- **Pros:** Reactive UIs without a build step. ~30KB.
- **Cons:** Vue templates in the browser mean Spanish copy is split between Python and JS — annoying. Lose the "read the source code, see the copy" simplicity.
- **Verdict:** **Skip.** Wrong size. Vue wins over HTMX only if you have a real-time dashboard with many reactive widgets — we have one.

### Alternative 4: React + Vite

- **Pros:** Modern. Industry-standard. Huge ecosystem.
- **Cons:** Build step (Vite). JS bundle. Dependency hell. Lose the "open .py files in any editor" simplicity.
- **Verdict:** **Strong no.** For a 70h quote, React is 20-30h overhead we can't afford.

### Alternative 5: HTMX + Alpine.js + Tailwind (utility-first CSS)

- **Pros:** HTMX for server round-trips, Alpine.js for client-side state, Tailwind for styling. No build step. ~30KB total JS.
- **Cons:** Three new concepts. Tailwind's class soup is hard to read for non-frontend devs (Kiki?). Styling is opinionated.
- **Verdict:** **Skip.** Pick one or the other, not all three.

### My recommendation

**Pure server-rendered HTML for fase 1, plan HTMX as a fase 1.5 upgrade.**

- Fase 1: pure HTML + minimal CSS. Forms POST, results re-render. ~3h of UI work saved vs HTMX.
- Fase 1.5 (after engagement ends, if she wants): add HTMX for the dashboard widgets. ~5h of work, well-bounded, can be billed as a separate small quote.

**In the spec, lock the choice.** The current "server-rendered OR HTMX" leaves it ambiguous. Make a call: **pure server-rendered for fase 1**, references HTMX as fase 1.5.

---

## 7. Templating — alternatives

### Locked (implied): Jinja2

### Alternative 1: Plain Python f-strings + `html.escape()`

- **Pros:** Zero template engine. Every output is explicit.
- **Cons:** No inheritance. No macros. No URL helpers. Tedious for 30+ templates.
- **Verdict:** **Skip.** We have 7 templates per the file map. Jinja2 is the right size.

### Alternative 2: Mako

- **Pros:** Faster than Jinja2. Simpler syntax.
- **Cons:** Less FastAPI integration (FastAPI's `Jinja2Templates` is built-in). Smaller community.
- **Verdict:** **Skip.** Jinja2 wins by default for FastAPI.

### Alternative 3: Just .md files (markdown → HTML)

- **Pros:** Operators (you, Kiki) can edit docs in markdown.
- **Cons:** Wrong tool for an interactive app. Use this for `docs/operations/*` (already done), not for the UI.
- **Verdict:** **Skip.** Wrong layer.

### My recommendation

**Keep Jinja2.** But specify **`jinja2[asyncsupport]`** is NOT needed (sync mode). Document `app/templates/base.html` as the layout template; child templates extend it.

---

## 8. Money handling — alternatives

### Locked (in `2026-09-fase-1-specs.md`): `Decimal` + custom `to_int_gs()`

### Alternative 1: `decimal.Decimal` everywhere + SQLAlchemy's `Numeric`

- **Pros:** What the spec already does.
- **Verdict:** **Locked.** No change.

### Alternative 2: Use an existing money library (e.g., `money`, `py-moneyed`, `currencies`)

- **Pros:** Battle-tested. Multi-currency support if she ever expands.
- **Cons:** Adds a dependency. `py-moneyed` stores in cents — we'd need a custom Guaraní backend. Paraguayan formatting (period-as-thousands-sep, no decimals) isn't built into any of these libraries.
- **Verdict:** **Skip.** Our custom `to_int_gs()` is 5 lines and matches the spec exactly. Adding a library buys us nothing.

### Alternative 3: Store as integer-only, format on display

- **Pros:** Simplest possible. No decimal arithmetic in the DB.
- **Cons:** Some recipes might need decimal intermediate calculations (e.g., "5.25 oz of flour × $13.99/lb" requires conversion to a common unit). If we round too aggressively, we lose precision.
- **Verdict:** **Already what the spec does.** No change.

### My recommendation

**Keep the custom `money.py`.** Add a docstring explaining "why we don't use `py-moneyed`" so future-Kiki doesn't try to add it. ~10 min of build time.

---

## 9. Tests — alternatives

### Locked: pytest

### Alternative 1: unittest (stdlib)

- **Pros:** Zero dependency.
- **Cons:** Verbose. Less ergonomic than pytest fixtures.
- **Verdict:** **Skip.** pytest is the de-facto Python standard.

### Alternative 2: pytest + hypothesis (property-based testing)

- **Pros:** Hypothesis auto-generates edge cases. For money rounding, this is **really useful** — generates 100s of edge cases that hand-written tests miss.
- **Cons:** Adds a dependency. Steeper learning curve for Kiki.
- **Verdict:** **Worth it for `money.py` and `costing.py`.** Add `hypothesis` to dev deps. ~1h to write 5 property tests. Pays back the first time she hits a rounding bug.

### Alternative 3: pytest + Playwright (UI tests)

- **Pros:** Catches the "the form doesn't actually submit" class of bugs.
- **Cons:** Playwright is ~200MB. Browser binary downloads. Heavy for a 70h build.
- **Verdict:** **Skip for fase 1.** Fase 1.5 if we add UI polish.

### My recommendation

**Add `hypothesis` to dev deps. Write 5 property tests for `money.py`.** Total: ~1h. Lives inside the existing spec at `2026-09-fase-1-specs.md`.

Single line in `pyproject.toml`:
```toml
[project.optional-dependencies]
dev = ["pytest>=8", "hypothesis>=6"]
```

---

## 10. Packaging / install — alternatives

### Locked (implied): `pyproject.toml` OR `requirements.txt` + `pip install -r` + `run.bat`

### Alternative 1: `uv` (recommended above)

- **Pros:** 10-100× faster than pip. Reproducible lockfile (`uv.lock`). Single binary. Industry trend.
- **Cons:** Requires installing `uv` first.
- **Verdict:** **Strong yes.** Already recommended above. Update spec to use `uv`.

### Alternative 2: `pip-tools` + `requirements.txt`

- **Pros:** Pure stdlib Python. No extra tool.
- **Cons:** Slow. Manual lockfile management. ~3-5 min for a fresh install.
- **Verdict:** **Skip** if `uv` is on the table.

### Alternative 3: Poetry

- **Pros:** Single tool (deps + venv + packaging). Pretty output.
- **Cons:** Adds a heavy Python wrapper. Poetry 1.x had lockfile issues. Poetry 2.x (2024+) is better but still heavier than `uv`.
- **Verdict:** **Skip.** `uv` is the future; Poetry is the past.

### Alternative 4: Nix / NixOS

- **Pros:** Hermetic, reproducible to the bit.
- **Cons:** Steep learning curve. Operator-side complexity.
- **Verdict:** **Strong no.** Wrong audience.

### Alternative 5: PyInstaller / Nuitka (compiled binary)

- **Pros:** User doesn't need Python at all. Single .exe.
- **Cons:** ~30 min to set up. Cross-platform builds require separate compile per OS. Updates require re-distributing the binary. Hard to debug.
- **Verdict:** **Consider for fase 1.5**, not fase 1. Saves the "install Python + venv + dependencies" step but adds "distribute the .exe" step. Net positive if she upgrades laptops often, net negative if she upgrades rarely.

### My recommendation

**Commit to `uv`.** Rewrite the install spec from `run.bat` → Python install → `pip install` → `uvicorn` to `run.bat` → `uv` install → `uv sync` → `uv run uvicorn`. ~1h of spec-writing. Pays back forever.

---

## 11. No migrations framework — implicit decision

### Current: `create_all()` on startup

### Alternative 1: Alembic (the SQLAlchemy-canonical migrations tool)

- **Pros:** Schema changes become versioned, reviewable, reversible. Industry standard for SQLAlchemy.
- **Cons:** Adds a dep + learning curve. For a single-user local app where "delete rms.sqlite and re-create" is always an option, it's overkill.
- **Verdict:** **Skip for fase 1.** Document the convention: "schema changes → bump `CURRENT_SCHEMA_VERSION` in `db.py` → add a migration function → call it on startup if version mismatches." This is ~30 lines and handles the 90% case (single schema-change per release).

### Alternative 2: Hand-written migrations (`migrations/001_init.sql`, `002_add_supplier_pii.py`)

- **Pros:** Total control. No magic.
- **Cons:** Manual bookkeeping. Easy to forget to run them.
- **Verdict:** **Skip.** Alembic does this better.

### My recommendation

**Hand-rolled versioned migrations in `app/db.py`.** Add 30 lines of code. Document in `app/README.md`:
```python
CURRENT_SCHEMA_VERSION = 1
MIGRATIONS = {
    1: "initial schema",
    # 2: "add supplier_pii table",  # future
}
def migrate_if_needed(session):
    current = session.execute(text("PRAGMA user_version")).scalar() or 0
    if current < CURRENT_SCHEMA_VERSION:
        # run migrations in order
        ...
```

Total: ~30 min. Saves "delete and re-create" cycles during dev.

---

## 12. No CI / no auto-deploy — implicit decision

### Current: local discipline only

### Alternative 1: GitHub Actions (free for public repos)

- **Pros:** Catches test failures on every push. Cheap.
- **Cons:** The repo is private... wait, the engagement repo `Ai-Whisperers/saskia` is **public**. So GitHub Actions is free. The only cost is the ~30 min to set up a workflow file.
- **Verdict:** **Add a minimal CI workflow.** Trigger on PR to main. Run `pytest`. That's it.

### Alternative 2: Pre-commit hooks (local)

- **Pros:** Runs before commit, not after push. Catches issues faster.
- **Verdict:** **Add `.pre-commit-config.yaml`** with `ruff` (lint) + `pytest-fast` (smoke). ~30 min of setup.

### My recommendation

**Add both.** GitHub Actions for "the operator can't merge if tests fail" + pre-commit hooks for "the dev can't commit if tests fail". Total ~1h of setup. **Pays back the first time a regression slips through.**

---

## 13. Logging / observability — implicit decision (no)

### Current: nothing

### Alternative 1: `loguru` (single-file logger)

- **Pros:** Drop-in replacement for stdlib `logging`. Pretty output. Zero config.
- **Cons:** One more dep.
- **Verdict:** **Add `loguru` for app logs.** Single import: `from loguru import logger`. Logs go to `~/.local/share/AIW-Saskia/app.log` (rotated daily, 7-day retention). **Cost: 15 min.** Worth it for debugging "what happened during that sale entry at 2pm Tuesday."

### Alternative 2: stdlib `logging` to file

- **Pros:** Zero deps.
- **Cons:** Verbose config. Easy to mis-configure.
- **Verdict:** **Skip** if `loguru` is on the table.

### Alternative 3: Sentry / structured observability

- **Pros:** Catches errors in production.
- **Cons:** Requires an account, network, and we'd be sending logs to a third party. **OPSEC red flag** for a family-data app.
- **Verdict:** **Strong no.** She's running this locally; no need for third-party error reporting.

### My recommendation

**Add `loguru`.** Update spec to include app logging. Total cost: ~30 min including config.

---

## 14. Single-process uvicorn — alternative

### Current: foreground uvicorn

### Alternative 1: System service (systemd / launchd / Windows Service)

- **Pros:** Auto-start on boot. Crash recovery. Logs go to syslog.
- **Cons:** Platform-specific. Linux: systemd unit. macOS: launchd plist. Windows: NSSM-wrapped service. Three different files.
- **Verdict:** **Skip for fase 1.** Spec `installer/README.md` should mention "if she restarts her laptop, double-click the shortcut." User-driven restart is fine for single-user.

### Alternative 2: `uvicorn` + `supervisor` (process manager)

- **Pros:** Auto-restart on crash.
- **Cons:** Adds a dep. Operator-side config.
- **Verdict:** **Skip for fase 1.** Fase 1.5 if she reports crashes.

### My recommendation

**Stay with `uvicorn` foreground for now.** Add to installer notes: "if app crashes, close the terminal and re-run `run.bat`." That's the support burden for a 70h quote.

---

## 15. Single-user, no auth — implicit decision

### Current: no login (dev plan §1 "no login")

### Alternative 1: OS-level PIN (Windows Hello / macOS Touch ID)

- **Pros:** Biometric auth is free and ubiquitous.
- **Verdict:** **Already there.** OS-level auth protects the PC. The app runs after login.

### Alternative 2: Local PIN (4-digit at app startup)

- **Pros:** Speed bump. Defense-in-depth.
- **Verdict:** **Skip for fase 1.** Add as a Task 11 (post-fase-1) at Gs. 250.000/h if she asks.

### Alternative 3: Full auth (username + password)

- **Verdict:** **Skip.** Violates "no login" principle.

### My recommendation

**Document the threat model.** Add to `app/README.md`:
> Threat model: single user, single PC, single trust boundary. Anyone with physical access to the laptop has full access. If this is a shared family PC, set up OS-level user accounts per person.

---

## Summary table — what to actually change

| # | Layer | Locked choice | Recommended change | When | Cost |
|---|---|---|---|---|---|
| 1 | Python install | Python 3.13 official installer | **Add `uv` to install story** | Before kickoff | 30 min |
| 2 | Web framework | FastAPI | **Pin `uvicorn[standard]`** | Task 1 | 5 min |
| 3 | DB | SQLite + WAL | **Add `PRAGMA secure_delete=ON`** | Task 1 | 1 line |
| 4 | ORM | SQLAlchemy sync | **Document "sync, not async"** | Task 1 | doc only |
| 5 | Excel | openpyxl | **Pin `>=3.1,<4` in pyproject** | Task 2 | 1 line |
| 6 | Frontend | "HTML or HTMX" | **Decide: pure HTML for fase 1, HTMX for fase 1.5** | Before Task 3 | 5 min (decision) |
| 7 | Templating | Jinja2 (implied) | **Document explicitly** | Before Task 3 | 1 line |
| 8 | Money | Decimal + custom | **Add "why no py-moneyed" docstring** | Task 2 | 10 min |
| 9 | Tests | pytest | **Add `hypothesis` for money tests** | Task 2 | 1h |
| 10 | Packaging | pyproject or requirements | **Commit to `uv` + `pyproject.toml`** | Before kickoff | 1h |
| 11 | Migrations | `create_all()` | **Add versioned migrations** | Task 1 | 30 min |
| 12 | CI | none | **Add GitHub Actions + pre-commit** | Before kickoff | 1h |
| 13 | Logging | none | **Add `loguru`** | Task 1 | 30 min |
| 14 | Process mgr | none | **Stay foreground** | n/a | 0 |
| 15 | Auth | none | **Document threat model** | Task 1 | 5 min |

**Total recommended work: ~6 hours of spec/code updates, all in fase 1 budget (70h) and all reducing long-term risk.**

**Recommended phasing:**

- **Before kickoff (does not affect clock):**
  - #1 (`uv`), #10 (`pyproject.toml`), #12 (CI), #15 (threat model doc) — all setup, no business logic

- **Tasks 1-2 (skeleton + costing engine):**
  - #2 (`uvicorn[standard]`), #3 (`secure_delete`), #4 (sync-mode docs), #8 (money docstring), #9 (hypothesis), #11 (migrations), #13 (loguru)

- **Before Task 3:**
  - #6 (frontend decision), #7 (Jinja2 explicit)

- **Fase 1.5 (if/when):**
  - #5 `xlsxwriter` for exports, HTMX upgrade, etc.

---

## What I deliberately did NOT recommend changing

- **FastAPI → something else**: all alternatives are worse for our use case.
- **SQLite → something else**: nothing fits single-user local better.
- **openpyxl → something else**: we already use it in the workbook system; consistency matters.
- **Single-process uvicorn → multi-worker**: adds complexity for zero benefit at single-user scale.
- **No auth → full auth**: violates the locked scope ("no login").

## What I'd push back on in code review

If a PR lands for fase 1 and any of these are true, I'd block:

1. **Async code without justification.** FastAPI sync mode is the locked choice. Don't sneak in `async def` handlers.
2. **Float for money.** Even one occurrence. Use `Decimal`.
3. **Migration framework added.** We hand-roll schema-versioning. Don't pull in Alembic mid-build.
4. **Docker / containers in installer.** Violates "single PC, no hosting" scope.
5. **External services (Sentry, Datadog, etc.).** OPSEC red flag.
6. **Breaking changes to openpyxl API.** Pin `>=3.1,<4`.
7. **Adding a CSS framework without asking.** Bootstrap/Tailwind = dependency on a build pipeline.

## Open questions for the operator

1. **Does Saskia have Python on her PC already?** (Task 0 pre-flight answers this.)
2. **Does she have `git` on her PC?** (Needed for some of the installer scripts.)
3. **Does she have admin rights to install Python/uv?** (If not, fall back to portable Python.)
4. **Does the Mac-vs-Windows answer change any of these recommendations?** Most don't, but the installer path differs.

## What this means for "the improvements review" doc

The review at `/opt/data/profiles/ivan/.hermes/plans/2026-08-31_192054-saskia-comprehensive-improvements.md` covers 40+ items. **None of those 40 items overlap with this tech-stack review.** They're orthogonal:
- Improvements review: **what** to build + **what scope** to build
- Tech-stack review (this doc): **how** to build it, with alternatives

Both should land in the engagement repo. The improvements review is already there (`docs/operations/2026-09-comprehensive-improvements-review.md`). This tech-stack review should land as a companion.

---

*Drafted 2026-09 by Hermes. Operator review pending. Most of this is fence-sitting — the existing stack is mostly right; the changes are small polish, not rewrites.*
