# CSS Animation Reference

## @property — The Game Changer

`@property` registers a CSS custom property with a type, unlocking smooth animation of things CSS normally can't animate (gradients, complex backgrounds). Baseline 2024 — ~90% global support.

### Aurora / Blob Background

```css
@property --aurora-1 { syntax: "<color>"; inherits: false; initial-value: hsl(280 80% 20%); }
@property --aurora-2 { syntax: "<color>"; inherits: false; initial-value: hsl(220 70% 15%); }
@property --aurora-3 { syntax: "<color>"; inherits: false; initial-value: hsl(180 60% 12%); }
@property --aurora-pos { syntax: "<percentage>"; inherits: false; initial-value: 40%; }

.aurora-bg {
  background:
    radial-gradient(ellipse 80% 60% at var(--aurora-pos) 30%, var(--aurora-1) 0%, transparent 70%),
    radial-gradient(ellipse 60% 80% at 70% var(--aurora-pos), var(--aurora-2) 0%, transparent 70%),
    radial-gradient(ellipse 100% 50% at 30% 70%, var(--aurora-3) 0%, transparent 70%),
    #050508;
  animation: aurora-drift 8s ease-in-out infinite alternate;
}

@keyframes aurora-drift {
  0%   { --aurora-1: hsl(280 80% 20%); --aurora-2: hsl(220 70% 15%); --aurora-3: hsl(180 60% 12%); --aurora-pos: 40%; }
  50%  { --aurora-1: hsl(320 70% 18%); --aurora-2: hsl(200 80% 13%); --aurora-3: hsl(160 65% 14%); --aurora-pos: 60%; }
  100% { --aurora-1: hsl(260 90% 22%); --aurora-2: hsl(240 65% 17%); --aurora-3: hsl(190 70% 11%); --aurora-pos: 35%; }
}
```

### Gradient Shimmer Text (metallic / gold)

```css
@property --shimmer-pos { syntax: "<percentage>"; inherits: false; initial-value: -100%; }

@keyframes shimmer { to { --shimmer-pos: 200%; } }

.shimmer-text {
  background: linear-gradient(
    105deg,
    #8b6914 0%,
    #c9a96e 30%,
    #f5d78e var(--shimmer-pos),
    #c9a96e 60%,
    #8b6914 100%
  );
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: shimmer 3s linear infinite;
}

/* Static metallic — no animation */
.metallic-text {
  background: linear-gradient(to bottom, #f5d78e 0%, #c9a96e 40%, #8b6914 60%, #c9a96e 80%, #f5d78e 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}
```

### Animated Glow Pulse

```css
@property --glow-color { syntax: "<color>"; inherits: false; initial-value: rgba(201,169,110,0.4); }

@keyframes pulse-glow { 50% { --glow-color: rgba(201,169,110,0.08); } }

.glow-pulse {
  box-shadow: 0 0 40px var(--glow-color);
  animation: pulse-glow 3s ease-in-out infinite;
}
```

### Progress Bar Fill

```css
@property --fill { syntax: "<percentage>"; inherits: false; initial-value: 0%; }

.progress-bar {
  background: linear-gradient(to right, #c9a96e var(--fill), rgba(255,255,255,0.06) var(--fill));
  animation: fill-bar 1.5s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}
@keyframes fill-bar { to { --fill: 73%; } }
```

---

## CSS 3D Techniques

### Perspective Rules

```css
/* Set on PARENT container */
.scene { perspective: 800px; }  /* 600–1200px is the sweet spot for cards */

/* Or inline per-element (each has own vanishing point) */
.card { transform: perspective(800px) rotateY(var(--ry, 0deg)); }
```

Smaller = more dramatic fish-eye. `800px` = natural premium feel. `1200px+` = subtle, large hero sections.

### Production 3D Card Flip

```html
<div class="card-scene">
  <div class="card-inner">
    <div class="card-face card-face--front"><!-- front content --></div>
    <div class="card-face card-face--back"><!-- back content --></div>
  </div>
</div>
```

```css
.card-scene {
  perspective: 900px;
  width: 340px;
  height: 460px;
}
.card-inner {
  position: relative;
  width: 100%; height: 100%;
  transform-style: preserve-3d;
  transition: transform 0.7s cubic-bezier(0.16, 1, 0.3, 1);
  cursor: pointer;
}
.card-scene:hover .card-inner,
.card-scene:focus-within .card-inner { transform: rotateY(180deg); }

.card-face {
  position: absolute; inset: 0;
  backface-visibility: hidden;
  -webkit-backface-visibility: hidden;
  border-radius: 16px;
  overflow: hidden;
}
.card-face--back { transform: rotateY(180deg); }
```

