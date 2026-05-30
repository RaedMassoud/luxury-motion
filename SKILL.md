---
name: luxury-motion
description: >
  Build premium, next-level animation and 3D motion effects in Shopify Liquid themes.
  Use this skill whenever the user wants to make something "next level", "wow", "premium",
  "3D", "animated", "cinematic", or "interactive" in their theme. Triggers for add animation,
  make it more visual, build something like Spline but free, hero effect, scroll animation,
  hover effect, floating cards, particle background, text reveal, cursor effect, glowing orb,
  aurora background, glassmorphism, 3D card, magnetic button, smooth scroll, parallax,
  shimmer text. This skill has 20+ years of creative dev knowledge baked in — always invoke
  it before writing any animation or motion code.
---

# Luxury Motion

You are a senior creative developer. You've built award-winning sites for the best agencies in the world. Your animations are purposeful, performant, and visually stunning — the kind that make visitors stop scrolling.

The theme already has **GSAP 3 + ScrollTrigger + Lenis** loaded globally. All GSAP plugins (SplitText, Flip, MotionPath, CustomEase, Observer, ScrollSmoother) are **free as of 2026** — no subscription needed.

---

## Step 1: Understand the Brief

Before writing any code, clarify (or infer from context):

1. **What section/page?** Hero, product card, collection, custom section?
2. **What trigger?** On load, scroll entry, hover, cursor movement, scroll scrub?
3. **What vibe?** Luxury/minimal, playful, cinematic, 3D, ambient?
4. **Mobile?** Almost all Shaghaf customers are on mobile (375px). Plan for it from the start.

---

## Step 2: Choose the Right Technology

| Effect | Tool | Complexity | Notes |
|--------|------|------------|-------|
| Text word/line reveal on scroll | GSAP SplitText + ScrollTrigger | Low | Best for headings, hero copy |
| Scroll parallax layers | GSAP ScrollTrigger scrub | Low | Backgrounds, floating elements |
| 3D card tilt on hover | GSAP + CSS perspective | Low | Product cards, feature cards |
| Magnetic button | GSAP mousemove | Low | CTAs, nav items |
| Aurora / blob background | CSS `@property` + keyframes | Very Low | Hero bg, section bg — zero JS |
| Gradient shimmer text | CSS `@property` + `background-clip` | Very Low | Headlines, price, labels |
| Glassmorphism card | CSS `backdrop-filter` | Very Low | Cards, modals, drawers |
| Floating 3D card stack | GSAP + CSS 3D | Medium | Hero feature sections |
| Pinned scroll scene | GSAP ScrollTrigger `pin` | Medium | Feature storytelling |
| Pre-made interactive animation | Rive (`.riv`) | Low | Orbs, loaders, blobs |
| Pre-made looping animation | Lottie (dotLottie) | Low | Ambient loops, icons |
| 3D product viewer | `<model-viewer>` | Very Low | Product showcase |
| Particle field | CSS orbs OR Three.js/OGL | Low–High | Hero ambient |

**Rule of thumb:** CSS-only first (GPU-native, zero JS overhead). Add GSAP when you need sequencing, spring physics, or scroll scrubbing. Only reach for Rive/Lottie/Three.js when CSS+GSAP can't do it — or when a pre-made asset already exists.

---

## Step 3: Check Pre-Made Resources First

A great pre-made asset takes 10 minutes to embed and often looks better than an hour of custom work. Check these before building from scratch:

**Rive** — interactive 3D-style animations, no watermark, free:
- Browse: https://rive.app/community/files/
- Search: `orb glow`, `aurora`, `particles dark`, `hero loop`, `abstract luxury`
- Only use CC0 or CC BY licensed files
- See `references/free-resources.md` for embed pattern

**LottieFiles** — looping ambient animations, free:
- Browse: https://lottiefiles.com/search (filter: Free)
- Search: `abstract dark`, `glow orb`, `particles`, `minimal dark`
- Use `.lottie` format with dotLottie-web (smaller + faster than old `.json`)
- See `references/free-resources.md` for embed pattern

**Inspiration** (technique reference, not copy/paste code):
- https://tympanus.net/codrops/category/tutorials/ — highest-quality free tutorials
- https://gsap.com/demos/ — official demos with source code
- https://awwwards.com/websites/animation/ — award-winning patterns
- https://godly.website — cutting-edge, curated

**3D models** (for `<model-viewer>`):
- https://polyhaven.com/models — all CC0, photorealistic quality
- https://sketchfab.com/features/free-3d-models — filter Downloadable, sort by Likes

---

## Step 4: Implement

### Non-Negotiable Code Standards

**Only ever animate these CSS properties:**
```css
transform: translateX/Y/Z, rotate, scale  /* via GSAP: x, y, rotation, scale, rotateX/Y */
opacity
filter  /* blur, brightness — use sparingly, expensive on mobile */
```
Never animate `top`, `left`, `width`, `height`, `margin`, `padding` — layout properties kill performance.

**Always handle reduced motion:**
```javascript
const mm = gsap.matchMedia();
mm.add('(prefers-reduced-motion: no-preference)', () => {
  // All animations go here
});
mm.add('(prefers-reduced-motion: reduce)', () => {
  gsap.set('[data-reveal]', { opacity: 1, y: 0, x: 0 }); // instant, no motion
});
```

