# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is not a software project — it contains prep material for a DrupalCon Rotterdam conference talk titled "From Ajax API to HTMX: Evolving Drupal Interactions." The talk covers Drupal's Ajax API, how HTMX maps onto Drupal's render/routing/caching architecture, and practical migration/adoption strategies.

`session-details.md` is the session proposal: description, learning objectives, prerequisites, target audience, and metadata (track, difficulty, format) as submitted to the conference. It is the source of truth every other doc here is checked against.

There is no build, lint, or test tooling here — work in this repo is writing/editing talk content (slides, demo code, notes), not maintaining an application.

## Prep docs (`docs/`)

Session prep is built section-by-section, one file per section, numbered in delivery order:

- `1-session-flow.md` — the full 45-minute run-of-show: 9 timed sections mapped against every commitment in `session-details.md` (topics + learning objectives), with design notes on ordering choices (e.g. live demo placed mid-talk, not at the end).
- `2-hook-intro.md` — content for §1 (Hook + Intro, 2 min). Two drafted variants; the live micro-demo teaser is the selected version (open item: teaser clip needs to be built from the §5 demo, and the self-intro line needs the actual role/one-liner filled in).
- `3-ajax-api-pain-points.md` — content for §2 (Ajax API: Evolution & Pain Points, 6 min). Includes a sequence diagram of the Ajax API request/response cycle, a code snippet (`#ajax` property + `AjaxResponse`/`ReplaceCommand`), a coupling diagram illustrating the wrapper-ID mismatch failure mode, and four pain points (JS coupling, complexity, maintenance overhead, discoverability). Closes by teasing the HTMX idea without naming it, saving the reveal for §3's opening.

**Recurring pattern per section:** draft content in chat (with diagrams/code where useful) → get explicit approval → write to numbered file in `docs/`. Each file carries its own "Notes / open items" list for unresolved decisions — check those before treating a section as final.

## Slides (`docs/slides.html`)

A self-contained HTML slide deck (published as a Claude Artifact) built from the approved section docs above. Currently covers §1 (Hook + Intro) and §2 (Ajax API: Evolution & Pain Points) — 15 slides.

- Keyboard/click navigation (arrows, spacebar, edge-click zones), progress bar + counter.
- Press `N` to toggle a speaker-notes drawer with the full scripted line per slide, sourced from the `docs/*.md` files.
- Color system: cool blue (`--ajax`) is used throughout for Ajax API content; a warm amber (`--htmx`) is reserved and only appears once, on the final slide's "→ next: HTMX" cue, to foreshadow the HTMX section's identity once built.
- Two placeholders still open: the speaker's role/company line (slides 1 and 4, marked `add role / company`).

**When adding a new section's slides:** append new `<section class="slide">` blocks (each needs a `data-notes` attribute for the notes drawer) to the existing markup in `docs/slides.html`, reuse the established CSS classes (`.eyebrow`, `.display`, `.bullets`, `.pain-card`, `.media-box`, etc.), and republish the same file path to update the existing artifact rather than creating a new one. Once §3 content introduces HTMX, switch its section-divider and accent usage to the warm `--htmx` token to complete the color handoff the deck already sets up.

**Next up:** §3, HTMX & hypermedia principles (6 min per `1-session-flow.md`) — not yet started.
