# Free Resources Reference

## Rive — Complete Shopify Embed

Use for: interactive 3D-style animations, morphing orbs, particle blobs, hero loops. Free, no watermark.

**Find animations:** https://rive.app/community/files/
Search: `orb glow`, `aurora`, `particles dark`, `hero loop`, `abstract luxury`
Only use files marked CC0 or CC BY.

**Runtime:** Use `@rive-app/webgl2` (primary, full features). Use `@rive-app/canvas-lite` for smaller bundle when you don't need Rive Text/Layouts.

**Full Shopify Liquid Embed:**

```liquid
<canvas id="rive-canvas-{{ section.id }}" style="width:100%;height:100%;display:block;"></canvas>

{% comment %}theme-check-disable RemoteAsset{% endcomment %}
<script src="https://unpkg.com/@rive-app/webgl2" defer></script>
{% comment %}theme-check-enable RemoteAsset{% endcomment %}

<script>
  document.addEventListener('DOMContentLoaded', function () {
    const canvas = document.getElementById('rive-canvas-{{ section.id }}');
    if (!canvas || !window.rive) return;

    const r = new rive.Rive({
      src: '{{ section.settings.rive_file | asset_url }}',
      canvas: canvas,
      autoplay: true,
      stateMachines: 'State Machine 1',
      onLoad: () => r.resizeDrawingSurfaceToCanvas(),
    });

    window.addEventListener('resize', () => r.resizeDrawingSurfaceToCanvas());
    document.addEventListener('shopify:section:unload', () => r.cleanup());
  });
</script>
```

Upload the `.riv` file to theme Assets, then reference it via `asset_url`.

**State machine inputs (hover, scroll):**
```javascript
const inputs = r.stateMachineInputs('State Machine 1');
const hoverInput = inputs.find(i => i.name === 'isHovered');
canvas.addEventListener('mouseenter', () => { hoverInput.value = true; });
canvas.addEventListener('mouseleave', () => { hoverInput.value = false; });
```

Rive docs: https://rive.app/docs/runtimes/web

---

## Lottie (dotLottie-web) — Complete Shopify Embed

Use for: looping ambient animations, icon animations, abstract loops. Free, no watermark.

**Find animations:** https://lottiefiles.com/search (filter: Free)
Search: `abstract dark`, `glow orb`, `particles`, `minimal dark`, `luxury`

**Use `.lottie` format** (not `.json`) — 30–50% smaller, compressed.

**Full Shopify Liquid Embed:**

```liquid
<canvas id="lottie-canvas-{{ section.id }}" style="width:100%;height:100%;display:block;"></canvas>

{% comment %}theme-check-disable RemoteAsset{% endcomment %}
<script src="https://unpkg.com/@lottiefiles/dotlottie-web/dist/dotlottie.js" defer></script>
{% comment %}theme-check-enable RemoteAsset{% endcomment %}

<script>
  document.addEventListener('DOMContentLoaded', function () {
    const canvas = document.getElementById('lottie-canvas-{{ section.id }}');
    if (!canvas || !window.DotLottie) return;

    new DotLottie({
      canvas: canvas,
      src: '{{ section.settings.lottie_file | asset_url }}',
      autoplay: true,
      loop: true,
      renderConfig: { devicePixelRatio: Math.min(window.devicePixelRatio || 2, 2) },
    });
  });
</script>
```

**Scroll-scrubbed Lottie:**
```javascript
const dotLottie = new DotLottie({ canvas, src, autoplay: false, loop: false });

dotLottie.addEventListener('load', () => {
  const total = dotLottie.totalFrames;
  window.addEventListener('scroll', () => {
    const pct = window.scrollY / (document.body.scrollHeight - window.innerHeight);
    dotLottie.goToAndStop(pct * total, true);
  });
});
```

dotLottie-web docs: https://github.com/LottieFiles/dotlottie-web

---

## model-viewer — 3D Product Viewer

Use for: product showcase, 3D model display, AR. Free, no watermark, Google-maintained.

```liquid
{% comment %}theme-check-disable RemoteAsset{% endcomment %}
<script type="module" src="https://ajax.googleapis.com/ajax/libs/model-viewer/3.5.0/model-viewer.min.js"></script>
{% comment %}theme-check-enable RemoteAsset{% endcomment %}

<model-viewer
  src="{{ section.settings.model_glb | asset_url }}"
  ios-src="{{ section.settings.model_usdz | asset_url }}"
  alt="{{ section.settings.model_alt | escape }}"

  camera-controls
  auto-rotate
  auto-rotate-delay="1000"
  rotation-per-second="30deg"
  camera-orbit="0deg 75deg 2m"
  field-of-view="30deg"

  environment-image="legacy"
  exposure="1.2"
  shadow-intensity="1"
  shadow-softness="0.8"
  tone-mapping="commerce"

  loading="lazy"
  reveal="interaction"

  style="width:100%;height:500px;background:transparent;"
>
  <div slot="poster" style="display:flex;align-items:center;justify-content:center;height:100%;">
    <img src="{{ section.settings.poster_image | image_url: width: 800 }}" alt="{{ section.settings.model_alt | escape }}" style="max-height:100%;">
  </div>
</model-viewer>
```

**Premium lighting tips:**
- `tone-mapping="commerce"` — designed for product photography feel
- `exposure="1.0–1.5"` — raise for luxury warmth
- `shadow-softness="0.7–1"` — soft ground shadow = premium
- Use `environment-image` (HDR) for accurate reflections, not `skybox-image`

**Free .glb model sources:**
- https://polyhaven.com/models — all CC0, photorealistic
- https://sketchfab.com/features/free-3d-models — filter Downloadable, sort by Likes
- https://fab.com — Epic's marketplace, many free assets

