# How Saskia opens and edits admin files

**Last updated:** 2026-08-12  
**Status:** Draft until Drive hub confirmed  

---

## Single work path (target)

She should need **one** place for day-to-day ops:

1. **Google Drive (cow email)** — working copies of Excel / Sheets she edits on phone/PC  
2. **Git backup** — `saskia-personal-context` / `04_foodbiz-management-system/data/` as versioned backup  

Do **not** make her hunt across Hermes chat exports, random Downloads, and three Drive folders.

---

## What she edits vs what agents regenerate

| She edits (inputs) | Agents / scripts maintain |
|---|---|
| Ingredient prices, stock counts | Formula cells, sheet protection |
| Recipe yields after cooking | Cross-sheet lookups structure |
| Production / waste / sales rows | Python rebuild of template structure |
| Supplier comparison inputs | Analysis dashboard formulas |

If structure/formulas break: team regenerates from Python — she should not fight locked cells.

---

## Recommended daily flow

1. Open Drive folder (cow email) → main FoodBiz workbook.  
2. Update prices when she shops.  
3. Log production / waste / sales the same day.  
4. WhatsApp orders → log sale (and fulfill).  
5. Weekly: team or Hermes-with-approval syncs backup to git if needed.

---

## Friday / first tour agenda

1. Open GitHub or local clone → show `04_foodbiz-management-system/data/`.  
2. Open main workbook in Excel.  
3. Point to Inventory + one Recipe_* + Production_Planner.  
4. Show `AGENTS.md` rules in one sentence: “agents don’t randomly rewrite this.”  
5. Agree Drive folder = daily; git = backup.

---

## Blockers until confirmed

- [ ] Exact Drive folder URL (private note / password manager — not committed if sensitive)  
- [ ] Which file is “the” daily workbook vs analysis/suppliers satellites  
- [ ] Phone workflow: Excel app vs Google Sheets  
