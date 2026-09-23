# Speaker Notes

Full spoken script for **From Ajax API to HTMX: Evolving Drupal Interactions**, slide by slide. Slide numbers match `Ajax to HTMX.pptx`.

**How to read this:** plain text is what you say, word for word if you need to. *[Italic brackets]* are stage cues, not spoken. Each section heading has its time budget. At ~130 words a minute the spoken script runs about 22 minutes on its own, so pauses, clicks, pointing at code and the live demo fill the rest. §4 is still the lightest on words for its slot, so walk through its code slowly.

---

## §1 · Hook + Intro (2 min)

### Slide 1: From Ajax API to HTMX: Evolving Drupal Interactions

*[Have this up while people settle in. Don't speak yet. When it's time to start, go straight to slide 2.]*

### Slide 2: Live teaser

*[Play the 15-20 second clip in silence. Let the filter update a few times, then pause on the result.]*

*[If the clip won't play, say:]* What you should be seeing here is a filter on a Drupal listing page, updating as you type, with no page reload. We'll build exactly that later on.

### Slide 3: What just happened

That's a live filter.
No JavaScript file.
No Ajax command class.
Just HTML.

*[Pause a beat.]*

### Slide 4: Ruturaj Chaubey

Hi everyone, I'm Ruturaj, a full stack engineer at QED42. And with me is my colleague Vighnesh Sadagopal, also from QED42.

I've spent a good chunk of the last few years building interactive Drupal sites the traditional way, callbacks and all. Which is exactly why I got so excited when HTMX showed up.

### Slide 5: The premise

*[Let the slide land. Read it slowly.]*

Here's the idea behind this whole talk. The future of Drupal UX isn't more JavaScript. It's smarter HTML. With HTMX.

*[Pause.]*

### Slide 6: Where we're headed

Here's where we're going over the next 45 minutes.

First, why the Ajax API gets painful as your project grows. Then the core idea behind HTMX, and how that idea maps onto things Drupal already has: routing, render arrays and caching.

Then we'll watch it work live: a form, a filter and a partial page update, with barely any JavaScript.

After that, an honest comparison of the two, because HTMX doesn't win everywhere. Then how to bring it into a project you already have, without a rewrite. And finally, where HTMX stands in Drupal core today, and how you can help.

---

## §2 · Ajax API: Evolution & Pain Points (6 min)

### Slide 7: Ajax API: Evolution & Pain Points (section divider)

So let's start with where we are today: the Ajax API.

### Slide 8: A brief history

Drupal's Ajax API isn't new. It's been in core in its current form since Drupal 7, unified under one framework. In Drupal 8 through 11 it was rebuilt on top of Symfony's HttpKernel. AjaxResponse extends Symfony's JsonResponse class, and every interaction is expressed as a stack of Command objects.

It's mature. It's well documented. And it's everywhere in core: exposed filters, autocomplete, modal dialogs, inline entity forms.

So I want to be clear. This isn't a story about a broken system. It's a story about a system that was the right answer for 2011, and is starting to show its age.

### Slide 9: The mechanics

A quick refresher on how it works.

Step one: you give a form or render element an #ajax property, with a callback, a wrapper and an event.

Step two: when that event fires, your callback runs on the server and returns an AjaxResponse, built from one or more Commands.

Step three: back in the browser, the core/drupal.ajax library runs each of those commands to change the DOM, and then Drupal.behaviors re-attach to the new markup.

### Slide 10: Ajax API request/response cycle

Here's that as a sequence.

The user triggers the event, say a change on a select. The browser sends a request to your callback. On the server, Drupal builds an AjaxResponse and adds commands to it, like a ReplaceCommand. That comes back as JSON: a list of commands, not HTML. The drupal.ajax library runs those commands one by one, and finally the DOM gets updated and behaviors re-attach.

Count the steps: seven. Keep that number in mind. We'll come back to it.

### Slide 11: The #ajax property

And here's what that looks like in code.

At the top, the form element with its #ajax array: a callback, a wrapper called results-wrapper, and the change event. Below it, the callback itself: create an AjaxResponse, add a ReplaceCommand that targets #results-wrapper, and return it.

Now look at that wrapper ID. That string lives in your PHP, and a matching ID has to exist in the markup, usually in a Twig template somewhere else. Two files, two languages, and one string that has to match exactly. If it doesn't, nothing happens. Silently.

### Slide 12: One string, two files, no compiler

This diagram is the failure mode.

On the left, MyForm.php declares the wrapper and builds the ReplaceCommand. On the right, a Twig template, three directories away, renders the element with that ID. Both sides have to match exactly.

There's no compiler checking that. No linter. Get it wrong and nothing throws an error. It just quietly doesn't work.

### Slide 13: Four pain points

So I'd sum up the pain in four points.

One, JS coupling. The built-in commands are covered, but every custom interaction needs a matching pair: a PHP command on the server and a JS command handler on the client. Often in different files, sometimes in different modules.

Two, complexity. The command pattern is imperative. You're telling the DOM what to do, step by step, instead of describing what the result should look like.

Three, maintenance overhead. One typo, no error, silent failure.

And four, discoverability. Look at the rendered HTML and nothing tells you that element is interactive. The behavior is buried in a PHP array, and the target lives in another file you have to go and find.

### Slide 14: A true story

And I'll admit, that third one is personal.

I once spent 40 minutes in devtools debugging an Ajax response that updated one region but silently failed on another. The bug wasn't in the PHP. It wasn't in the JavaScript logic. It was one mismatched wrapper ID, defined in a template three directories away from the PHP that built the response.

Nothing threw an error. It just didn't work.

*[Pause.]*

### Slide 15: …the element itself could just describe what it needs?

So what if, instead of a callback, a command class and a wrapper ID to keep in sync for every interaction, the element itself could just describe what it needs?

When this changes, fetch this URL, and put the result here.

No command classes. No wrapper IDs to keep in sync. No second file to go find. What would that even look like?

*[Pause, then click.]*

---

## §3 · HTMX & Hypermedia Principles (6 min)

### Slide 16: The reveal: it's called HTMX

That's not a hypothetical. It's called HTMX. And it's built on an idea that's actually older than Ajax: hypermedia.

### Slide 17: The idea: hypermedia

And you already know hypermedia, even if you've never used the word.

Every a href you've ever written, and every form, is a hypermedia control. It's an element that tells the browser what to fetch, and where to put the result. A link fetches a page and replaces the whole window. A form sends data and does the same.

HTMX just extends that vocabulary. Any element can make a request. On any event. With any HTTP verb. And put the result into any part of the page.

### Slide 18: One element. Four attributes.

Here it is in code. A select element with four attributes.

hx-get: the URL to fetch. hx-trigger: when to fetch it, here on change. hx-target: where the result goes, the results div. And hx-swap: how to put it in, here by replacing the inner HTML. The server sends back just that fragment of HTML.

That's it. One element, four attributes. The behavior lives right there in the tag. No PHP file to go find, no wrapper ID to keep in sync across two files.

Remember the discoverability problem? This is the answer. The markup describes itself.

### Slide 19: HTMX request/response cycle

Here's the same interaction as a sequence.

The user triggers the event. The browser makes a normal HTTP request to the hx-get URL. The server responds with an HTML fragment. Not JSON, not commands. And HTMX swaps it into the target.

Compare that to the Ajax version from a few minutes ago. Same three participants: user, browser, server. But four steps instead of seven.

### Slide 20: The response is the next state of the UI

And this is the bigger idea.

There's no separate JSON API here. No client-side templating layer rebuilding HTML from data. The server decides what HTML comes back, the same way it always has for the first page load.

So HTMX isn't a new architecture. It closes the gap between the first page load and everything after it, so both work the same way. That's what hypermedia-driven means: the response is the next state of the UI, already rendered.

### Slide 21: Four attributes

If you remember one slide from this section, make it this one. Four core attributes.

Verb: hx-get, hx-post, hx-put, hx-delete. Which HTTP method to use.

Trigger: hx-trigger. Any DOM event, not just click or submit.

Target: hx-target. Any element on the page, not just the one that fired the event.

And swap: hx-swap. How the new HTML goes in: innerHTML, outerHTML, beforeend, afterbegin, and a few more.

With those four, you can build most of what people use the Ajax API for.

### Slide 22: HTMX gives Drupal back a vocabulary it never lost: Hypermedia.

So HTMX gives Drupal back a vocabulary it never really lost: hypermedia.

The real question is how this fits Drupal's own architecture. Render arrays, routing, caching. That's next.

---

## §4 · Mapping HTMX onto Drupal's Architecture (8 min)

### Slide 23: Mapping HTMX onto Drupal's Architecture (section divider)

Let's map HTMX onto three things Drupal already has.

### Slide 24: The question

Here's the thing. HTMX doesn't need anything special from Drupal. It needs three things Drupal already has, and has had for over a decade: routes, render arrays and cache metadata.

Let's take them one at a time.

### Slide 25: A normal Drupal route

First, routing. That hx-get URL isn't a special Ajax callback. It's just a normal Drupal route.

Here's the routing.yml: a path, /filter-results, pointing at a controller method, with access requirements, the same as any other page. No #ajax property. No form-building context.

There's exactly one HTMX-specific line: _htmx_route: TRUE, under options. That tells Drupal to send back just the main content and the assets it needs. Without it, Drupal does what it always does for a page: wraps your content in the full theme, with the header, the regions and the blocks. And you'd end up swapping a whole page into your results div.

If you've written a Drupal route, you've already written an HTMX endpoint. And because it's a real route, you get everything routes give you, including access checks, and a URL you can open directly in your browser to see exactly what it returns.

### Slide 26: The same render array Drupal always returns

Second, render arrays. The controller returns a render array, the same thing every Drupal controller has always returned.

Here it reads the filter value from the query string, builds an item list of the filtered results, and adds cache metadata. Drupal renders that to HTML, and HTMX drops the HTML wherever hx-target points.

No AjaxResponse. No Commands. The render pipeline was already built to produce exactly this. It's the same select-and-results example from the HTMX section, now wired to a real Drupal controller.

### Slide 27: Core does the glue

And what about the markup side? Since 11.3, you don't have to hand-write hx attributes at all.

Core ships an Htmx class that builds every htmx attribute from PHP. Here it is: a new Htmx object, get with our route's URL, trigger on change, target the results div, swap the inner HTML, and apply it to the filter element. The same four attributes from the cheat sheet, just built in PHP instead of typed into a template.

And applyTo() does one more thing. It attaches core/drupal.htmx, which is the glue. When a response comes back, it loads any CSS and JavaScript that response needs, and it re-attaches Drupal.behaviors to the new markup. So your existing Drupal JavaScript keeps working after every swap.

That's the part most people would expect to write themselves. Core already did it.

### Slide 28: Where this pays off: caching

And third, caching. This is where it really pays off.

Because the endpoint returns a plain render array, and not a Command stack inside an AjaxResponse, it gets Drupal's caching for free.

Cache tags bubble up automatically, so when content changes, the cached fragment gets invalidated. Cache contexts vary the response correctly. That url.query_args:filter context means Dynamic Page Cache stores a separate copy for each filter value.

With an AjaxResponse, you're mostly on your own for caching. With HTMX, the endpoint is just a cacheable page fragment, using the caching system you already have.

### Slide 29: Three things Drupal already gives

So, to recap.

HTMX asks for a URL. Drupal already has routes and controllers, with one flag.

HTMX needs HTML to swap in. Drupal already has render arrays.

And that HTML has to be correct under caching. Drupal already has cache tags and contexts.

Three things Drupal already gives every page. HTMX just asks for them directly, without a translation layer in between. Enough slides. Let's see it work.

---

## §5 · Live Demo (10 min)

### Slide 30: Live Demo

*[Draft. Adjust the beats once the demo is built. Keep a recorded backup of every beat ready to play.]*

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

**Beat 2: Dynamic form (~3 min)**

*[Show a form field whose options or visibility depend on another field.]*

Now a form, where one field changes based on another. This is the classic #ajax use case.

*[Change the first field and show the second update.]*

The same pattern again: the element says what to fetch and where to put it. Since 11.3, Form API rebuilds the form from the HTMX request with all its values, so the server returns the updated form element.

**Beat 3: Partial update (~2 min)**

*[Trigger a region of the page to refresh on its own, like a load-more or a counter.]*

And finally, a partial update: just this region refreshes, and the rest of the page stays exactly where it was.

**Wrap (~1 min)**

*[Back to the slides.]*

So that's the whole thing. One route, one controller returning a render array, and a handful of htmx attributes. No custom JavaScript file.

*[If anything breaks, say:]* Live demos, right? Let me show you the recorded version. *[Play the backup and keep narrating the same lines.]*

---

## §6 · Ajax API vs HTMX, Compared Honestly (5 min)

### Slide 31: Ajax API vs HTMX, Compared Honestly (section divider)

Now, I promised an honest comparison. HTMX doesn't win everywhere. So let's put the two side by side across four things: developer experience, performance, debugging and progressive enhancement.

### Slide 32: DX & performance

Developer experience first.

The Ajax API has more ceremony: two files, two languages, a wrapper ID that has to match. But for complex multi-step forms and wizards, it's the most proven path. Years of core and contrib patterns are built on it.

HTMX has far less ceremony: one element, a few attributes, no second file. And since 11.3, Form API can rebuild a form from an HTMX request with all its values, so dynamic forms work. But for complex multi-step flows, the patterns are still young.

Performance. An Ajax API response is a JSON stack of commands, and the browser has to run them one by one.

HTMX is a library of about 16 kilobytes, and the response is the HTML itself. Nothing to interpret. And this isn't just theory: drupal.org reports that switching core interactions to native HTMX loaded up to 71% less JavaScript.

### Slide 33: Debugging & progressive enhancement

Debugging. With the Ajax API, the Network tab shows you a stack of commands, not the markup that came out of it. You have to run those commands in your head to know what changed, and a wrapper ID typo fails without any error.

With HTMX, the Network tab shows the exact HTML that got inserted. What you see is what you got.

Progressive enhancement. Honestly, this one is close to a draw. #ajax sits on top of a real form element, the same way hx attributes sit on a real link or form. Both can fall back. But let's be honest: on most real projects, neither one ever gets tested with JavaScript turned off.

### Slide 34: The verdict, side by side

So here's the verdict, side by side.

DX: the Ajax API has more ceremony, but it's the most proven path for complex multi-step forms. HTMX has less ceremony, and its multi-step patterns are still maturing.

Performance: a JSON command stack run in the browser, versus a small library, a plain HTML response, and up to 71% less JavaScript.

Debugging: an opaque command stack with silent failures, versus the actual HTML right there in the Network tab.

And progressive enhancement is a draw, on purpose. This isn't a clean sweep for either one.

### Slide 35: Pick the tool for the job, not the trend.

So my advice: pick the tool for the job, not the trend.

Use HTMX where the round trip is simple and the UI is a fragment. Keep the Ajax API where it already works, and for complex multi-step flows, at least until the HTMX patterns there mature.

This isn't a replace-everything story. It's a which-tool-for-which-job story. Which leads to the practical question: how do you bring HTMX into a project you already have, without a rewrite?

---

## §7 · Incremental Adoption Strategy (4 min)

### Slide 36: Incremental Adoption Strategy (section divider)

Let's talk about incremental adoption.

### Slide 37: No rewrite required

The good news first. You don't have to rip out the Ajax API to start using HTMX. They live happily on the same page. HTMX doesn't touch anything #ajax owns.

So here's how to actually bring it into a project you already have.

### Slide 38: One library, one #attached

The integration cost? On Drupal 11.3 and up, there's nothing to install.

Core already ships htmx, as core/htmx, plus core/drupal.htmx for the Drupal glue: loading assets and re-attaching behaviors. If you use the Htmx class we saw earlier, it attaches the library for you. If you're hand-writing hx attributes in Twig, it's one #attached line.

No library definition. No CDN. No build step.

### Slide 39: Pick candidates, don't do a sweep

Next, pick your candidates. Don't do a sweep.

The Ajax API code that already works? Leave it. HTMX is for new interactive spots, or the small annoying ones: a filter, a load-more pager, an autocomplete list. One element at a time, not a module-wide rewrite.

And if you want a low-effort way to try it, there's hx-boost. Put hx-boost="true" on an element, say your nav, and the links and forms inside it become HTMX requests automatically. Nothing else in your markup has to change.

One word of caution: each boosted request still swaps the whole body of the page. So start with one region, and test the page-level JavaScript, things like the toolbar and BigPipe. It's a great way to get a feel for HTMX on a real site before you write a single hx-get.

### Slide 40: Which tool, when

If you want a simple rule of thumb, here it is.

Building a new interactive feature? Reach for HTMX by default.

Existing Ajax API code that works? Leave it alone.

And complex multi-step forms, like wizards or heavy Form API flows? Keep them on the Ajax API for now, just like we said in the comparison.

### Slide 41: A second tool in the same box, not a rewrite.

So the takeaway: it's a second tool in the same box, not a rewrite.

Use what core ships, pick a handful of low-risk candidates, and boost one region to get a feel for it. No migration weekend needed.

Next: where this actually stands in Drupal core today, and how you can help move it forward.

---

## §8 · Current Status + How to Contribute (3 min)

### Slide 42: Current Status + How to Contribute (section divider)

So, where are we today?

### Slide 43: Where things stand today

This isn't a someday story. HTMX came into Drupal core as a dependency in 11.2, and became fully featured in 11.3. It's part of an active initiative to gradually replace Drupal's Ajax system with HTMX, and that initiative is still going. Right now both APIs run side by side, on purpose, while contrib gets time to convert.

And on top of core there's the contrib module, drupal/htmx, which adds extra tooling. It's already used on more than eleven hundred sites.

And the payoff is measurable. According to drupal.org, moving core's own interactions to native HTMX loaded up to 71% less JavaScript.

*[Re-check the 11.3 status and usage number on drupal.org the week of the talk.]*

### Slide 44: Both APIs, on purpose

Why keep both APIs? Because it's the same incremental idea from a minute ago, just at the level of core instead of your project. Full deprecation of the Ajax API waits until contrib has had real time to convert.

And quickly, so there's no confusion: this isn't the same thing as Experience Builder or Single Directory Components. Experience Builder is about building pages and layouts. SDC is about authoring components. HTMX is about interactions. Different jobs.

### Slide 45: How to contribute

So how can you help?

There's the core initiative, with a plan issue and a proof-of-concept issue in the core queue. There's the contrib module's issue queue, at drupal.org/project/htmx. And there's the #htmx channel on Drupal Slack, where a lot of the conversation happens.

If you're wondering where to start: triage an issue. Convert one #ajax callback to HTMX and report back how it went. Write docs or examples. Or build a real-world case study, like this talk.

### Slide 46: It's not waiting on permission.

This isn't waiting on anyone's permission. It's already in core, people are actively working on it, and it needs more hands.

If you're on 11.3, it's already on your site. So go home, pick one #ajax callback in your own project, convert it, and see how it feels.

---

## §9 · Wrap-up / Q&A (1 min + spillover)

### Slide 47: Thank you! Questions?

One last thing before questions. If you remember one line from today: the future of Drupal UX isn't more JavaScript. It's smarter HTML.

Thank you all for coming. We'd love to hear your questions.

*[Repeat each question into the mic before answering. Backup answers for likely questions are in `docs/8-qa-backup.md`.]*
