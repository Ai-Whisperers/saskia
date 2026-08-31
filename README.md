# ⚠️ This repository has been split — see below

**This is the legacy engagement repo for the Saskia Weiss Vander RMS fase 1
engagement.** It was archived on 2026-09. New work does NOT go here.

The original single-repo design mixed personal/family data (PII) with the
RMS app source code. That's now split into two repos:

| Repo | Visibility | Contents |
|---|---|---|
| **[`Ai-Whisperers/saskia-context`](https://github.com/Ai-Whisperers/saskia-context)** | **private** | Saskia's identity, bank statements, foodbiz workbook system, engagement commercial docs (CURRENT-CONTEXT, PACKAGE-AND-PRICING), session notes, OPSEC posture. **Contains PII.** |
| **[`Ai-Whisperers/saskia-app`](https://github.com/Ai-Whisperers/saskia-app)** | **public** | The RMS app source code: `app/`, `tests/`, `installer/`, build specs, dev plan, install checklist. **No PII.** |

If you came here for the **engagement context** (quote, OPSEC posture, who
Saskia is, food-business workbook system), go to **`saskia-context`**.

If you came here for the **app source code** (FastAPI + SQLite + openpyxl),
go to **`saskia-app`**.

## What was here

The original `Ai-Whisperers/saskia` had 13 commits:
- 1–4: Visibility decision + INDEX rebuild + relationship-wording fix (now in `saskia-context`)
- 5–8: Session notes + lineage note (now in `saskia-context`)
- 9–11: Build specs + tech-stack review + comprehensive-improvements review
  (3 stay in `saskia-app`, 2 go to `saskia-context`)
- 12: 21-file pre-signoff skeleton (the build: `app/`, `tests/`, `installer/`,
  `pyproject.toml`, `LICENSE`, CI workflow — now in `saskia-app`)

## Why this URL is preserved

We could have deleted this repo. We chose to archive it instead so:

- **Existing bookmarks continue to resolve.** Anyone with this URL lands on
  a useful redirect page rather than a 404.
- **History is preserved.** The 13-commit history shows the full arc of the
  engagement, useful for future audits.
- **Cross-links don't break.** Many docs in `saskia-context` and
  `saskia-app` reference each other; the archived README is the
  cross-reference map.

## Links

- [`Ai-Whisperers/saskia-context`](https://github.com/Ai-Whisperers/saskia-context) (private)
- [`Ai-Whisperers/saskia-app`](https://github.com/Ai-Whisperers/saskia-app) (public)
- The locked engagement commercial docs (CURRENT-CONTEXT, PACKAGE-AND-PRICING,
  NEXT-STEPS) are now in `saskia-context/docs/`.

---

_Archived 2026-09. Read-only. No new issues or PRs accepted._
_Maintained by Ivan / Erebus / Hermes._
