# Section 7: Incremental Adoption Strategy (4 min)

## [0:00–0:30] Frame

"You don't rip out the Ajax API to start using HTMX. They coexist on the same page - HTMX doesn't touch anything `#ajax` owns. Here's how to actually bring it into a project you already have."

## [0:30–2:00] Where to start: it's already in core

"On Drupal 11.3 and up there's nothing to install. Core ships htmx as `core/htmx`, plus `core/drupal.htmx` for the Drupal glue: asset loading and behaviors."

```php
// Using the Htmx class? It attaches core/drupal.htmx for you.
// Hand-writing hx-* attributes in Twig? One line:
$build['#attached']['library'][] = 'core/drupal.htmx';
```

"If you use the `Htmx` class from §4, it attaches the library for you. If you hand-write hx attributes in Twig, it's one `#attached` line. No library definition, no CDN, no build step."

## [2:00–3:00] Pick candidates, don't do a sweep

"The Ajax API code that already works - leave it. HTMX is for new interactive spots, or the small annoying ones: a filter, a 'load more' pager, an autocomplete list. One element at a time, not a module-wide rewrite."

```html
<nav hx-boost="true">
```

"`hx-boost` is a low-effort way to try it - links and forms inside the boosted element become HTMX requests automatically, and nothing else in your markup has to change. Scope it to one region first: each boosted request still swaps the whole `<body>` by default, so test page-level JS like the toolbar and BigPipe. It's how you get a feel for HTMX on a real site before you hand-write a single `hx-get`."

## [3:00–3:30] The rule of thumb

- New interactive feature → HTMX by default
- Existing, working Ajax API code → leave it alone
- Complex multi-step forms (wizards, heavy Form API flows) → keep them on the Ajax API for now, per §6's verdict

## [3:30–4:00] Recap + transition into §8

"Use what core ships, pick a handful of low-risk candidates, boost one region to get a feel for it. No rewrite, no migration - just a second tool in the same box as the Ajax API. Next: where this actually stands in Drupal core today, and how you can help move it forward."

## Notes / open items

- Deliberately code+checklist only, no diagram — a practical/actionable change of pace after several diagram-heavy sections (§2–§4).
- Directly resolves §6's closing tease ("without a rewrite") — this section is that payoff, not a fresh topic.
- `hx-boost` wasn't introduced in §3's cheat sheet (which stuck to the four core attributes) — first appearance here, deliberately, since it's an adoption tool rather than a core concept.
- Fact-checked 2026-09-23: replaced the unpkg CDN library (outdated and contradicts §8 - htmx ships in core as `core/htmx` / `core/drupal.htmx`, and `Htmx::applyTo()` attaches it per [api.drupal.org](https://api.drupal.org/api/drupal/core!lib!Drupal!Core!Htmx!Htmx.php/class/Htmx/11.x)). hx-boost softened from "zero-risk" - per [htmx docs](https://htmx.org/attributes/hx-boost/) boosted requests target `<body>` with innerHTML by default.