**Critical:** `transform-style: preserve-3d` does NOT propagate through elements with `overflow`, `filter`, `opacity < 1`, `clip-path`, or `mask` — those create flattening contexts.

### CSS Tilt via Custom Properties (JS sets --rx, --ry)

```css
.tilt-card {
  transform: perspective(800px) rotateX(var(--rx, 0deg)) rotateY(var(--ry, 0deg));
  transition: transform 0.1s ease-out;
  position: relative;
  overflow: hidden;
}
.tilt-card::after {
  content: '';
  position: absolute; inset: 0;
  background: radial-gradient(circle at var(--gx, 50%) var(--gy, 50%), rgba(255,255,255,0.13) 0%, transparent 55%);
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.3s ease;
}
.tilt-card:hover::after { opacity: 1; }
```

```javascript
card.addEventListener('mousemove', (e) => {
  const r = card.getBoundingClientRect();
  const x = (e.clientX - r.left) / r.width - 0.5;
  const y = (e.clientY - r.top) / r.height - 0.5;
  card.style.setProperty('--ry', `${x * 12}deg`);
  card.style.setProperty('--rx', `${-y * 12}deg`);
  card.style.setProperty('--gx', `${(x + 0.5) * 100}%`);
  card.style.setProperty('--gy', `${(y + 0.5) * 100}%`);
});
card.addEventListener('mouseleave', () => {
  card.style.setProperty('--rx', '0deg');
  card.style.setProperty('--ry', '0deg');
});
```

---

## Glassmorphism — Dark Luxury Pattern

```css
.glass-card {
  background: linear-gradient(135deg, rgba(255,255,255,0.06) 0%, rgba(255,255,255,0.02) 100%);
  backdrop-filter: blur(16px) brightness(1.1);
  -webkit-backdrop-filter: blur(16px) brightness(1.1); /* always include Safari prefix */
  border: 1px solid rgba(255,255,255,0.1);
  border-bottom: 1px solid rgba(255,255,255,0.05);
  border-right:  1px solid rgba(255,255,255,0.05);
  box-shadow:
    0 8px 32px rgba(0,0,0,0.4),
    inset 0 1px 0 rgba(255,255,255,0.08); /* top edge highlight */
  border-radius: 16px;
}

@supports not (backdrop-filter: blur(1px)) {
  .glass-card { background: rgba(20,20,20,0.88); } /* fallback */
}
```

**Gotcha:** Any parent with `opacity < 1`, `filter`, `transform`, `clip-path`, or `mask` creates a new backdrop root and breaks the blur. This is the #1 reason glassmorphism looks wrong.

---

## Glow Effects

```css
/* 1. box-shadow — clips to border-box, GPU accelerated */
.glow-box { box-shadow: 0 0 20px rgba(201,169,110,0.3), 0 0 60px rgba(201,169,110,0.12); }

/* 2. filter: drop-shadow — follows alpha channel / irregular shapes */
.glow-filter { filter: drop-shadow(0 0 20px rgba(201,169,110,0.4)); }

/* 3. Pseudo-element radial gradient — most control */
.glow-radial { position: relative; }
.glow-radial::before {
  content: ''; position: absolute; inset: -40px;
  background: radial-gradient(ellipse at 50% 50%, rgba(201,169,110,0.2) 0%, transparent 70%);
  pointer-events: none; z-index: -1;
}
```

---

## Noise Texture Overlay

Adds organic texture that prevents the "too digital" look.

```css
.textured::before {
  content: '';
  position: absolute; inset: 0;
  border-radius: inherit;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  background-size: 200px 200px;
  opacity: 0.03;
  mix-blend-mode: overlay;
  pointer-events: none;
}
```

---

## CSS Scroll-Driven Animations (Native, No JS)

Runs off the main thread — as smooth as transforms. Use `@supports` guard since Firefox support is behind a flag.

```css
@supports (animation-timeline: scroll()) {

  /* Reading progress bar */
  .reading-progress {
    position: fixed; top: 0; left: 0; height: 2px; width: 100%;
    background: #c9a96e; transform-origin: left;
    animation: grow-bar linear;
    animation-timeline: scroll(root block);
  }
  @keyframes grow-bar { from { transform: scaleX(0); } to { transform: scaleX(1); } }

  /* Scroll reveal */
  .reveal {
    animation: reveal-up linear both;
    animation-timeline: view();
    animation-range: entry 0% cover 35%;
  }
  @keyframes reveal-up {
    from { opacity: 0; transform: translateY(40px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  /* Stagger on grid items */
  .grid .item:nth-child(1) { animation-delay: 0ms; }
  .grid .item:nth-child(2) { animation-delay: 80ms; }
  .grid .item:nth-child(3) { animation-delay: 160ms; }

}
```

