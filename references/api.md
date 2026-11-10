# Scroll-Driven Animation API Reference

This file documents the CSS and JavaScript surface used by this Skill. Syntax reflects modern evergreen browsers; verify against `references/fallbacks.md` before relying on a specific feature.

## Core concept

A CSS animation normally runs on a clock. A scroll-driven animation runs on a **timeline** that maps scroll progress (0–1) to animation progress (0–1). The same `@keyframes` work for both; only the timeline changes.

## CSS: `animation-timeline`

Attach a scroll timeline to an animation:

```css
.element {
  animation: my-animation 1s linear; /* duration ignored for scroll timelines */
  animation-timeline: scroll();
}
```

When `animation-timeline` is a scroll timeline, the `duration` is ignored; the range comes from the scroll/view range.

### `scroll()` — link to a scroll container

```css
animation-timeline: scroll();           /* nearest scrollable ancestor, block axis */
animation-timeline: scroll(root);       /* the document (root) scroller */
animation-timeline: scroll(self);       /* the element's own scrollport */
animation-timeline: scroll(nearest inline); /* horizontal axis of nearest scroller */
```

Axis values: `block`, `inline`, `x`, `y`. The default axis is `block`.

### `view()` — link to the element's visibility in the viewport

```css
animation-timeline: view();             /* element's own journey through the viewport */
animation-timeline: view(block 10px);   /* with an inset from the block-start edge */
```

### `animation-range` — narrow the active window

```css
animation-range: entry;                 /* entering the viewport */
animation-range: exit;                  /* leaving the viewport */
animation-range: cover;                 /* full journey through the viewport */
animation-range: contain;               /* fully inside the viewport */
animation-range: entry 0% entry 40%;    /* custom start..end */
animation-range: 20% 80%;               /* percentage of the view() range */
```

### Named timelines

```css
.scroller { scroll-timeline-name: --my-timeline; }
.consumer {
  animation-timeline: --my-timeline;
  timeline-scope: --my-timeline; /* when the scroller is an ancestor */
}
```

## JavaScript: Web Animations API timelines

```js
// Scroll-linked timeline
const tl = new ScrollTimeline({ source: document.scrollingElement, axis: 'block' });

// View-triggered timeline
const vt = new ViewTimeline({ subject: document.querySelector('.card'), axis: 'block' });

element.animate(
  [{ transform: 'translateY(20px)' }, { transform: 'translateY(0)' }],
  { timeline: tl, fill: 'both' }
);
```

## Notes

- The older `@scroll-timeline` at-rule is deprecated; use `animation-timeline` / `scroll()` / `view()`.
- `animation-timeline` does not inherit; set it on the same element as `animation`.
- Use `animation-fill-mode: both` so the element holds its final state at the end of the range.