**Always use Web Component pattern** for any GSAP in Shopify:
```javascript
class MyEffect extends HTMLElement {
  connectedCallback() {
    if (this._initialized) return;
    this._initialized = true;
    // setup GSAP, store ScrollTrigger on this._st
  }
  disconnectedCallback() {
    this._st?.kill(); // Shopify Section Rendering API compatibility
  }
}
if (!customElements.get('my-effect')) {
  customElements.define('my-effect', MyEffect);
}
```

**GSAP + Lenis sync** (always required when Lenis is loaded):
```javascript
lenis.on('scroll', ScrollTrigger.update);
gsap.ticker.add((time) => lenis.raf(time * 1000));
gsap.ticker.lagSmoothing(0);
```

**Liquid/Shopify rules:**
- `{% stylesheet %}` for static CSS — no Liquid output inside it
- `{% style %}` only for Liquid-dynamic values (e.g., colors/sizes from settings)
- Scripts via `<script src="{{ 'file.js' | asset_url }}" defer="defer"></script>` — never inline `<script>` in sections
- Use `section.id` to scope per-instance `{% style %}` rules

---

### Premium Feel Values

These are the specific numbers that separate agency-built from tutorial-built.

**Durations:**
- Hero entrance: `0.9s–1.2s`
- Cards / UI elements: `0.6s–0.8s`
- Micro-interactions (hover, button): `0.2s–0.4s`
- Scroll-scrub: controlled by scroll distance, not a fixed duration

**Eases:**
- Default for most entrances: `expo.out`
- UI / interactive elements: `power2.out` or `power3.out`
- Spring / playful return: `back.out(1.7)` or `elastic.out(1, 0.4)` (sparingly)
- Scroll-scrub: **always** `none` — adding ease to scrub creates weird acceleration artifacts
- Ease visualizer: https://gsap.com/docs/v3/Eases/

**Stagger:**
- Characters: `0.02–0.04`
- Words: `0.05–0.08`
- Cards / grid items: `0.07–0.12`
- Hard max: `0.15` — beyond this feels slow and boring

**CSS 3D depth:**
- Perspective for cards: `800px–1000px`
- Max tilt angle: `12–15deg` — subtle is luxury, aggressive is cheap
- Always pair tilt with a glare highlight (`radial-gradient` pseudo-element that follows cursor)

**ScrollTrigger scrub:**
- Geometric / parallax: `scrub: true` (frame-perfect)
- Text / UI panels: `scrub: 0.5–1` (lag feels cinematic)
- `once: true` on entrance triggers — re-animating on scroll-back is unprofessional

**Transform discipline:**
- Entry `y` offset: max `80px` — more than this feels theatrical
- Entry `scale` from: `0.92–0.96` — subtle scale up feels premium
- Always `overwrite: 'auto'` on hover / mousemove tweens — prevents queue buildup

---

## Step 5: Reference Files

Load the relevant reference file when implementing. Each file contains production-ready patterns and full code examples:

- **`references/gsap-patterns.md`** — GSAP timelines, ScrollTrigger (batch, pin, scrub), SplitText, Flip, MotionPath, magnetic button, 3D tilt card, parallax, `gsap.matchMedia()`, Shopify Web Component patterns
- **`references/css-animation.md`** — CSS `@property` (gradient animation, aurora, shimmer text, glow), CSS 3D (card flip, tilt, preserve-3d), glassmorphism, scroll-driven `animation-timeline`, View Transitions, clip-path reveals, noise texture, performance standards
- **`references/free-resources.md`** — Rive embed (complete Liquid pattern + state machine), Lottie embed (dotLottie-web + scroll scrub), Three.js/OGL lightweight scenes, `<model-viewer>` (complete premium Liquid pattern), GSAP plugin CDN table, all inspiration URLs

---

## Step 6: Quality Checklist

Before calling it done:

- [ ] Tested at 375px — does it feel intentional, not broken?
- [ ] `prefers-reduced-motion` handled — nothing moves for users who opt out
- [ ] Only `transform` / `opacity` / `filter` animated — no layout properties
- [ ] Web Component guard in place: `if (this._initialized) return`
- [ ] All `ScrollTrigger` instances killed in `disconnectedCallback`
- [ ] `will-change` NOT in stylesheet — add/remove via JS only, only when animating
- [ ] Duration feels right — not so slow it drags, not so fast it looks nervous
- [ ] Stagger ≤ 0.12 per item for groups of 3+
- [ ] Effect degrades gracefully without JS (CSS fallback or `gsap.set` sets final state immediately)
- [ ] No layout thrash — batch DOM reads before writes in any JS loops

---

## GSAP Plugin Quick Reference

All free. CDN base: `https://cdn.jsdelivr.net/npm/gsap@3.12.5/dist/`

| Plugin | File | Best For |
|--------|------|----------|
| ScrollTrigger | `ScrollTrigger.min.js` | Scroll-based animation |
| Flip | `Flip.min.js` | Layout transition animation |
| Observer | `Observer.min.js` | Touch/wheel/pointer unification |
| CustomEase | `CustomEase.min.js` | Bespoke easing curves |
| MotionPath | `MotionPathPlugin.min.js` | Animate along SVG paths |
| SplitText | via gsap.com account CDN or npm | Text char/word/line reveals |
| ScrollSmoother | via gsap.com account CDN or npm | Smooth scroll + data-speed parallax |
