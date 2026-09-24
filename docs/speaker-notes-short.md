# Speaker Notes (Short Version)

Full spoken script for **From Ajax API to HTMX: Evolving Drupal Interactions**, slide by slide. Slide numbers match `Ajax to HTMX - Short.pptx` (36 slides).

**How to read this:** plain text is what you say, word for word if you need to. *[Italic brackets]* are stage cues, not spoken. Each section heading has its time budget. At ~130 words a minute the spoken script runs about 26 minutes on its own; pauses, clicks, pointing at code and the live demo fill the rest. Total budget: about 33 minutes including an 8-minute demo, leaving 5-10 minutes for Q&A. If rehearsal runs long, cut "A brief history" (slide 8) and "The response is the next state of the UI" (slide 19) first.

*[Analogy]* cues mark short optional analogies. They reuse a few pictures on purpose, so the audience learns each one once: **flat-pack furniture** (Ajax sends instructions, HTMX sends the finished chair) and a **restaurant** (hypermedia, the four attributes, `_htmx_route`). The two marked *skip first* are the ones to drop if you're running long.

---

## §1 · Hook + Intro (2.5 min)

### Slide 1: From Ajax API to HTMX: Evolving Drupal Interactions

*[Have this up while people settle in. Don't speak yet. When it's time to start, go straight to the next slide.]*

### Slide 2: Live teaser

*[Play the 15-20 second clip in silence. Let the filter update a few times, then pause on the result.]*

