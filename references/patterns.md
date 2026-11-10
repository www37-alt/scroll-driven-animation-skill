# Scroll-Driven Animation Patterns

Copy-ready patterns. Each assumes fallback-first markup (content visible without animation). Gate every rule behind `@supports` where noted.

## 1. Scroll progress bar

A fixed bar that grows with the page scroll.

```css
#progress {
  position: fixed;
  inset: 0 auto auto 0;
  width: 100%;
  height: 4px;
  background: #00e5ff;
  transform: scaleX(0);
  transform-origin: 0 50%;
  animation: grow-progress linear;
  animation-timeline: scroll(root);
}
@keyframes grow-progress { to { transform: scaleX(1); } }
```

## 2. Number count-up (scroll-linked)

Scroll-driven CSS cannot change text content alone; drive a custom property and render it with a counter.

```css
@property --value {
  syntax: '<number>';
  initial-value: 0;
  inherits: false;
}
.stat {
  animation: count-up linear both;
  animation-timeline: view();
  animation-range: entry 20% entry 80%;
  counter-reset: num var(--value);
}
.stat::after { content: counter(num, decimal); }
@keyframes count-up { to { --value: 128.4; } }
```

Alternative (broader support): drive the number with `ScrollTimeline` in JS.

```js
const tl = new ScrollTimeline({ source: document.scrollingElement });
const stat = document.querySelector('.stat');
stat.animate({ '--value': [0, 128.4] }, { timeline: tl, fill: 'both' });
```

## 3. Parallax

A background layer moves at a different rate than the text.

```css
.parallax-bg {
  animation: parallax linear both;
  animation-timeline: view();
  animation-range: cover;
}
@keyframes parallax {
  from { transform: translateY(8%); }
  to   { transform: translateY(-8%); }
}
```

## 4. Card reveal (view-triggered)

Cards fade and slide up as they enter the viewport. Start hidden only inside `@supports`.

```css
.card { /* visible by default */ }

@supports (animation-timeline: view()) {
  .card {
    opacity: 0;
    animation: reveal linear both;
    animation-timeline: view();
    animation-range: entry 0% entry 40%;
  }
  @keyframes reveal {
    from { opacity: 0; transform: translateY(24px); }
  }
}
```

## 5. Scroll storytelling (pinned, frame-scrubbed)

A tall section with a sticky frame that scrubs through steps.

```css
.story { height: 300vh; }
.sticky { position: sticky; top: 0; height: 100vh; display: grid; place-items: center; }
.step {
  opacity: 0;
  animation: step linear both;
  animation-timeline: view();
}
.step:nth-child(1) { animation-range: entry 0% entry 33%; }
.step:nth-child(2) { animation-range: entry 33% entry 66%; }
.step:nth-child(3) { animation-range: entry 66% entry 100%; }
@keyframes step { to { opacity: 1; } }
```

## General reminders

- Prefer `transform` and `opacity`; do not animate `width`, `height`, `top`, or `left`.
- Use `animation-fill-mode: both` so elements keep their final state after the range ends.
- Wrap every animation in `@supports` and keep the default state visible (see `references/fallbacks.md`).
