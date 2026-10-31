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

## UX (Apple Music-inspired, Oct 2026)
- 5-tab bar: Read Now · Browse · Search · Library · Badges. Grown-ups lives
  behind the gear button in the header (with a Back to Read Now button).
- Read Now: large title, Now Reading hero (big artwork, Hear + I Read It),
  Recently Added + Favorites horizontal shelves, stats, Star Jar, Next Badge.
- Browse: shelves from the library itself — New This Week, Made for Monroe
  (favorites), Read Again, Badges to Earn (tap a badge card to jump to Badges).
- Search: search Monroe's own library, plus Scan, ISBN lookup, Find by Name,
  and manual add — everything for finding or adding a book in one place.
- Library: the full book grid with the existing All / This Week / Favorites /
  Read filters and sort.
- Now Reading bar: a persistent mini-player above the tab bar for the current
  book (newest, or the last one opened) with Hear + I Read It; tapping it opens
  the book. It hides when the library is empty and survives reloads via
  settings.currentBook in the existing settings store — book data format
  unchanged.
