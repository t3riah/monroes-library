# Monroe's Library

A book-catalog PWA for Monroe (age 4): scan barcodes (camera or photo), track a
big library haul, earn badges, fill the Star Jar.

Live: https://t3riah.github.io/monroes-library/
Install (iPhone): open in Safari → Share → Add to Home Screen.

## Design system (UI Component Directive, Oct 2026)

- `tokens.json` — the single source for color (light + bedtime-dark), type,
  radius, spacing and shadow. CSS must use these values; no ad-hoc hex.
- Components in `index.html` follow the shadcn/ui API in vanilla form:
  Button (`.btn`/`.bigbtn`, variants primary/secondary/orange/ghost/danger,
  sizes small/default/lg), Card, Badge, Chip, Input (`.field`), Switch
  (`.pill`), Progress, Sheet, Toast, Alert (`.notice`), Tabs (`.tabbtn`).
- `components.html` — Storybook-style reference page rendering every
  component with the shipped CSS.
- Identity: _her own library branch_ — deep library green, warm cream paper,
  brass bookplates/stamps, Fraunces serif display type. No generic rainbow.

## Files
- `index.html` — the whole app (scanner: ZXing via CDN, storage: localStorage)
- `tokens.json`, `components.html` — design system
