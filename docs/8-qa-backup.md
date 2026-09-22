# Section 8: Q&A Backup Material

Not part of the timed 45-minute run-of-show (see `1-session-flow.md`, §9 "Wrap-up / Q&A buffer"). This is reference material to have ready if attendees ask questions the main sections don't cover directly.

## Browser memory & caching (Ajax API vs HTMX)

Likely to come up given how technical this crowd tends to be. Kept simple and analogy-driven so it's easy to say out loud, not read off a slide.

| Axis | Ajax API | HTMX |
|---|---|---|
| **Browser memory** | Always loads jQuery + Drupal's Ajax machinery, even for one small interaction. Cleanup after each update is left to whoever wrote that code - if they forget, the page slowly gets heavier the longer you use it. | Lighter by default - no jQuery required. But it keeps a copy of recent pages in browser storage for the back button, so that storage can grow on content-heavy sites. |
| **Caching** | Almost always uses POST requests - and POST requests are never cached by the browser or by Drupal's page cache, so every single update hits the server fresh. | Defaults to GET requests, the same kind used for normal page loads - so these updates *can* be cached and reused, just like a regular page, as long as it's set up correctly. |

**Plain-English one-liner for the audience:** "Ajax API always does a fresh server trip for every update - HTMX's updates can be cached like a normal page, because they use the same kind of request a browser normally makes."

### Showing it live, if the demo allows

If there's time (or a question prompts it) during §5's filter demo, this is visible in ~15 seconds via the Network tab - no extra tooling needed:

1. Trigger the same filter twice.
2. Point at the **Method** column - Ajax API shows `POST` every time; the HTMX version (`hx-get`) shows `GET`.
3. On the repeat trigger, point at the **Size** column for the HTMX request - `(from disk cache)` or a `304` shows up. The Ajax API request never shows this - it's a fresh hit every time.

Lower-risk than a memory heap-snapshot comparison, which is fragile to run live and not worth the time against §5's already-tight 10-minute window for three use cases. If used, narrate it inline while doing the filter interaction already planned - don't add it as a separate demo beat.

## CSRF & security on GET requests (Ajax API vs HTMX)

A real gotcha, not just theory - worth having a confident answer ready.

| Axis | Ajax API | HTMX |
|---|---|---|
| **CSRF protection** | Drupal's Form API attaches a CSRF token automatically as part of the form - it just travels along with every Ajax submission. | `hx-get` doesn't carry a token automatically - GET requests aren't supposed to change data, so Drupal doesn't expect one there. For anything that changes state, use `hx-post` and add the token yourself (`hx-headers` or a hidden field), same as any other Drupal form. |

**Plain-English one-liner for the audience:** "Drupal hands the Ajax API a security token for free because it's still a form. With HTMX, you only need to think about that when you're changing something - reads stay plain and simple, writes still need the same token Drupal forms have always used."

## Back/forward button behavior (Ajax API vs HTMX)

Connects directly to the memory/caching answer above, since it's the same history-cache tradeoff from a different angle.

| Axis | Ajax API | HTMX |
|---|---|---|
| **Back/forward button** | Does nothing by default - an Ajax update doesn't change the URL or add a browser history entry unless a developer writes extra code for it. Hit back and you're off the page entirely, not back to the state before the update. | `hx-boost`/history support does this out of the box - back and forward move through the states the page was actually in. The tradeoff is the same one from the memory table: it works by keeping page snapshots in browser storage. |

**Plain-English one-liner for the audience:** "With the Ajax API, the back button ignores your update and takes you off the page. HTMX can make the back button actually undo the update - it just costs a bit of browser storage to remember those states."

## SEO & crawlability (Ajax API vs HTMX)

Smaller point, but an easy one to answer confidently if it comes up.

| Axis | Ajax API | HTMX |
|---|---|---|
| **SEO / crawlability** | Ajax callbacks are POST requests returning a command stack, not a URL a search engine or a `curl` would ever fetch on its own - effectively invisible to crawlers. | HTMX fragments are usually plain GET requests, the same kind a crawler already follows - so the content behind an `hx-get` is naturally reachable, not hidden behind JavaScript. |

**Plain-English one-liner for the audience:** "A search engine can't see what an Ajax API update produces - it's not a URL. An HTMX update usually *is* a URL, so it's naturally visible."

## Notes / open items

- Add further anticipated questions here as they come up during prep, each as its own `##` subsection.
- Not wired into `slides.html` - this is speaker reference only, not deck content.
