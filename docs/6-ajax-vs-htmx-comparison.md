# Section 6: Ajax API vs HTMX, Compared Honestly (5 min)

## [0:00–0:30] Frame the honesty

"HTMX doesn't win everywhere. Let's put the two side by side across four axes, honestly."

## [0:30–2:00] DX & Performance

**DX** - The Ajax API is more ceremony: two files, two languages, a wrapper ID that has to match exactly. But it's the most proven path for complex multi-step forms and wizards - years of core and contrib patterns built on the same request cycle. HTMX is far less ceremony - one element, declarative attributes, no second file to go find. Since 11.3, `FormBuilder` can rebuild a form from an HTMX request with all its values available, so dynamic forms work - but complex multi-step flows are less proven.

**Performance** - An Ajax API response is a JSON command stack that `core/drupal.ajax` runs command by command. HTMX is a ~16kb library (min+gzip), and the response *is* the HTML itself - no command-interpretation step. Drupal.org reports that native HTMX in 11.3 "reduced the loaded JavaScript size by up to 71% for browser-server interactions, including HTML streaming with BigPipe." (Both re-attach `Drupal.behaviors` after an update - core's `drupal.htmx` does this on purpose - so that's not a difference.)

## [2:00–3:30] Debugging & Progressive enhancement

**Debugging** - In the Ajax API, the Network tab shows an opaque command stack, not the resulting markup - you have to mentally execute the commands to know what changed, and a wrapper-ID typo fails silently. With HTMX, the Network tab shows the literal HTML that got inserted. What you see is what you got.

**Progressive enhancement** - Both are progressive by construction: `#ajax` layers onto a real form element the same way `hx-*` attributes layer onto a real `<a>`/`<form>`. Honestly, though, this axis is close to a wash - in practice, both patterns are rarely tested with JS actually disabled on real projects.

## [3:30–4:30] The verdict table

Visual - reuse the `.map-table` component from §4, four rows:

| Axis | Ajax API | HTMX |
|---|---|---|
| DX | More ceremony, but the most proven path for complex multi-step forms | Less ceremony; 11.3 rebuilds forms from HTMX requests, multi-step patterns still maturing |
| Performance | JSON command stack, executed client-side | Small library, HTML response, up to 71% less JS loaded |
| Debugging | Opaque command stack; silent wrapper-ID failures | Transparent - see the actual HTML in the Network tab |
| Progressive enhancement | Built on Form API's fallback, but rarely tested | Same pattern, same caveat |

## [4:30–5:00] Recap + transition into §7

"Pick HTMX where round trips are simple and the UI is a fragment. Keep the Ajax API where it already works, and for complex multi-step flows until HTMX patterns there mature. This isn't a replace-everything story. It's a which-tool-for-which-job story. Next: how to actually bring HTMX into a project you already have, without a rewrite."

## Notes / open items

- Deliberately keeps one axis (DX, multi-step state) as a real Ajax API win rather than a clean HTMX sweep - keeps the comparison credible and sets up §7's "incremental adoption, not a rewrite" message.
- Progressive enhancement is called a wash rather than a win for either side - open question: keep as a 4th axis, or cut to 3 axes if 5 minutes feels tight for 4.
- No slides drafted yet for this section - the verdict table is the natural anchor visual, same `.map-table` component used in §4's recap slide.
- Fact-checked 2026-09-23: htmx size corrected to ~16k (htmx.org); behavior re-attachment removed as a differentiator; DX axis updated for 11.3's HTMX form rebuild support; 71% figure from the [drupal.org 11.3 announcement](https://www.drupal.org/about/core/blog/native-htmx-in-drupal-1130-rich-ux-with-up-to-71-less-javascript).
