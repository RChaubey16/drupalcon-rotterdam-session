# Section 6: Ajax API vs HTMX, Compared Honestly (5 min)

## [0:00–0:30] Frame the honesty

"HTMX doesn't win everywhere. Let's put the two side by side across four axes, honestly."

## [0:30–2:00] DX & Performance

**DX** — The Ajax API is more ceremony: two files, two languages, a wrapper ID that has to match exactly. But it's deeply integrated with Form API — multi-step wizards, validation, AHAH patterns all come along for free because it's built into the same request cycle. HTMX is far less ceremony — one element, declarative attributes, no second file to go find. But state management for anything multi-step is on you; Drupal's Form API machinery doesn't travel with it.

**Performance** — An Ajax API response is a JSON command stack, interpreted by `core/drupal.ajax`, with `Drupal.behaviors` reattaching on every response — more payload, more client-side work per round trip. HTMX ships a ~14kb library, and the response *is* the HTML fragment itself — no reconstruction step, less to ship, less to execute.

## [2:00–3:30] Debugging & Progressive enhancement

**Debugging** — In the Ajax API, the Network tab shows an opaque command stack, not the resulting markup — you have to mentally execute the commands to know what changed, and a wrapper-ID typo fails silently. With HTMX, the Network tab shows the literal HTML that got inserted. What you see is what you got.

**Progressive enhancement** — Both are progressive by construction: `#ajax` layers onto a real form element the same way `hx-*` attributes layer onto a real `<a>`/`<form>`. Honestly, though, this axis is close to a wash — in practice, both patterns are rarely tested with JS actually disabled on real projects.

## [3:30–4:30] The verdict table

Visual — reuse the `.map-table` component from §4, four rows:

| Axis | Ajax API | HTMX |
|---|---|---|
| DX | More ceremony, but native Form API integration (multi-step wizards, validation) | Less ceremony, but state management is on you |
| Performance | Command-stack + behavior reattachment overhead | Small library, HTML-fragment response |
| Debugging | Opaque command stack; silent wrapper-ID failures | Transparent — see the actual HTML in the Network tab |
| Progressive enhancement | Built on Form API's fallback, but rarely tested | Same pattern, same caveat |

## [4:30–5:00] Recap + transition into §7

"Pick HTMX where round trips are simple and the UI is a fragment. Keep the Ajax API — or Drupal's own wizard-form machinery — where you need genuine multi-step state. This isn't a replace-everything story. It's a which-tool-for-which-job story. Next: how to actually bring HTMX into a project you already have, without a rewrite."

## Notes / open items

- Deliberately keeps one axis (DX, multi-step state) as a real Ajax API win rather than a clean HTMX sweep — keeps the comparison credible and sets up §7's "incremental adoption, not a rewrite" message.
- Progressive enhancement is called a wash rather than a win for either side — open question: keep as a 4th axis, or cut to 3 axes if 5 minutes feels tight for 4.
- No slides drafted yet for this section — the verdict table is the natural anchor visual, same `.map-table` component used in §4's recap slide.
