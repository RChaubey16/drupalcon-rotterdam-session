# Section 8: Current Status + How to Contribute (3 min)

## [0:00–1:00] Where things stand today

"This isn't a someday story. HTMX came into core as a dependency in 11.2 and became fully featured in 11.3, as part of an active initiative to gradually replace the Ajax system. Both APIs are running side by side right now, on purpose, while contrib gets time to convert. And the payoff is measurable: drupal.org reports up to 71% less JavaScript loaded for browser-server interactions."

- Core initiative: [Replace AJAX API with HTMX](https://www.drupal.org/community-initiatives/replace-ajax-api-with-htmx) - an official Drupal community initiative, still active. Plan issue: "[Plan] Gradually replace Drupal's AJAX system with HTMX". HTMX was added as a dependency in 11.2 and is fully featured in 11.3 - [drupal.org/project/drupal/issues/3404409](https://www.drupal.org/project/drupal/issues/3404409).
- Contrib module `drupal/htmx` adds extra tooling on top of what's in core - [drupal.org/project/htmx](https://www.drupal.org/project/htmx), 1,156 sites already using it.
- Stat: native HTMX in 11.3 "reduced the loaded JavaScript size by up to 71% for browser-server interactions, including HTML streaming with BigPipe" - [drupal.org announcement](https://www.drupal.org/about/core/blog/native-htmx-in-drupal-1130-rich-ux-with-up-to-71-less-javascript).

## [1:00–2:00] Why "both APIs at once" is deliberate, not messy

"Core is keeping the old Ajax API and the new HTMX path running together on purpose - full deprecation waits until contrib has had real time to convert. That's the same incremental philosophy from §7, just at the core level instead of your project's."

Worth distinguishing from adjacent core initiatives that occupy similar "less heavy JS" territory but solve a different problem - page building, not interaction:

- Experience Builder - page/layout building, not interaction plumbing.
- Single Directory Components - component authoring, not interaction plumbing.

## [2:00–2:40] How to contribute

- Initiative page: [drupal.org/community-initiatives/replace-ajax-api-with-htmx](https://www.drupal.org/community-initiatives/replace-ajax-api-with-htmx) - the starting point.
- Core issue queue: the [plan issue #3404409](https://www.drupal.org/project/drupal/issues/3404409) and its child issues. (The [POC issue #3446642](https://www.drupal.org/project/drupal/issues/3446642) is closed/fixed - its learnings moved into those child issues - so don't send people there.)
- Contrib module issue queue: [drupal.org/project/htmx/issues](https://www.drupal.org/project/htmx/issues).
- Community: **#htmx** on Drupal Slack - lead collaborators talk there almost daily; coordination meetings every other Thursday, left open for 24 hours for all time zones.
- Concrete entry points: triage issues, convert a `#ajax`/Command usage to HTMX and report back, write docs/examples, build real-world case studies like this talk.

## [2:40–3:00] Close + transition to §9

"This isn't waiting on permission - it's already in core, it's actively being worked, and it needs hands. If you're on 11.3, it's already on your site: go convert one `#ajax` callback in your own project and see what it feels like."

## Notes / open items

- Corrects the earlier draft's premise: HTMX is not contrib-only. It landed in core as of 11.3 via the "Replace AJAX API with HTMX" initiative - the contrib module now layers extra tooling on top of that core integration rather than being the sole source of it. Verified against drupal.org on 2026-09-22; re-check before the talk in case release/usage numbers move.
- Deliberately mirrors §7's "incremental, not a rewrite" framing at the core level - both APIs coexisting on purpose is the same philosophy scaled up.
- Given only 3 minutes, kept to two beats plus a close - "where it lives today" + "how to get involved" - no deep initiative history.
- No name-dropping of individual contributors (deliberate choice) - keeps the section purely about "here's how you start," not about who's already involved.
- No slides drafted yet for this section.
- Re-checked 2026-09-23: initiative title is "[Plan] Gradually replace Drupal's AJAX system with HTMX"; Ajax API not deprecated (both maintained "possibly several major Drupal versions"). drupal/htmx 2.0 requires core ^11.3. Closing line no longer says "install the contrib module", since on 11.3 htmx is already there.
- 2026-09-23: added the official initiative page (https://www.drupal.org/community-initiatives/replace-ajax-api-with-htmx) as the primary link on the status + contribute slides; initiative name is "Replace AJAX API with HTMX" (the plan issue carries the longer "Gradually replace..." title). POC issue dropped from the contribute slide since it's closed.
