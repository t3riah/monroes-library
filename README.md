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

## Card uniformity — universal method (Nov 4, 2026)
Card size must depend only on column width, never on content. The bug was
CSS automatic minimums: a grid track of `1fr` means `minmax(auto, 1fr)`,
and a grid/flex item's `min-width: auto` means its content's min size, so
a very long author name (nowrap) or one long unbroken title word forced
its whole column wider, and an empty author line collapsed a card shorter.
The fix, applied to book cards and shelf cards alike:
- tracks are `minmax(0, 1fr)`, never bare `1fr`
- `min-width: 0` on the card and on its cover, title, and author lines
- titles break long words (`overflow-wrap: anywhere`) inside the 2-line clamp
- the author line has a fixed line-height and min-height, so an empty
  author still reserves one line
- the cover slot is always a fixed 2:3 box (real cover, placeholder, or
  the broken-cover fallback)
Verified by measuring every card's box at phone width with hostile data
(huge cover, broken cover, missing cover, 40-letter word, 50-character
author, no author): all 8 cards exactly 113x240, covers 93x140.

## Cover images — uniform and centered (Nov 4, 2026)
One render path, `coverHTML`, serves every cover in the app (grid, shelves,
hero, Now Reading bar, search results, detail, confirm dialog). The
guarantee is three rules, all declared in CSS, not left to defaults:
- a fixed 2:3 slot at every size (grid 93x140, shelf 132px wide, hero
  128px, bar 34x51, detail 130px, result 46x68)
- `object-fit: cover` plus an explicit `object-position: center`, so any
  source shape (portrait, square, landscape) is cropped equally from
  both sides around its center
- a broken or missing cover never leaves a hole: the image hides and the
  same centered book-icon placeholder takes its slot, at the same size
Proven with stripe test images (gold stripe dead center of each source):
the landscape stripe lands dead center in the detail slot, and a broken
cover shows the centered placeholder at full slot size. The one thing
this does not do: it crops rather than letterboxes, so the extreme edges
of a wide source are cut. Whole-cover display would need `contain` on a
uniform backdrop instead, which is a look change, not a fix.

## Hoopla / Libby method, adopted Nov 4, 2026 (supersedes crop above)
Firsthand public-grid inspection (no sign-in) of hoopladigital.com and
the NYPL OverDrive catalog, the web catalog behind Libby:
- Hoopla: white rounded card, portrait ~2:3 cover, title bold + author
  grey below. Non-portrait art is NOT cropped: the whole image shows
  centered, and the rest of the frame is filled with a blurred, enlarged
  copy of the same cover. One standard thumbnail size everywhere
  (`cover.hoopladigital.com/{id}_270.jpg`).
- OverDrive/Libby web: white square-cornered card, portrait 3:4 cover
  box (220x293), fill-and-crop, so edges are cut (an author name was
  seen clipped at the edge). Size token in the URL path
  (`ImageType-100` grid, `ImageType-400` detail). Long titles truncate
  with an ellipsis; Hoopla wraps them.
- Missing-cover placeholder: not verifiable on either service without
  sign-in; every title encountered had publisher-supplied art.
Adopted for Monroe's Library (Hoopla's side of the contrast, since a
4-year-old recognizes a book by its whole cover):
- every cover now renders as a slot: blurred enlarged copy behind,
  whole cover centered on top (`contain`, never cropped)
- grid/shelf/hero/bar/result slots use a size-matched thumbnail:
  Open Library `-L.jpg` is served as `-M.jpg` at display time only
  (stored data still `-L`); detail and confirm keep full `-L`
- titles still wrap to 2 lines (Hoopla-style), not truncated
- our centered book-icon placeholder stays, since neither service
  offered a verifiable placeholder to copy
Verified Nov 4: all 8 hostile cards still exactly 113x240 with 93x140
slots; stripe images show whole art centered with blurred bands;
broken cover falls back to the centered placeholder; suite 0 failures;
scanner block unchanged.

## Cover system — every book has an image (Nov 4, 2026)
- A book with no cover art gets a made cover at first paint: canvas-drawn,
  on-brand (cream paper, library green frame, brass rule, M medallion),
  title wrapped and auto-fit, author below, palette picked by title hash
  so each book looks distinct and stable. No stored data changes; the art
  is generated from title and author on the device.
- Real art is still preferred: on load (visible tab only), books without
  art are checked once (retried after 30 days, or anytime from the
  Grown-ups Cover check panel) against Open Library by ISBN, then Open
  Library search, then Google Books. A candidate only counts if it loads
  and its short side is at least 120px. Found art is saved to the book.
- Refresh Cover in detail uses the same validated check instead of
  blindly setting an ISBN URL. New single adds without art try the same
  check quietly.
- The Grown-ups Cover check panel shows real/made/total counts, lists
  every made-cover book, and re-runs the check on tap.
- If the device has no canvas, or is offline, the book-icon placeholder
  is the fallback, at full slot size.
Verified Nov 4 end to end in a real browser: a known-ISBN coverless book
gained and saved its real Open Library cover; an unfindable book kept a
made cover; suite 0 failures; scanner block unchanged.

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
