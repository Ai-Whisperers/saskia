# Access and tools (team)

**Last updated:** 2026-08-12  

---

## What we can reach from Cursor (this workstation)

| Resource | Access |
|---|---|
| `Ai-Whisperers/saskia` | Yes — git clone + push |
| `Ai-Whisperers/saskia-personal-context` | Yes — GitHub API / `gh` (private) |
| Local clone of personal-context | Not present under `02-Work` (clone for live Excel) |
| Hermes CLI (`hermes` v0.19) | Installed at `%LOCALAPPDATA%\hermes` |
| Hermes sessions about Saskia | **None** on this machine (`sessions list` = 0) |
| Hermes MCP inside Cursor | **No** |
| Company Hermes gateway / her phone chats | **Not** visible here |

---

## Implications

- Durable context for documentation = **GitHub repos**, not local Hermes memory.  
- To “see what she did in Hermes,” export sessions from the **server/gateway** or ask her for Drive/Sheets links.  
- Cursor agent ≠ Hermes agent; do not assume shared memory.

---

## Recommended setup for next session

```
gh repo clone Ai-Whisperers/saskia-personal-context
# open 04_foodbiz-management-system/data/*.xlsx in Excel
```

Optional: `hermes sessions export` from the machine/gateway that actually holds her chats.
