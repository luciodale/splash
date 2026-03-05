# Splash SVG Spec

Rules for creating new splash screen SVGs that are visually consistent with the existing set.

## Canvas

- `viewBox="0 0 120 120"` — always
- Visual center: **(60, 60)** — all content must be symmetrically centered here
- Content zone: **36–84** on both axes (inner ~48x48 area for shapes)
- `fill="none"` on the root `<svg>`
- `xmlns="http://www.w3.org/2000/svg"`

## Outer Frame

Every SVG has exactly one outer frame:

```xml
<rect x="30" y="30" width="60" height="60" rx="13" stroke="url(#PREFIX-lg)" stroke-width="0.7" fill="none"/>
```

Frame gradient (define in `<defs>`, replace `PREFIX` with your unique ID prefix):

```xml
<linearGradient id="PREFIX-lg" x1="30%" y1="0%" x2="70%" y2="100%">
  <stop offset="0%" stop-color="currentColor" stop-opacity="0.6"/>
  <stop offset="100%" stop-color="currentColor" stop-opacity="0.12"/>
</linearGradient>
```

## Colors

- **Only use `currentColor`** — never hardcoded colors
- The parent HTML sets `color` via CSS variables so it adapts to light/dark mode automatically

## Strokes

| Use | stroke-width | stroke-opacity |
|-----|-------------|----------------|
| Primary shapes | 0.8–1.0 | 0.4–0.55 |
| Secondary/subtle shapes | 0.6–0.8 | 0.08–0.3 |
| Outer frame | 0.7 | via gradient |

## Fills

- Shapes: `fill="currentColor"` with `fill-opacity="0.03"` to `0.07`
- Dots/indicators: `fill="currentColor"` with `opacity="0.3"` to `0.6`
- Never use solid opaque fills

## Animations

All animations use `repeatCount="indefinite"`.

| Type | Duration | Notes |
|------|----------|-------|
| Breathing opacity | 2–3s | e.g. `values="0.5;0.9;0.5"` |
| Fill pulse | 2.5–3s | e.g. `fill-opacity` from 0.04 to 0.12 |
| Orbit/rotation | 5–10s | `animateTransform type="rotate"` around (60,60) |
| Scan line sweep | 4–5s | horizontal or vertical, fade in/out at edges |
| Staggered groups | +0.4s per step | use `begin="0.4s"`, `begin="0.8s"`, etc. |

### Orbit pattern (reliable)

Place the dot at its orbital radius, then rotate around center:

```xml
<circle cx="[60 + radius]" cy="60" r="2" fill="currentColor" opacity="0.4">
  <animateTransform attributeName="transform" type="rotate"
    from="0 60 60" to="360 60 60" dur="7s" repeatCount="indefinite"/>
</circle>
```

Reverse orbit: change `to` to `"-360 60 60"`.

### Scan line pattern (optional)

Horizontal sweep across the content zone:

```xml
<linearGradient id="PREFIX-scan" x1="0%" y1="50%" x2="100%" y2="50%">
  <stop offset="0%" stop-color="currentColor" stop-opacity="0"/>
  <stop offset="50%" stop-color="currentColor" stop-opacity="0.4"/>
  <stop offset="100%" stop-color="currentColor" stop-opacity="0"/>
</linearGradient>

<line x1="31" y1="60" x2="89" y2="60" stroke="url(#PREFIX-scan)" stroke-width="0.7">
  <animate attributeName="y1" values="31;89;31" dur="4.5s" repeatCount="indefinite"/>
  <animate attributeName="y2" values="31;89;31" dur="4.5s" repeatCount="indefinite"/>
  <animate attributeName="opacity" values="0;1;1;0" keyTimes="0;0.1;0.9;1" dur="4.5s" repeatCount="indefinite"/>
</line>
```

## ID Prefixes

Each SVG must use a unique prefix to avoid gradient/filter ID collisions when multiple SVGs are on the same page:

| File | Prefix |
|------|--------|
| splash-spaces.svg | `s-` |
| splash-presets.svg | `p-` |
| splash-agent.svg | `a-` |
| (new file) | pick a unique single letter + `-` |

## Existing Examples

- **Presets** (`p-`): 2x2 grid of rounded rects with dots, staggered breathing, horizontal scan line
- **Spaces** (`s-`): Central dot with two concentric orbit rings, each with a traveling dot
- **Agent** (`a-`): Three stacked horizontal bars (pipeline), indicator dots, traveling pulse, scan line

## Checklist for a New SVG

1. `viewBox="0 0 120 120"`, `fill="none"`
2. Pick a unique ID prefix
3. Define frame gradient in `<defs>`
4. Content centered at (60,60), stays within 36–84 zone
5. Outer frame rect at (30,30) 60x60 rx 13
6. Only `currentColor`, no hardcoded colors
7. Strokes 0.6–1.0, fills with low opacity
8. Animations slow and subtle (2–10s), no jarring motion
9. Keep it minimal — fewer elements reads cleaner
