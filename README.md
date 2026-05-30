# luxury-motion

A Claude Code skill for building premium, next-level animations and motion effects in Shopify Liquid themes. Covers GSAP, CSS 3D, scroll-driven animations, Rive, Lottie, Three.js, and model-viewer.

## What it does

When invoked, this skill acts as a senior creative developer with deep knowledge of modern web animation. It guides you from brief to finished code by choosing the right technology, checking for free pre-made assets first, and producing clean, performant, agency-quality output.

## When to use it

Invoke `/luxury-motion` before writing any animation or motion code. It triggers on requests like:

- "make this section next level"
- "add a hero animation"
- "floating 3D cards"
- "text reveal on scroll"
- "aurora background"
- "glassmorphism card"
- "magnetic button"
- "glowing orb"
- "smooth scroll parallax"
- "something like Spline but free"

## Contents

```
luxury-motion/
├── SKILL.md                        - Main skill: decision matrix, standards, quality checklist
└── references/
    ├── gsap-patterns.md            - GSAP timelines, ScrollTrigger, SplitText, Flip, magnetic button, tilt card
    ├── css-animation.md            - @property, CSS 3D, glassmorphism, aurora, shimmer text, scroll-driven
    └── free-resources.md           - Rive, Lottie, model-viewer embed patterns + inspiration links
```

## What is baked in

- Full decision matrix: CSS vs GSAP vs Rive vs Lottie vs Three.js vs model-viewer
- Premium feel values: exact durations, eases, stagger timing, perspective depth
- All GSAP patterns written as Shopify Web Components (Section Rendering API safe)
- Lenis + GSAP ScrollTrigger sync pattern
- `prefers-reduced-motion` handling on every animation
- Pre-made resource checklist: Rive community, LottieFiles, Poly Haven, Sketchfab
- GSAP plugin CDN table (all plugins are free as of 2026)
- Inspiration references: Codrops, Awwwards, Godly, Stripe, Apple, Linear

## Installation

This skill installs as a local Claude Code plugin. One-time setup per machine.

### 1. Create the local marketplace structure

Pick any directory (e.g. `~/.claude/plugins/marketplaces/local/`) and create this layout:

```
local/
├── .claude-plugin/
│   └── marketplace.json
└── plugins/
    └── luxury-motion/
        ├── .claude-plugin/
        │   └── plugin.json
        └── skills/
            └── luxury-motion/
                ├── SKILL.md
                └── references/
                    ├── gsap-patterns.md
                    ├── css-animation.md
                    └── free-resources.md
```

Copy the files from this repo into `plugins/luxury-motion/skills/luxury-motion/`.

### 2. Create the marketplace manifest

`local/.claude-plugin/marketplace.json`:

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "local",
  "description": "Local plugins",
  "owner": { "name": "", "email": "" },
  "plugins": [
    {
      "name": "luxury-motion",
      "description": "Build premium animations and motion effects in Shopify Liquid themes.",
      "author": { "name": "", "email": "" },
      "category": "design",
      "source": "./plugins/luxury-motion"
    }
  ]
}
```

### 3. Register the marketplace

```bash
claude plugin marketplace add /path/to/local
```

### 4. Install the plugin

```bash
claude plugin install luxury-motion@local
```

### 5. Reload

Run `/reload-plugins` in Claude Code, or restart the app.
