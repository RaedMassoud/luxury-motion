# GSAP Patterns Reference

## Core: When to Use Each Method

```javascript
gsap.to('.el', { y: 0, opacity: 1 })        // element already in final state, animate away
gsap.from('.el', { y: 60, opacity: 0 })      // entrance — animate FROM an offset
gsap.fromTo('.el', { y: 60 }, { y: 0 })      // use inside ScrollTrigger scrub — predictable both ways
gsap.set('.el', { y: 40, opacity: 0 })       // instant state — use BEFORE ScrollTrigger fires
```

**Rule:** Prefer `gsap.fromTo()` inside any `scrub` ScrollTrigger — both values always known, no direction-change artifacts.

---

## Timeline — Sequencing

```javascript
const tl = gsap.timeline({
  defaults: { duration: 0.7, ease: 'expo.out' }, // shared defaults
});

tl.addLabel('start')
  .from('.hero-eyebrow',  { y: 20, opacity: 0 },         'start')
  .from('.hero-heading',  { y: 40, opacity: 0 },         'start+=0.1')
  .from('.hero-subtext',  { y: 30, opacity: 0 },         'start+=0.25')
  .from('.hero-cta',      { y: 20, opacity: 0, scale: 0.96 }, 'start+=0.4')
  .to('.bg-shape', { scale: 1.06, duration: 1.2 }, '<0.2'); // '<' = start of previous
```

---

## ScrollTrigger Patterns

### Scroll Reveal (entrance)
```javascript
gsap.registerPlugin(ScrollTrigger);

gsap.set('.reveal-item', { y: 40, opacity: 0 });

ScrollTrigger.batch('.reveal-item', {
  interval: 0.15,
  batchMax: 4,
  onEnter: (els) => {
    gsap.to(els, {
      opacity: 1, y: 0,
      duration: 0.7,
      ease: 'power2.out',
      stagger: { each: 0.1 },
      overwrite: true,
    });
  },
  start: 'top 90%',
});
```

### Scrub: Parallax
```javascript
// scrub: true — frame-perfect sync. Use for parallax, progress bars.
gsap.to('.bg-layer', {
  yPercent: -20,
  ease: 'none',  // ALWAYS none for scrub
  scrollTrigger: {
    trigger: '.hero',
    start: 'top bottom',
    end: 'bottom top',
    scrub: true,
  },
});

// scrub: 0.8 — animation lags behind scroll. Feels cinematic. Use for text/UI panels.
gsap.fromTo('.feature-text',
  { x: 80, opacity: 0 },
  {
    x: 0, opacity: 1,
    scrollTrigger: {
      trigger: '.feature-section',
      scrub: 0.8,
      start: 'top 80%',
      end: 'top 20%',
    },
  }
);
```

### Pin — Scroll Scene
```javascript
const tl = gsap.timeline();
tl.from('.step-1', { opacity: 0, y: 30, duration: 0.4 })
  .from('.step-2', { opacity: 0, y: 30, duration: 0.4 })
  .from('.step-3', { opacity: 0, y: 30, duration: 0.4 });

ScrollTrigger.create({
  trigger: '.pinned-section',
  start: 'top top',
  end: '+=200%',
  pin: true,
  scrub: 1,
  animation: tl,
  anticipatePin: 1,
});
```

### toggleClass (for header compact state, etc.)
```javascript
ScrollTrigger.create({
  trigger: '.scroll-trigger',
  start: 'top top',
  toggleClass: { targets: '.site-header', className: 'is-scrolled' },
});
```

---

## SplitText — Text Reveal

SplitText is free (all GSAP plugins free as of 2026). Requires a free account CDN or npm.

```javascript
gsap.registerPlugin(SplitText, ScrollTrigger);

document.fonts.ready.then(() => {
  SplitText.create('.headline', {
    type: 'words,lines',
    mask: 'lines',       // clean clip — no overflow needed on parent
    autoSplit: true,     // re-splits on resize automatically
    onSplit(self) {
      return gsap.from(self.words, {
        y: 100,
        opacity: 0,
        duration: 1,
        ease: 'power4.out',
        stagger: { each: 0.05 },
        scrollTrigger: {
          trigger: self.elements[0],
          start: 'top 85%',
          once: true,
        },
      });
    },
  });
});
```

**Line-by-line reveal (body text):**
```javascript
SplitText.create('.body-copy', {
  type: 'lines',
  autoSplit: true,
  onSplit(self) {
    return gsap.from(self.lines, {
      y: 30, opacity: 0,
      duration: 0.6, ease: 'power2.out',
      stagger: 0.1,
      scrollTrigger: { trigger: self.elements[0], start: 'top 88%', once: true },
    });
  },
});
```

---

## Magnetic Button

```javascript
class MagneticButton extends HTMLElement {
  connectedCallback() {
    this.addEventListener('mousemove', this.#onMove.bind(this));
    this.addEventListener('mouseleave', this.#onLeave.bind(this));
  }
  #onMove(e) {
    const rect = this.getBoundingClientRect();
    const dx = (e.clientX - (rect.left + rect.width / 2)) * 0.35;
    const dy = (e.clientY - (rect.top + rect.height / 2)) * 0.35;
    gsap.to(this, { x: dx, y: dy, duration: 0.4, ease: 'power2.out', overwrite: 'auto' });
  }
  #onLeave() {
    gsap.to(this, { x: 0, y: 0, duration: 0.7, ease: 'elastic.out(1, 0.4)', overwrite: 'auto' });
  }
}
if (!customElements.get('magnetic-button')) customElements.define('magnetic-button', MagneticButton);
```

```liquid
<magnetic-button>
  <button class="btn-primary">Shop Now</button>
</magnetic-button>
```

---

## 3D Card Tilt (mousemove → rotateX/Y + glare)

