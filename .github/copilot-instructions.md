# Backyard Ramp – Copilot Instructions

## Project overview

Static GitHub Pages site documenting a DIY mini ramp build. No build step, no package manager, no framework — a single `index.html` with all CSS and JS inline, deployed directly from the `develop` branch.

Live at: **https://backyardramp.mayjestic.net/**

## Architecture

Everything lives in `index.html`:
- **Hero** — full-viewport section with `assets/ramp_flags.mp4` as background video (`autoplay loop muted playsinline`), dark overlay via `::after` pseudo-element
- **Gallery** — two Swiper.js instances (CDN): a main slider and a linked thumbnail strip below it
- **About + Footer** — static text sections

Swiper is loaded from `https://cdn.jsdelivr.net/npm/swiper@11/swiper-bundle.min.css` and `.min.js`. No local JS/CSS files.

## Image list generation

Images are built at runtime in JS — **not hardcoded as individual slides**:

```js
const jpegSet = new Set([62, 73, 79, 80]); // these use .jpeg, rest use .jpg
const skipSet = new Set([32]);              // miniramp_0032 does not exist
for (let i = 1; i <= 80; i++) { … }
```

When adding new images, update `jpegSet`, `skipSet`, or the loop ceiling accordingly.

## Captions

Captions are assigned by phase: one caption string per group of ~8 images via `Math.floor(i / 8)`. The `captions[]` array has 10 entries. To add per-image captions, replace the array lookup with a map keyed by image index.

## Deployment

- Branch: `develop` → GitHub Pages root
- Custom domain configured via `CNAME` file (`backyardramp.mayjestic.net`)
- `.nojekyll` prevents Jekyll processing
- `robots.txt` blocks all search engine indexing

No CI/CD pipeline — a `git push` to `develop` deploys automatically.

## Key constraints

- **No build tooling** — keep everything in `index.html`; do not introduce npm, bundlers, or frameworks without explicit agreement
- **GitHub Pages CDN only** — external resources must be loaded from public CDNs (jsDelivr, unpkg); no private registries
- **Mobile-first breakpoints** — `≤600px` and `≤380px` are the active breakpoints; nav arrows are hidden on mobile (swipe only)
- **Video autoplay** — always include `muted` and `playsinline` for cross-browser/iOS autoplay support
