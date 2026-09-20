# Section 4: Mapping HTMX onto Drupal's Architecture (8 min)

## [0:00–1:00] Frame the question

"HTMX doesn't need anything special from Drupal. It needs three things Drupal already has, and has had for over a decade: routes, render arrays, and cache metadata. Let's map them one at a time."

## [1:00–3:00] Routing — `hx-get` is just a route

"An `hx-get` URL isn't a special Ajax callback — it's a normal Drupal route. No `#ajax` property, no form-building context required."

```yaml
# my_module.routing.yml
my_module.filter_results:
  path: '/filter-results'
  defaults:
    _controller: '\Drupal\my_module\Controller\FilterController::results'
  requirements:
    _access: 'TRUE'
```

## [3:00–5:00] Render arrays — the natural fit

"The controller returns a render array — the same thing every Drupal controller has always returned. Drupal renders it to HTML. HTMX drops that HTML wherever `hx-target` points. No `AjaxResponse`, no `Commands` — the render pipeline was already built to produce exactly this."

```php
public function results(Request $request) {
  $filter = $request->query->get('filter');
  return [
    '#theme' => 'item_list',
    '#items' => $this->getFilteredItems($filter),
    '#cache' => [
      'tags' => ['node_list'],
      'contexts' => ['url.query_args:filter'],
    ],
  ];
}
```

This is the Drupal-specific version of §3's generic `hx-get`/`hx-target` example — same markup pattern, now wired to a real controller.

## [5:00–7:00] Caching — where this actually pays off

"Here's the part that doesn't exist for the Ajax API without extra work: because this is a plain render array, not a Command stack buried inside an `AjaxResponse`, it gets Drupal's caching for free. Cache tags bubble up automatically. Cache contexts vary the response correctly — `url.query_args:filter` means Dynamic Page Cache can store a separate cached copy per filter value. An `AjaxResponse` built from Commands doesn't sit in that pipeline the same way — you're mostly on your own for caching an Ajax callback response. An HTMX endpoint is just... a cacheable page fragment, using the caching system you already have."

## [7:00–8:00] Recap + transition into §5

Visual — a three-row mapping table as the anchor visual for this section:

| HTMX asks for | Drupal already has |
|---|---|
| A URL (`hx-get`/`hx-post`) | A route + controller |
| HTML to swap in | A render array |
| Correctness under caching | `#cache` tags & contexts |

"Three things Drupal already gives every page. HTMX just asks for them directly, without a translation layer in between. Let's see it work."

## Notes / open items

- Mapping table used as the section's primary visual instead of a sequence diagram — this section is about correspondence between two systems, not a process over time, so a table reads more clearly here.
- Reuses the exact `hx-get`/`hx-target` shape from §3's generic example, now made Drupal-specific, so the throughline from "here's the generic idea" to "here's how it lands in Drupal" stays visible.
- The caching beat directly resolves the caching-model pain point flagged as a candidate during the §2 discussion — this is where it was reserved for.
