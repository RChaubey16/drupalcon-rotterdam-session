# Session Flow: From Ajax API to HTMX

45-minute session flow, mapped to the commitments in `session-details.md`.

| # | Section | Time | Content |
|---|---------|------|---------|
| 1 | Hook + intro | 2 min | Open with framing quote ("smarter HTML, not more JS"), state the arc of the talk |
| 2 | Ajax API: evolution & pain points | 6 min | Quick history, how it works today, where it breaks down (JS coupling, complexity, maintenance) |
| 3 | HTMX & hypermedia principles | 6 min | Core HTMX concepts (hx-get/post/swap/trigger), the hypermedia-driven-app idea |
| 4 | Mapping HTMX onto Drupal's architecture | 8 min | How HTMX fits render arrays, routing, caching — the conceptual bridge |
| 5 | Live demo | 10 min | Dynamic form, filtering, partial page update — built live or walked through |
| 6 | Ajax API vs HTMX comparison | 5 min | DX, performance, debugging, progressive enhancement — side by side |
| 7 | Incremental adoption strategy | 4 min | How to introduce HTMX into an existing Drupal project without a rewrite |
| 8 | Current status + how to contribute | 3 min | Where the core initiative stands, contribution opportunities |
| 9 | Wrap-up / Q&A buffer | 1 min + spillover | Recap, invite questions |

Total ≈ 45 min.

## Design notes

- **Live demo placed mid-talk (§5)**, not at the end — the comparison (§6) and adoption strategy (§7) sections can reference the concrete example, and a demo stumble here still leaves recovery room before the close.
- **One combined demo** covering all three use cases (form, filter, partial update) rather than three separate demos, to fit the 10-minute window.
- **Architecture mapping (§4) and HTMX principles (§3) are split** into two sections rather than combined, since this is the conceptual core of the talk.

## Coverage check against session-details.md

| Proposal commitment | Section |
|---|---|
| Evolution and limitations of Ajax API | §2 |
| Core principles of HTMX and hypermedia | §3 |
| Mapping HTMX concepts to Drupal architecture (render arrays, routing, caching) | §4 |
| Dynamic forms, filtering, partial page updates | §5 |
| Ajax vs HTMX: DX, performance, debugging, progressive enhancement | §6 |
| Incremental adoption strategies in existing projects | §7 |
| Current status in Drupal core + contribution opportunities | §8 |
| "Real-world examples and a live demonstration" | §5 |
| Opening quote | §1 |

All 5 learning objectives from the proposal map to a section: evolution/tradeoffs → §2 + §6, HTMX principles → §3, applying to use cases → §5, comparison → §6, contribution paths → §8.

**Open item:** the abstract says "real-world examples" (plural). Current plan has one combined demo — consider adding a short before/after Ajax API code snippet in §2 or §6 if a second, distinct example is wanted.