**vs GSAP ScrollTrigger:** Use native for simple reveals/progress bars. Use ScrollTrigger for pinning, complex scrub sequences, or anything needing JS callbacks.

---

## Clip-Path Reveals

```css
/* Iris expand */
@keyframes iris { from { clip-path: circle(0% at 50% 50%); } to { clip-path: circle(75% at 50% 50%); } }
.iris-reveal { animation: iris 0.8s cubic-bezier(0.16,1,0.3,1) forwards; }

/* Curtain wipe from bottom */
@keyframes curtain { from { clip-path: inset(100% 0 0 0); } to { clip-path: inset(0% 0 0 0); } }
.curtain-reveal { animation: curtain 0.6s cubic-bezier(0.16,1,0.3,1) forwards; }

/* Diagonal wipe */
@keyframes diagonal {
  from { clip-path: polygon(0 0, 0 0, 0 100%, 0 100%); }
  to   { clip-path: polygon(0 0, 100% 0, 100% 100%, 0 100%); }
}
```

---

## Entry Animations from display:none (@starting-style)

Solves the "can't animate from display:none" problem. Baseline 2024.

```css
.modal {
  opacity: 1; transform: scale(1) translateY(0);
  transition:
    opacity 0.4s cubic-bezier(0.16,1,0.3,1),
    transform 0.4s cubic-bezier(0.16,1,0.3,1),
    display 0.4s allow-discrete,
    overlay 0.4s allow-discrete;
}
@starting-style {
  .modal { opacity: 0; transform: scale(0.94) translateY(16px); }
}
.modal.is-hidden {
  opacity: 0; transform: scale(0.94) translateY(16px); display: none;
}
```

---

## View Transitions — Cross-Document (Shopify MPA)

```css
/* In base CSS — enables smooth page transitions */
@supports (view-transition-name: none) {
  @view-transition { navigation: auto; }

  ::view-transition-old(root) { animation: vt-out 0.25s ease-out; }
  ::view-transition-new(root) { animation: vt-in 0.25s ease-in; }
  @keyframes vt-out { to { opacity: 0; } }
  @keyframes vt-in  { from { opacity: 0; } }
}

/* Shared element morph: collection card → product hero */
.product-card__image { view-transition-name: product-hero; contain: layout; }
.product-hero__image  { view-transition-name: product-hero; contain: layout; }

::view-transition-group(product-hero) {
  animation-duration: 0.45s;
  animation-timing-function: cubic-bezier(0.16,1,0.3,1);
}
```

**Rule:** `view-transition-name` values must be unique per page — two elements cannot share the same name simultaneously.

---

## Mouse-Driven Cursor Parallax

```javascript
document.addEventListener('mousemove', (e) => {
  const x = (e.clientX / window.innerWidth).toFixed(3);
  const y = (e.clientY / window.innerHeight).toFixed(3);
  document.documentElement.style.setProperty('--mouse-x', x);
  document.documentElement.style.setProperty('--mouse-y', y);
});
```

```css
.parallax-near {
  transform: translate(calc((var(--mouse-x) - 0.5) * -30px), calc((var(--mouse-y) - 0.5) * -20px));
}
.parallax-far {
  transform: translate(calc((var(--mouse-x) - 0.5) * -8px), calc((var(--mouse-y) - 0.5) * -5px));
}
/* Cursor-following glow on background */
.cursor-glow {
  background: radial-gradient(
    circle 600px at calc(var(--mouse-x) * 100%) calc(var(--mouse-y) * 100%),
    rgba(201,169,110,0.07) 0%, transparent 50%
  );
}
```

---

## will-change — Correct Usage

```css
/* WRONG — forces GPU layer for ALL elements at all times */
.card { will-change: transform; }
```

```javascript
/* CORRECT — set just before animating, remove after */
el.addEventListener('mouseenter', () => { el.style.willChange = 'transform, box-shadow'; });
el.addEventListener('transitionend', () => { el.style.willChange = 'auto'; });
```

---

## CSS vs GSAP Decision Guide

| Effect | Use CSS | Use GSAP |
|--------|---------|---------|
| Gradient animation | `@property` — only way | Not possible natively |
| Scroll reveal (simple) | `animation-timeline: view()` | ScrollTrigger (complex/pinned) |
| Aurora background | `@property` + keyframes | Overkill |
| Text shimmer | `@property` + `background-clip` | Not needed |
| Card tilt | CSS custom props + JS setter | GSAP for smoother spring return |
| Entry from display:none | `@starting-style` | GSAP for complex stagger entry |
| Spring / bounce physics | Not possible in CSS | GSAP elastic eases |
| Sequenced multi-element | Not practical in CSS | GSAP timeline |
| Scroll scrub / pin | `animation-timeline` (simple) | ScrollTrigger (complex) |
