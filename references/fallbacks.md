# Browser Support and Fallbacks

Scroll-driven animations are a progressive enhancement. The page must be fully usable when they are unavailable.

## Support (as of late 2025 — verify current status before relying on it)

| Browser | Support | Notes |
|---|---|---|
| Chrome / Edge | 115+ | Stable since mid-2023 |
| Safari | 18+ | Stable since late 2024 |
| Firefox | Behind flag, stabilization ongoing | Test explicitly; do not assume |

Unknown metrics here are estimates — confirm against a current compatibility table before shipping.

## Feature detection

Gate animation rules with `@supports` so unsupported browsers keep the static layout:

```css
@supports (animation-timeline: scroll()) {
  /* scroll-linked rules */
}

@supports (animation-timeline: view()) {
  /* view-triggered rules */
}
```

## Fallback-first pattern

1. Write markup and styles so content is fully visible with no animation.
2. Only inside `@supports` set the pre-animation hidden state (e.g. `opacity: 0`).
3. The animation then reveals it. Without support, nothing is ever hidden.

```css
.card { opacity: 1; } /* default: visible */

@supports (animation-timeline: view()) {
  .card { opacity: 0; animation: reveal linear both; animation-timeline: view(); }
}
```

## `prefers-reduced-motion`

Disable scroll-driven animation when the user requests reduced motion:

```css
@media (prefers-reduced-motion: reduce) {
  .card, .parallax-bg, .step {
    animation: none;
    opacity: 1;
    transform: none;
  }
}
```

## Performance

- Animate only compositable properties (`transform`, `opacity`, `filter`).
- Avoid animating layout properties (`width`, `height`, `top`, `left`, `margin`); they force reflow on every scroll frame.
- Limit the number of simultaneously scrubbed elements on low-end devices.
- Keep pinned/sticky sections short; long pinned scroll ranges can feel sluggish.
