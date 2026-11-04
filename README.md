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

## Rebuild (Nov 2026)
- Same look, same scanner, same saved library. The camera scanner block is
  byte-identical to the pre-rebuild version, and the two original stores
  (`monroes_library_books_v1`, `monroes_library_settings_v1`) and the
  book-object format are unchanged, so existing phones keep everything.
- Premise (Louie, Nov 4, 2026): every book in the app has been read by
  Monroe, so the app carries NO read/unread distinction and NO reading
  streaks. Removed in full: "I Read It" buttons (hero, detail, Now Reading
  bar), the I Read It stat and filter, the Read Again shelf, the read
  checkmarks on cards, the read column/marker in exports, and the read
  log. The old `read` flag still sits inert in saved book objects for
  import compatibility, but nothing reads or shows it.
- Badges went from 45 to 37: the 5 Books Read badges and the 3 day-streak
  badges are gone (they measured the removed concepts); "4 Library Weeks"
  stays under a renamed Library Weeks category, since it measures weeks
  with books added, not reading streaks. All remaining badge ids and
  thresholds are unchanged.
- New: tappable stat cards (My Books / This Week / Favorites jump to the
  Library with that filter; Authors jumps to the Library sorted by
  author), "Pick a Book for Me" on Read Now (any book, no unread
  preference), a "Books I Want" list (Search + Browse; a scanned-in match
  leaves the list by itself), "Refresh Cover" in book detail (Open
  Library cover by ISBN), visible keyboard focus outlines, and a
  Favorite toggle on the Now Reading hero.
- Want list lives in a new key `monroes_library_want_v1`. Backup (.json)
  is version 2 and includes the want list; version 1 backups still import
  as before. The want list survives clearing the library.

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
