# Section 8: Current Status + How to Contribute (3 min)

## [0:00–1:00] Where things stand today

"This isn't a someday story - HTMX landed in Drupal core in 11.3, as part of an active community initiative to replace the Ajax API. Both APIs are running side by side right now, on purpose, while contrib gets time to convert."

- Core initiative: "Replace AJAX API with HTMX," landed in 11.3, still active - [drupal.org/project/drupal/issues/3404409](https://www.drupal.org/project/drupal/issues/3404409).
- Contrib module `drupal/htmx` adds extra tooling on top of what's in core - [drupal.org/project/htmx](https://www.drupal.org/project/htmx), 1,156 sites already using it.

## [1:00–2:00] Why "both APIs at once" is deliberate, not messy

"Core is keeping the old Ajax API and the new HTMX path running together on purpose - full deprecation waits until contrib has had real time to convert. That's the same incremental philosophy from §7, just at the core level instead of your project's."

Worth distinguishing from adjacent core initiatives that occupy similar "less heavy JS" territory but solve a different problem - page building, not interaction:

- Experience Builder - page/layout building, not interaction plumbing.
- Single Directory Components - component authoring, not interaction plumbing.

## [2:00–2:40] How to contribute

- Core initiative issue queue: the plan issue above, plus the [POC issue #3446642](https://www.drupal.org/project/drupal/issues/3446642).
- Contrib module issue queue: [drupal.org/project/htmx/issues](https://www.drupal.org/project/htmx/issues).
- Community: **#htmx** on Drupal Slack.
- Concrete entry points: triage issues, convert a `#ajax`/Command usage to HTMX and report back, write docs/examples, build real-world case studies like this talk.

## [2:40–3:00] Close + transition to §9

"This isn't waiting on permission - it's already in core, it's actively being worked, and it needs hands. Install the contrib module today, or go convert one `#ajax` callback in your own project and see what it feels like."

## Notes / open items

- Corrects the earlier draft's premise: HTMX is not contrib-only. It landed in core as of 11.3 via the "Replace AJAX API with HTMX" initiative - the contrib module now layers extra tooling on top of that core integration rather than being the sole source of it. Verified against drupal.org on 2026-09-22; re-check before the talk in case release/usage numbers move.
- Deliberately mirrors §7's "incremental, not a rewrite" framing at the core level - both APIs coexisting on purpose is the same philosophy scaled up.
- Given only 3 minutes, kept to two beats plus a close - "where it lives today" + "how to get involved" - no deep initiative history.
- No name-dropping of individual contributors (deliberate choice) - keeps the section purely about "here's how you start," not about who's already involved.
- No slides drafted yet for this section.