**Optimize before upload:** https://gltf.report/ — browser-based GLB analyzer/optimizer. Target under 3MB for hero, under 1MB for product thumbnails.

model-viewer docs: https://model-viewer.dev/

---

## GSAP Plugin CDN Reference

All plugins free. CDN base: `https://cdn.jsdelivr.net/npm/gsap@3.12.5/dist/`

| Plugin | File | Use For |
|--------|------|---------|
| ScrollTrigger | `ScrollTrigger.min.js` | All scroll-based animation |
| Flip | `Flip.min.js` | Layout transitions, filter/sort animation |
| Observer | `Observer.min.js` | Touch/wheel/pointer unification |
| CustomEase | `CustomEase.min.js` | Bespoke easing curves |
| MotionPath | `MotionPathPlugin.min.js` | Animate along SVG paths |
| ScrollTo | `ScrollToPlugin.min.js` | Programmatic scroll to position |
| EasePack | `EasePack.min.js` | Extra eases (slow, rough, expoScale) |
| SplitText | gsap.com free account CDN / npm | Text char/word/line reveals |
| ScrollSmoother | gsap.com free account CDN / npm | Smooth scroll + `data-speed` parallax |
| Draggable | `Draggable.min.js` | Touch/mouse drag, carousels |

SplitText and ScrollSmoother need a free GSAP account: https://gsap.com (then use their private CDN or npm).

Ease visualizer: https://gsap.com/docs/v3/Eases/
GSAP demos: https://gsap.com/demos/

---

## Three.js / OGL Lightweight Patterns

### When to Use

Use **Three.js / OGL** when CSS + GSAP can't achieve the result:
- GPU particle systems (thousands of particles, smooth 60fps)
- GLSL shader backgrounds (noise, fluid, distortion)
- Interactive 3D product scenes

Skip it for anything achievable with CSS + GSAP — the bundle size and maintenance cost isn't worth it for decorative effects.

**OGL** (https://github.com/oframe/ogl) — 29KB min. Better choice than Three.js for Shopify themes (smaller, same capabilities for shader work).

### Minimal Three.js Particle Field (~0 scene data)

```javascript
// Procedural — no external assets needed
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, innerWidth / innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer({ canvas: document.getElementById('webgl'), alpha: true });
renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
renderer.setSize(innerWidth, innerHeight);

const geo = new THREE.BufferGeometry();
const positions = new Float32Array(3000 * 3);
for (let i = 0; i < positions.length; i++) positions[i] = (Math.random() - 0.5) * 10;
geo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
const mat = new THREE.PointsMaterial({ size: 0.015, color: 0xc9a96e, transparent: true, opacity: 0.6 });
scene.add(new THREE.Points(geo, mat));
camera.position.z = 3;

const tick = () => { requestAnimationFrame(tick); renderer.render(scene, camera); };
tick();
```

### GLSL Learning Resources

- https://thebookofshaders.com/ — free, interactive, the definitive GLSL book
- https://shadertoy.com — thousands of GLSL examples, all runnable (reference, not copy-paste)
- https://lygia.xyz/ — free GLSL shader functions library
- https://threejs-journey.com — $95 one-time, has a full shader chapter

---

## Inspiration Reference

### Specific Techniques by Brand

**Linear.app** — Pure CSS conic gradients + subtle glow animations. No WebGL. Proof that CSS alone can look premium.

**Stripe.com** — Animated gradient mesh via `<canvas>` + GLSL noise. 4–5 colors cycling through noise function. Open source recreation: https://github.com/sarcadass/granim.js (free)

**Apple.com** — Scroll-scrubbed video: `video.currentTime = progress * video.duration` via ScrollTrigger. Product shots are GSAP parallax on high-res images, not WebGL.

### Curation Sites

- https://tympanus.net/codrops/category/tutorials/ — highest-quality free tutorials with full source code
- https://gsap.com/demos/ — official demos with CodePen source
- https://awwwards.com/websites/animation/ — award-winning animation patterns
- https://godly.website — curated cutting-edge sites
- https://siteinspire.com — premium minimal design references

### Notable Codepen Creators

- https://codepen.io/GreenSock — GSAP official, all demos free
- https://codepen.io/jh3y — Jhey Tompkins, creative CSS
- https://codepen.io/Hyperplexed — glassmorphism, CSS 3D
- https://codepen.io/argyleink — Adam Argyle (Chrome DevRel), modern CSS

### CSS-Only Ambient Orb (no JS, no Three.js)

The simplest "next level" background — pure CSS, zero overhead:

```css
.orb-container { position: absolute; inset: 0; overflow: hidden; }

.orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  mix-blend-mode: screen;
  opacity: 0.6;
  animation: orb-float var(--duration) ease-in-out infinite alternate;
}

.orb-1 {
  width: 600px; height: 600px;
  top: -200px; left: -100px;
  background: radial-gradient(circle, rgba(139,92,246,0.5) 0%, transparent 70%);
  --duration: 10s;
}
.orb-2 {
  width: 500px; height: 500px;
  bottom: -150px; right: -100px;
  background: radial-gradient(circle, rgba(201,169,110,0.4) 0%, transparent 70%);
  --duration: 13s;
  animation-delay: -4s;
}
.orb-3 {
  width: 400px; height: 400px;
  top: 40%; left: 50%;
  background: radial-gradient(circle, rgba(59,130,246,0.3) 0%, transparent 70%);
  --duration: 8s;
  animation-delay: -7s;
}

@keyframes orb-float {
  from { transform: translate(0, 0) scale(1); }
  to   { transform: translate(40px, -30px) scale(1.08); }
}

@media (prefers-reduced-motion: reduce) {
  .orb { animation: none; }
}
```
