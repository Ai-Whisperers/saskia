# Session notes — 2026-09 — visibility decision on saskia-personal-context

## Context

The parallel repo `Ai-Whisperers/saskia-personal-context` (PII + Excels
+ Python builders + session log) had been documented as PRIVATE in its
own README and AGENTS.md but was actually PUBLIC on GitHub.

## Decision

Operator (Ivan) reviewed three options:
- A. Private repo + outside collaborator (SaskiaWeiss1234, read-only)
- B. Keep public (chosen)
- C. Public mirror + private source

Picked B. Rationale: Saskia needs to read her HEREBUS workbooks without
joining the org or sharing credentials; the outside-collaborator path
adds one click of friction for marginal OPSEC benefit. The OPSEC trade-off
(identity + bank PII + IBAN exposed) was explicitly accepted.

## Operational changes

- `SECURITY.md` added to repo root (GitHub-recognised security policy).
- `AGENTS.md` rule #1 rewritten (public-by-decision); rule #6 added
  (no new PII without operator OK).
- `README.md` claims of PRIVATE flipped to PUBLIC with disclosure block
  + Visibility section.
- `CHANGELOG.md` decision entry recording rationale, alternatives
  considered, revisit conditions.

Commit `dddeef03` on `main`. See commit message for full detail.

## Branch close

The `compliance/whatsapp-banlist-scrub` branch (1 commit, global
`WhatsApp` → `Messaging` find/replace) was closed without merging.
Rationale: the replacement produces nonsense prose and turns
`AGENTS.md` rule #6 into gibberish. See
`BRANCH-DECISION-whatsapp-banlist-scrub.md` in `saskia-personal-context`.

## herebus archive

`Ai-Whisperers/herebus` archived via GitHub API PATCH on 2026-09.
Description unchanged. New visitors see the red archived banner.

## Status

- saskia-personal-context: public, decision documented
- herebus: archived
- compliance/whatsapp-banlist-scrub: closed
- follow-up: INDEX rebuild on saskia-personal-context (planned separately)

[reconstructed session note: written from commit metadata + AGENTS.md
history. Operator should verify dates and details.]
