# Section 7: Incremental Adoption Strategy (4 min)

## [0:00–0:30] Frame

"You don't rip out the Ajax API to start using HTMX. They coexist on the same page — HTMX doesn't touch anything `#ajax` owns. Here's how to actually bring it into a project you already have."

## [0:30–2:00] Where to start: one library, one target

"The integration cost is one library definition and one `#attached`, reusing the exact render-array pattern from §4."

```yaml
# my_module.libraries.yml
my_module.htmx:
  js:
    https://unpkg.com/htmx.org@2.0.4:
      type: external
      minified: true
```

```php
$build['#attached']['library'][] = 'my_module/htmx';
```

"That's the whole footprint. No build step, no bundler, no npm required just to get htmx on the page — attach it to one render array, on one page, and it stays scoped there."

## [2:00–3:00] Pick candidates, don't do a sweep

"The Ajax API code that already works — leave it. HTMX is for new interactive spots, or the small annoying ones: a filter, a 'load more' pager, an autocomplete list. One element at a time, not a module-wide rewrite."

```html
<body hx-boost="true">
```

"`hx-boost` is the zero-risk on-ramp — it turns ordinary links and forms into HTMX requests automatically, full-page navigation becomes a partial swap, and nothing else in your markup has to change. It's how you get a feel for HTMX on a real site before you hand-write a single `hx-get`."

## [3:00–3:30] The rule of thumb

- New interactive feature → HTMX by default
- Existing, working Ajax API code → leave it alone
- Multi-step form state (wizards, Form API validation) → stays on Ajax API, per §6's verdict

## [3:30–4:00] Recap + transition into §8

"Add the library, pick a handful of low-risk candidates, boost the rest for free. No rewrite, no migration — just a second tool in the same box as the Ajax API. Next: where this actually stands in Drupal core today, and how you can help move it forward."

## Notes / open items

- Deliberately code+checklist only, no diagram — a practical/actionable change of pace after several diagram-heavy sections (§2–§4).
- Directly resolves §6's closing tease ("without a rewrite") — this section is that payoff, not a fresh topic.
- `hx-boost` wasn't introduced in §3's cheat sheet (which stuck to the four core attributes) — first appearance here, deliberately, since it's an adoption tool rather than a core concept.
- No slides drafted yet for this section.