*[If the clip won't play, say:]* What you should be seeing here is a filter on a Drupal listing page, updating as you type, with no page reload. We'll build exactly that later on.

### Slide 3: What just happened

That's a live filter.
No JavaScript file.
No Ajax command class.
Just HTML.

*[Pause a beat.]*

### Slide 4: Who's talking

**Ruturaj:** Hi everyone, I'm Ruturaj, a full stack engineer at QED42.

**Vighnesh:** And I'm Vighnesh, a full stack Drupal engineer, also at QED42.

### Slide 5: The premise

*[Let the slide land. Read it slowly.]*

Here's the idea behind this whole talk. The future of Drupal UX isn't more JavaScript. It's smarter HTML. With HTMX.

*[Pause.]*

### Slide 6: Where we're headed

Here's where we're going over the next 35 minutes.

First, why the Ajax API gets painful as your project grows. Then the core idea behind HTMX, and how that idea maps onto things Drupal already has: routing, render arrays and caching.

Then we'll watch it work live: a form, a filter and a partial page update, with barely any JavaScript.

After that, an honest comparison of the two, because HTMX doesn't win everywhere. Then how to bring it into a project you already have, without a rewrite. And finally, where HTMX stands in Drupal core today, and how you can help.

---

## §2 · Ajax API: Evolution & Pain Points (5.5 min)

### Slide 7: Ajax API: Evolution & Pain Points (section divider)

So let's start with where we are today: the Ajax API.

### Slide 8: A brief history

Drupal's Ajax API isn't new. It's been in core in its current form since Drupal 7, unified under one framework. In Drupal 8 through 11 it was rebuilt on top of Symfony's HttpKernel. AjaxResponse extends Symfony's JsonResponse class, and every interaction is expressed as a stack of Command objects.

It's mature. It's well documented. And it's everywhere in core: exposed filters, autocomplete, modal dialogs, inline entity forms.

So I want to be clear. This isn't a story about a broken system. It's a story about a system that was the right answer for 2011, and is starting to show its age.

### Slide 9: Ajax API request/response cycle

Here's how it works, as a sequence.

You give a form element an #ajax property: a callback, a wrapper and an event. The user triggers that event, say a change on a select. The browser sends a request to your callback. On the server, Drupal builds an AjaxResponse and adds commands to it, like a ReplaceCommand. That comes back as JSON: a list of commands, not HTML. The core/drupal.ajax library runs those commands one by one, and finally the DOM gets updated and Drupal.behaviors re-attach.

*[Analogy]*

Think of it like flat-pack furniture. You order a chair, and the server doesn't send you a chair. It sends you a box of parts and an instruction sheet: remove this, replace that, attach this here. The browser is the one sitting on the floor, assembling it.

Count the steps: seven. Keep that number in mind. We'll come back to it.

### Slide 10: The #ajax property

And here's what that looks like in code.

At the top, the form element with its #ajax array: a callback, a wrapper called results-wrapper, and the change event. Below it, the callback itself: create an AjaxResponse, add a ReplaceCommand that targets #results-wrapper, and return it.

Now look at that wrapper ID. That string lives in your PHP, and a matching ID has to exist in the markup, usually in a Twig template three directories away. Two files, two languages, and one string that has to match exactly. There's no compiler checking that, no linter.

*[Analogy]*

It's like posting a parcel to flat 12B when the building only has a 12A. The parcel doesn't come back stamped "address not found". It just quietly never arrives. That's a wrapper ID mismatch: nothing happens. Silently.

### Slide 11: Four pain points

So I'd sum up the pain in four points.

One, JS coupling. The built-in commands are covered, but every custom interaction needs a matching pair: a PHP command on the server and a JS command handler on the client. Often in different files, sometimes in different modules.

Two, complexity. The command pattern is imperative. You're telling the DOM what to do, step by step, instead of describing what the result should look like.

*[Analogy - skip first if you're running long]*

It's the difference between turn-by-turn GPS directions and just giving the taxi driver an address. With commands, you're dictating every single turn. Most of the time, you'd rather just say where you want to end up.

Three, maintenance overhead. One typo, no error, silent failure.

And four, discoverability. Look at the rendered HTML and nothing tells you that element is interactive. The behavior is buried in a PHP array, and the target lives in another file you have to go and find.

### Slide 12: A true story

And I'll admit, that third one is personal.

I once spent 40 minutes in devtools debugging an Ajax response that updated one region but silently failed on another. The bug wasn't in the PHP. It wasn't in the JavaScript logic. It was one mismatched wrapper ID, defined in a template three directories away from the PHP that built the response.

Nothing threw an error. It just didn't work.

*[Pause.]*

### Slide 13: …the element itself could just describe what it needs?

So what if, instead of a callback, a command class and a wrapper ID to keep in sync for every interaction, the element itself could just describe what it needs?

When this changes, fetch this URL, and put the result here.

No command classes. No wrapper IDs to keep in sync. No second file to go find. What would that even look like?

*[Pause, then click.]*

---

## §3 · HTMX & Hypermedia Principles (4 min)

### Slide 14: The reveal: it's called HTMX

That's not a hypothetical. It's called HTMX. And it's built on an idea that's actually older than Ajax: hypermedia.

### Slide 15: HTMX & Hypermedia Principles (section divider)

*[Let the section number land.]*

So what is hypermedia, and why does it matter for Drupal? Let's unpack it.

### Slide 16: The idea: hypermedia

And you already know hypermedia, even if you've never used the word.

Every a href you've ever written, and every form, is a hypermedia control. It's an element that tells the browser what to fetch, and where to put the result. A link fetches a page and replaces the whole window. A form sends data and does the same.

HTMX just extends that vocabulary. Any element can make a request. On any event. With any HTTP verb. And put the result into any part of the page.

*[Analogy]*

Think of a restaurant. A normal link is like asking the waiter for a new drink, and they clear the whole table and reset it from scratch just to bring it. HTMX lets you ask the waiter to swap just the glass, and leave everything else exactly where it is.

### Slide 17: One element. Four attributes.

Here it is in code. A select element with four attributes, and these four are most of what you need to know.

hx-get: the verb and the URL to fetch. You also have hx-post, hx-put and hx-delete. hx-trigger: when to fetch it, here on change, but it can be any DOM event. hx-target: where the result goes, any element on the page. And hx-swap: how to put it in, here by replacing the inner HTML, or outerHTML, beforeend, afterbegin and a few more. The server sends back just that fragment of HTML.

*[Analogy]*

Think of these four as the order slip you hand a waiter. What you're asking for: the verb. When to send the order: the trigger. Which table it goes to: the target. And how to serve it, replace the plate or add to it: the swap.

One element, four attributes. The behavior lives right there in the tag. No PHP file to go find, no wrapper ID to keep in sync. Remember the discoverability problem? This is the answer. The markup describes itself.

### Slide 18: HTMX request/response cycle

Here's the same interaction as a sequence.

The user triggers the event. The browser makes a normal HTTP request to the hx-get URL. The server responds with an HTML fragment. Not JSON, not commands. And HTMX swaps it into the target.

Compare that to the Ajax version from a few minutes ago. Same three participants: user, browser, server. But four steps instead of seven.

### Slide 19: The response is the next state of the UI

And this is the bigger idea.

There's no separate JSON API here. No client-side templating layer rebuilding HTML from data. The server decides what HTML comes back, the same way it always has for the first page load.

So HTMX isn't a new architecture. It closes the gap between the first page load and everything after it, so both work the same way. That's what hypermedia-driven means: the response is the next state of the UI, already rendered.

*[Analogy]*

Back to the flat-pack furniture. The Ajax API sends you the parts and the instruction sheet. HTMX sends you the chair, already assembled. You just put it where it goes.

So HTMX gives Drupal back a vocabulary it never really lost: hypermedia. The real question is how it fits Drupal's own architecture. That's next.

---

## §4 · Mapping HTMX onto Drupal's Architecture (5 min)

### Slide 20: Mapping HTMX onto Drupal's Architecture (section divider)

Here's the thing. HTMX doesn't need anything special from Drupal. It needs three things Drupal already has, and has had for over a decade: routes, render arrays and cache metadata.

Let's take them one at a time.

### Slide 21: A normal Drupal route

First, routing. That hx-get URL isn't a special Ajax callback. It's just a normal Drupal route.

Here's the routing.yml: a path, /filter-results, pointing at a controller method, with access requirements, the same as any other page. No #ajax property. No form-building context.

There's exactly one HTMX-specific line: _htmx_route: TRUE, under options. That tells Drupal to send back just the main content and the assets it needs. Without it, Drupal does what it always does for a page: wraps your content in the full theme, with the header, the regions and the blocks. And you'd end up swapping a whole page into your results div.

*[Analogy]*

Think of ordering one dish at a buffet restaurant. Without the flag, Drupal hands you the entire buffet, the tablecloth and all, when you only asked for a single plate. _htmx_route says: just the plate, please.

If you've written a Drupal route, you've already written an HTMX endpoint. And because it's a real route, you get everything routes give you, including access checks, and a URL you can open directly in your browser to see exactly what it returns.

### Slide 22: The same render array Drupal always returns

Second, render arrays. The controller returns a render array, the same thing every Drupal controller has always returned.

Here it reads the filter value from the query string, builds an item list of the filtered results, and adds cache metadata. Drupal renders that to HTML, and HTMX drops the HTML wherever hx-target points.

No AjaxResponse. No Commands. The render pipeline was already built to produce exactly this. It's the same select-and-results example from the HTMX section, now wired to a real Drupal controller.

### Slide 23: Core does the glue

And what about the markup side? Since 11.3, you don't have to hand-write hx attributes at all.

Core ships an Htmx class that builds every htmx attribute from PHP. Here it is: a new Htmx object, get with our route's URL, trigger on change, target the results div, swap the inner HTML, and apply it to the filter element. The same four attributes we've been using, just built in PHP instead of typed into a template.

And applyTo() does one more thing. It attaches core/drupal.htmx, which is the glue. When a response comes back, it loads any CSS and JavaScript that response needs, and it re-attaches Drupal.behaviors to the new markup. So your existing Drupal JavaScript keeps working after every swap.

*[Analogy]*

It's like the stage crew in a theatre. When a new piece of scenery gets wheeled on, the crew plugs in its lights and cues the actors, so the scene just works. core/drupal.htmx is that stage crew, for every swap.

That's the part most people would expect to write themselves. Core already did it.

### Slide 24: Where this pays off: caching

And third, caching. This is where it really pays off.

Because the endpoint returns a plain render array, and not a Command stack inside an AjaxResponse, it gets Drupal's caching for free.

Cache tags bubble up automatically, so when content changes, the cached fragment gets invalidated. Cache contexts vary the response correctly. That url.query_args:filter context means Dynamic Page Cache stores a separate copy for each filter value.

*[Analogy]*

Picture a bakery. Cache contexts are like keeping a separate tray for each flavor, so a chocolate order never gets a vanilla cake. Cache tags are the label on each tray saying which ingredients went in, so when an ingredient changes, you know exactly which trays to throw out. Drupal does that bookkeeping for your fragment automatically.

So, to recap: HTMX asks for a URL, and Drupal has routes. It needs HTML, and Drupal has render arrays. That HTML has to be correct under caching, and Drupal has cache tags and contexts. No translation layer in between. Enough slides. Let's see it work.

---

## §5 · Live Demo (8 min)

### Slide 25: Live Demo

*[Budget: 8 minutes, hard stop. Draft. Adjust the beats once the demo is built. Keep a recorded backup of every beat ready to play.]*

*[Switch to the browser.]*

**Intro (~30 sec)**

Remember the filter from the very start? Let's build that, plus two more things, in one flow: a filter, a dynamic form and a partial page update.

**Beat 1: Filter (~3 min)**

*[Show the listing page, then the template or render array with the hx attributes.]*

Here's the select. The same four attributes from the slides, built with the Htmx class: get pointing at our route, trigger on change, target on the results container, and swap.

*[Change the filter. Open DevTools, Network tab.]*

Look at the Network tab. That response is just HTML. What you see is what you got.

*[Show the controller.]*

And here's the other end: a normal route with _htmx_route, a controller, a render array with cache tags and contexts. Nothing special.

**Beat 2: Dynamic form (~2.5 min)**

*[Show a form field whose options or visibility depend on another field.]*

Now a form, where one field changes based on another. This is the classic #ajax use case.

*[Change the first field and show the second update.]*

The same pattern again: the element says what to fetch and where to put it. Since 11.3, Form API rebuilds the form from the HTMX request with all its values, so the server returns the updated form element.

**Beat 3: Partial update (~1.5 min)**

*[Trigger a region of the page to refresh on its own, like a load-more or a counter.]*

And finally, a partial update: just this region refreshes, and the rest of the page stays exactly where it was.

**Wrap (~30 sec)**

*[Back to the slides.]*

So that's the whole thing. One route, one controller returning a render array, and a handful of htmx attributes. No custom JavaScript file.

*[If anything breaks, say:]* Live demos, right? Let me show you the recorded version. *[Play the backup and keep narrating the same lines.]*

---

## §6 · Ajax API vs HTMX, Compared Honestly (3 min)

### Slide 26: Ajax API vs HTMX, Compared Honestly (section divider)

Now, I promised an honest comparison. HTMX doesn't win everywhere. So let's put the two side by side: developer experience, performance, debugging, caching and learning curve.

### Slide 27: The verdict, side by side

Here they are, side by side. Top to bottom:

DX. The Ajax API has more ceremony: two files, two languages, a wrapper ID that has to match. But for complex multi-step forms and wizards, it's the most proven path. HTMX has far less ceremony, and since 11.3 Form API can rebuild a form from an HTMX request, so dynamic forms work. But the multi-step patterns are still young.

Performance. An Ajax response is a JSON stack of commands the browser runs one by one. HTMX is a library of about 16 kilobytes, and the response is the HTML itself. drupal.org reports up to 71% less JavaScript loaded when core interactions moved to native HTMX.

Debugging. With the Ajax API, the Network tab shows you the instruction sheet: a stack of commands, and a wrapper ID typo fails silently. With HTMX, it shows you the finished chair: the exact HTML that got inserted.

Caching. We saw this a few minutes ago: an AjaxResponse sits outside the render cache, so you're mostly on your own. An HTMX endpoint is a plain render array, so cache tags and contexts bubble up automatically.

And learning curve. The Ajax API is a Drupal-only API. HTMX is standard htmx, documented at htmx.org, so what you learn works on any backend, not just Drupal.

So it isn't a clean sweep. The Ajax API still has the edge on complex multi-step forms.

### Slide 28: Pick the tool for the job, not the trend.

So my advice: pick the tool for the job, not the trend.

Use HTMX where the round trip is simple and the UI is a fragment. Keep the Ajax API where it already works, and for complex multi-step flows, at least until the HTMX patterns there mature.

This isn't a replace-everything story. It's a which-tool-for-which-job story. Which leads to the practical question: how do you bring HTMX into a project you already have, without a rewrite?

---

## §7 · Incremental Adoption Strategy (2 min)

### Slide 29: Incremental Adoption Strategy (section divider)

Let's talk about incremental adoption: how to bring HTMX into a project you already have, without a rewrite.

### Slide 30: Adopt it in three steps

Let's make adoption concrete. You don't need a migration plan. You need one small spot and an afternoon.

Three steps.

One, check. On Drupal 11.3 and up, HTMX is already in core. Nothing to install, no library, no build step.

Two, pick one spot. Something small and self-contained: a filter, a dependent dropdown, a load-more button.

Three, convert it. Swap that one #ajax callback for HTMX, and leave everything else alone.

And HTMX and #ajax run side by side on the same page, so you can stop after any step and nothing breaks.

*[Analogy]*

It's like renovating a house while you're still living in it. You don't knock the whole thing down. You redo one room at a time, and the rest of the house keeps working.

### Slide 31: Convert one #ajax callback

Here's step three, using the filter we've followed all talk.

*[Point left.]* On the left, today's Ajax API version: an #ajax array with a callback and a wrapper ID, plus a callback method that builds an AjaxResponse with a ReplaceCommand.

*[Point right.]* On the right, the HTMX version. The Htmx class says: get this route, on change, and put the result in #results. Behind it, a normal route with _htmx_route, and a controller that returns a render array.

That's the whole migration for one interaction. No command class, no wrapper ID to keep in sync, and because it's a render array, the response is cacheable. Everything on the right, you already saw in the mapping section.

And once that works, the rule of thumb: new interactive features, HTMX by default. Existing Ajax code that works, leave it alone. Complex multi-step forms and wizards, keep them on the Ajax API for now. It's a second tool in the same box, not a rewrite.

---

## §8 · Current Status + How to Contribute (2 min)

### Slide 32: Current Status + How to Contribute (section divider)

So, where are we today?

### Slide 33: Where things stand today

This isn't a someday story. HTMX came into Drupal core as a dependency in 11.2, and became fully featured in 11.3. It's part of an official community initiative, called "Replace AJAX API with HTMX", and that initiative is still going.

Right now both APIs run side by side, on purpose, while contrib gets time to convert. Full deprecation of the Ajax API waits until contrib has had real time to switch.

*[Analogy - skip first if you're running long]*

It's like a city opening a new metro line. They don't shut down the buses on day one. Both run side by side until people have had time to switch.

And on top of core there's the contrib module, drupal/htmx, which adds extra tooling. It's already used on more than eleven hundred sites.

And the payoff is measurable. According to drupal.org, moving core's own interactions to native HTMX loaded up to 71% less JavaScript.

*[Re-check the 11.3 status and usage number on drupal.org the week of the talk.]*

### Slide 34: How to contribute

So how can you help?

Start at the initiative page on drupal.org, Replace AJAX API with HTMX. The link is right here on the slide. The plan issue and its child issues are where the core work happens, and the contrib module has its own issue queue at drupal.org/project/htmx.

There's also the #htmx channel on Drupal Slack, where the people leading this talk almost every day, with a coordination meeting every other Thursday, open for 24 hours so any time zone can join.

Where to start: triage an issue, convert one #ajax callback and report back, write docs, or build a case study, like this talk.

And this isn't waiting on anyone's permission. If you're on 11.3, it's already on your site. So go home, pick one #ajax callback, convert it, and see how it feels.

---

## §9 · Wrap-up / Q&A (1.5 min, then Q&A)

### Slide 35: Four things to take home

So, four things to take home.

The problem: the Ajax API makes you keep a callback, a command stack and a wrapper ID in sync, and when they drift, it fails silently.

The idea: HTMX puts the behavior right in the markup, and the server just returns HTML.

The fit: HTMX only needs routes, render arrays and cache metadata. Drupal has had all three for over a decade, and since 11.3, core ships an Htmx class to glue them together.

And the path: build new interactive features with HTMX, leave working Ajax code alone, and go convert one callback to see how it feels.

### Slide 36: Thank you! Questions?

If you remember one line from today: the future of Drupal UX isn't more JavaScript. It's smarter HTML.

Thank you all for coming. We'd love to hear your questions.

*[Repeat each question into the mic before answering. Backup answers for likely questions are in `docs/8-qa-backup.md`.]*

*[If someone asks how this relates to Experience Builder or Single Directory Components:]* Different jobs. Experience Builder is about building pages and layouts, SDC is about authoring components, and HTMX is about interactions.