```javascript
class TiltCard extends HTMLElement {
  connectedCallback() {
    this._strength = parseFloat(this.dataset.strength || '12');
    gsap.set(this, { transformPerspective: 900, transformStyle: 'preserve-3d' });
    this.addEventListener('mouseenter', () => gsap.to(this, { scale: 1.02, duration: 0.3, ease: 'power2.out' }));
    this.addEventListener('mousemove', this.#onMove.bind(this));
    this.addEventListener('mouseleave', this.#onLeave.bind(this));
  }
  #onMove(e) {
    const r = this.getBoundingClientRect();
    const x = (e.clientX - r.left) / r.width - 0.5;   // -0.5 to 0.5
    const y = (e.clientY - r.top) / r.height - 0.5;
    gsap.to(this, {
      rotationY: x * this._strength,
      rotationX: -y * this._strength,
      duration: 0.4, ease: 'power2.out', overwrite: 'auto',
    });
    // Glare highlight via CSS custom property
    this.style.setProperty('--glare-x', `${(x + 0.5) * 100}%`);
    this.style.setProperty('--glare-y', `${(y + 0.5) * 100}%`);
  }
  #onLeave() {
    gsap.to(this, { rotationX: 0, rotationY: 0, scale: 1, duration: 0.7, ease: 'power2.out', overwrite: 'auto' });
  }
}
if (!customElements.get('tilt-card')) customElements.define('tilt-card', TiltCard);
```

```css
tilt-card {
  display: block;
  position: relative;
  overflow: hidden;
}
tilt-card::after {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at var(--glare-x, 50%) var(--glare-y, 50%), rgba(255,255,255,0.12) 0%, transparent 60%);
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.3s ease;
}
tilt-card:hover::after { opacity: 1; }
```

---

## Parallax — Multi-Layer

```javascript
[
  { el: '.parallax-bg',   speed: -0.15 },
  { el: '.parallax-mid',  speed: -0.05 },
  { el: '.parallax-fore', speed:  0.08 },
].forEach(({ el, speed }) => {
  gsap.to(el, {
    yPercent: speed * 100,
    ease: 'none',
    scrollTrigger: {
      trigger: '.parallax-section',
      start: 'top bottom',
      end: 'bottom top',
      scrub: true,
    },
  });
});
```

---

## gsap.matchMedia() — Responsive + Reduced Motion

```javascript
const mm = gsap.matchMedia();
mm.add(
  {
    isDesktop:    '(min-width: 1024px)',
    isMobile:     '(max-width: 767px)',
    reduceMotion: '(prefers-reduced-motion: reduce)',
  },
  ({ conditions: { isDesktop, isMobile, reduceMotion } }) => {
    if (reduceMotion) {
      gsap.set('.hero-title', { opacity: 1, y: 0 });
      return;
    }
    gsap.from('.hero-title', {
      y: isDesktop ? 80 : 30,
      opacity: 0,
      duration: isDesktop ? 1.1 : 0.7,
      ease: 'expo.out',
    });
    return () => ScrollTrigger.getAll().forEach(t => t.kill()); // cleanup
  }
);
```

---

## Flip Plugin — Layout Transition

```javascript
gsap.registerPlugin(Flip);

document.querySelector('.card').addEventListener('click', () => {
  const state = Flip.getState('.card, .card-content');
  document.querySelector('.card').classList.toggle('is-expanded');
  Flip.from(state, {
    duration: 0.6,
    ease: 'power2.inOut',
    absolute: true,
    nested: true,
  });
});
```

---

## Lenis + GSAP Integration (canonical 2025/2026)

```javascript
const lenis = new Lenis({ duration: 1.2 });
lenis.on('scroll', ScrollTrigger.update);
gsap.ticker.add((time) => lenis.raf(time * 1000));
gsap.ticker.lagSmoothing(0);
```

---

## Shopify Web Component Pattern (required for all GSAP in sections)

```javascript
class RevealSection extends HTMLElement {
  connectedCallback() {
    if (this._initialized) return;
    this._initialized = true;

    const delay = parseFloat(this.dataset.delay || '0');

    gsap.set(this, { y: 50, opacity: 0 });

    this._st = ScrollTrigger.create({
      trigger: this,
      start: 'top 88%',
      once: true,
      onEnter: () => {
        gsap.to(this, { y: 0, opacity: 1, duration: 0.8, delay, ease: 'expo.out', overwrite: 'auto' });
      },
    });
  }
  disconnectedCallback() {
    this._st?.kill();
  }
}
if (!customElements.get('reveal-section')) customElements.define('reveal-section', RevealSection);
```

**Re-init after Shopify Section Rendering:**
```javascript
document.addEventListener('shopify:section:load', () => ScrollTrigger.refresh());
```

---

## Animate CSS Custom Properties via GSAP

```javascript
// Animate a CSS variable (drives multiple CSS rules at once)
gsap.to(document.documentElement, {
  '--hero-progress': 1,
  duration: 1.5,
  ease: 'power2.out',
});

// Scrub a blur amount on scroll
gsap.fromTo(document.documentElement,
  { '--blur': '0px' },
  {
    '--blur': '12px',
    ease: 'none',
    scrollTrigger: { trigger: '.blur-section', scrub: true },
  }
);
```

```css
.hero-bg { filter: blur(var(--blur, 0px)); }
```

---

## Stagger Quick Reference

```javascript
// Simple
gsap.from('.card', { y: 50, opacity: 0, duration: 0.8, stagger: 0.09, ease: 'power2.out' });

// Advanced — ripple from center
gsap.from('.grid-item', {
  scale: 0.9, opacity: 0, duration: 0.6,
  stagger: { each: 0.06, from: 'center', grid: 'auto', ease: 'power2.inOut' },
});
```
