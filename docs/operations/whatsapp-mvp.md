# WhatsApp MVP options (Saskia)

**Last updated:** 2026-08-12  
**Status:** Choose one for P0  

---

## Goal

Customers can order; Saskia gets a clear notification; she can fulfill and log the sale.  
Full autonomous bot is **not** required for P0.

---

## Options

| Option | What we set up | Effort | Pros | Cons |
|---|---|---|---|---|
| **A. WA Business + free-text** | Business profile, greeting, hours, catalog optional | Low | Fastest; she already chats | Manual parsing of orders |
| **B. WA Business + product catalog** | Catalog of V1 products + prices | Medium | Clearer orders | Needs V1 list + prices |
| **C. Web → WhatsApp handoff** | Light site CTA `wa.me` with prefilled text | Medium | Nice UX; findability | Needs P1 site |
| **D. Automated bot** | Order state machine / Hermes bot | High | Scalable | Out of personal-context by default; price as P2 |

**Recommendation for P0:** **A**, then add **B** once V1 products/prices exist. **C** with site. **D** only after quote.

---

## Notifications

Minimum: new order reaches her on:

- [ ] WhatsApp (same Business number or personal)  
- [ ] Cow email  

Optional later: push into sales log / production planner (with Hermes approval gate).

---

## Out of stock

Personal-context has design notes (`processes/out-of-stock.md`).  

| Choice | P0? |
|---|---|
| She replies manually “no hay” | Yes |
| Auto out-of-stock replies | Later |

---

## Decision

- [ ] Option A / B / C / D  
- [ ] Number ready?  
- [ ] Who owns catalog updates (Saskia / team)?  

Record in `intake/answers-from-meetings.md`.
