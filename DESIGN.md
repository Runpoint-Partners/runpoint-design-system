# Runpoint design system

Every page is built from the parts below. They are shown live at
`/design-system`. If a page needs something that is not here, add it here
and to that page first, then use it.

The system covers what goes on a page as much as how it looks. Words,
photographs, charts and product screens each have a standard here, the
same as color and type do.

Copy rules are in `VOICE.md`. The record of past page-by-page design
decisions is in `docs/design/change-log.md`.

## Principles

1. The heading and the picture make the point. Body copy is for the reader
   who wants more.
2. One point per section.
3. Everything sits on one grid. Twelve columns; every block starts and ends
   on a column edge, and columns that sit side by side are the same height.
4. A picture shows something real: a quantity, a sequence, a place or the
   actual product. Words in boxes are not a picture, and neither is a
   screen we made up. If it can't be shown, cut it.
5. One orange mark per view. Orange means "look here." It is either the
   emphasized phrase in a headline or one mark in a figure. When a figure
   sits right under a headline, the headline goes without.
6. Things move once, then hold still.

## Where things live

| What | Where |
|---|---|
| Tokens (color, type, space, layout, motion) | `design/tokens.json`, built to `src/app/tokens.css` and `design/index.html` by `npm run design:build` |
| Type, layout, controls, motion | `src/app/system.css` (scoped to `.rp`) |
| Components | `src/components/system/` |
| Product screens and their captures | `src/components/system/screens/`, `public/media/screens/` |
| Device frames | `AppWindow`, `IPhone`, `Canvas` in `src/components/system/` |
| Chart primitives | `src/components/system/figures/` |
| Page layouts shared by every vertical | `src/components/site/` |
| A vertical's words and pictures | `src/content/<vertical>.ts` |
| Product pages (Foundation, Decisions) | `src/components/site/TechnologyPage.tsx`, words in `src/content/technology/` |
| Navigation for every site | `src/components/layout/navigation.ts` |
| Image list and generator | `scripts/site-images.mjs`, `scripts/generate-site-images.mjs` |

A page is on the system when its route is listed in `isSystemPath`
(`navigation.ts`). The site frame then adds the `rp` class. Older pages
keep their old styles until they are moved over.

## Tokens

Every color, font, type size, space step, width and timing is in
`design/tokens.json`. Change a value there and run `npm run design:build`;
it writes `src/app/tokens.css` (the `--rp-*` variables every stylesheet
reads) and `design/index.html` (the visual reference, with an audit of
values that sit outside the tokens). Never type a hex code or a pixel font
size into a page. The older names (`--color-*` in `globals.css`, `--v-*` in
`v4.css`, `--rp-night` and friends in `system.css`) all point at the tokens.

## Color

Paper `#FAFAF7`, mist `#F1F0EC`, night `#161411`, ink `#141311`, body
`#46433D`, muted `#77736B` and international orange `#FF4400`. Retired
colors and their replacements are listed in `design/tokens.json`
(`retired`). No other colors, except inside photographs and product screens.

## Type

Three families, loaded once in `src/app/layout.tsx`. Newsreader sets the
few big statements on a page: the hero, each section's headline, numbers
and client quotes. Hanken Grotesk sets everything else. Geist Mono is for
small uppercase labels only. Rebuilt product screens (`.rp-os`) keep the
product's own fonts so they match the captures.

| Class | Face | Use |
|---|---|---|
| `rp-display` | Serif | Hero and closing headline |
| `rp-title` (`rp-title-sm`) | Serif | Section headline; smaller for sub-sections |
| `rp-stat` | Serif | A real number |
| `rp-quote` | Serif | A client's words |
| `rp-heading` | Sans, semibold | Cards, list items, names |
| `rp-lead` | Sans | One sentence under a headline |
| `rp-body` | Sans | Supporting copy |
| `rp-small` | Sans | Attribution, notes |
| `rp-label` | Sans | The only label style: uppercase, tracked |

Emphasis is `<em>`: italic and orange, at most once per headline. Sizes
are fluid and grow on wide monitors, so do not set font sizes on a page.

System type is unlayered CSS, so Tailwind text utilities cannot override
it. Use the modifiers `rp-strong`, `rp-accent` and `rp-title-sm`.

## Grid and balance

- `Section` is a full-width band (`tone`: paper, cream, night) whose content
  sits on `rp-container`. Every section of every page is one.
- The container is 1240px and grows to 1680px on wide monitors.
- Inside it, `rp-grid` is twelve columns with one gap (`--rp-gap`). Blocks
  take a span with `rp-col-*`. The allowed layouts are 9 (a headline and
  its sentence), 7/5 and 6/6 (words and a picture), 4/8 (words beside a product demo), 4/4/4 (three cards),
  3/3/3/3 (four steps) and 12 (a screen or a wide picture). Blocks stack
  on phones.
- `Split` puts words and a picture side by side at the same height. The
  picture is landscape and grows to match taller text; shorter text pins
  its last block (`SplitFoot`) to the picture's bottom edge. Never put a
  tall picture beside a few lines of text.
- `PhotoPoints` puts a photograph on five columns beside four short points
  on seven, two by two. From 1024px up the photograph takes the points'
  height, so both columns end on the same line. `flip` puts the photograph
  on the right.
- Space between blocks in a section is `--rp-block-y`. Do not invent
  margins.
- `Hero` is the only part that spans the whole window, including the rail.
  On a home page the first scroll lands on the top of the next section,
  then the page scrolls normally (`SmoothScroll`).
- A section may carry a number and name that match the rail's "On this
  page" list (`SectionLabel`). There are no other labels above headlines.
- Corners are square. A phone mockup keeps its rounded body because it
  depicts a real object.
- Add `?grid` to any system page to see the columns. Review every page at
  390, 1440 and 2000 wide, and look at the composition, not only for
  overlaps: squint, and check that each section reads as one shape.

## Controls

- `Button`: one primary per page, labeled "Contact us." On the mechanical
  pages the primary names the first step instead: `FIRST_STEP` in
  `src/content/technology/path.ts` ("Start with two weeks of discovery").
  Secondary is outlined.
- `TextLink`: ink label, orange arrow. `→` for another page, `↓` for
  further down this one.

## Images

Three kinds, and no others:

1. **Documentary photograph**: a specific trade, place or technology.
2. **Paper model**: an abstract idea (a sequence, a choice, a way of
   working).
3. **Product screen**: a real capture, or a faithful rebuild of one
   (below).

Photographs and paper models are generated with
`scripts/generate-site-images.mjs`: GPT Image 2.5 Sunburst, 1536x1024, high
quality, compressed to WebP. The two style prompts are in that script and
are shared by every image; an image adds only its scene, in
`scripts/site-images.mjs`. Every frame has exactly one orange detail and no
text. Look at each output at full size before using it.

Real photographs of our own people and work are always welcome and take
priority over a generated image. Client logos appear in grayscale.

Use `Photo` to place one. Beside text a picture is landscape; portrait
crops are for people and phones. A paper model is shown whole, never
cropped into. No stock video and no third illustration style.

Our own films go in `Film`: 16:9, square corners, the poster until the
reader presses play, with the player's own controls. A film never
autoplays or loops. Beside text it takes the picture's place in a `Split`;
its caption is the film's name, its length and at most one link. No stock
video.

## Product screens

A product screen is the real product, never a drawing of what it might
look like. Invented screens read as mockups: too few rows, no navigation,
tidy made-up data, wrong device proportions.

1. **Start from a capture.** A real screenshot of the real product, saved
   in `public/media/screens/` and listed in
   `src/components/system/screens/sources.ts`. No capture, no screen: use a
   photograph, or ask for a capture.
2. **Show it, or copy it exactly.** Put the capture in a frame (`AppWindow
   src=...`). Rebuild it in code only when it has to move or must stay
   sharp at a large size, and then copy it element for element: same
   labels, same numbers, same rows, same navigation. The rebuild's header
   comment names its capture.
3. **Keep everything that makes it look used.** The sidebar, the toolbar,
   every row and status. Do not simplify a screen to a few tidy rows.
4. **Draw it at its real size.** A rebuild is drawn at the capture's pixel
   size (1440 by 900 for desktop, 393 wide for phone) inside `Canvas`,
   which scales it as one piece. Never restyle a rebuild to fit a space;
   give it a different width.
5. **Use real devices.** Desktop screens sit in `AppWindow` (16:10). Phone
   screens sit in `IPhone`, which has an iPhone's true proportions, status
   bar, Dynamic Island and home indicator. Put a phone beside a window with
   `rp-stage`.
6. **Say when the data is a sample.** In the product's own demo banner, or
   in the caption under the frame.
7. **Crop to make a detail readable.** `CaptureCrop` cuts one region out of
   a capture (in the capture's own pixels) and frames it like a window: a
   comment thread, an approver list, a clock. A crop shows the product
   exactly as captured and never says something the full screen does not.
8. **Let the reader click through.** `ScreenTour` puts up to four real
   captures behind tabs on the 3/3/3/3 grid, with the chosen screen in the
   desktop frame across all twelve columns. Nothing moves until the reader
   picks a tab.
9. **Let the reader use it.** `ProductDemo` is a clickable prototype of
   the real product, built from real captures
   (`scripts/capture-os-demo.mjs` records each screen and measures where
   its buttons are, so every hotspot sits on the real thing). It opens as
   a guided tour: a cursor clicks through, one short caption per step. The
   reader takes over by clicking a highlighted spot, the sidebar, a
   chapter or Back, and can resume the tour. It tilts in space while the
   tour plays and squares up when the reader reaches for it; with reduced
   motion it holds still. Write the tour in `src/content/`; a caption
   names only what the screen shows.

Inside a rebuild, keep the product's own shapes, type sizes and colors
(`rp-os`, `rp-os-serif`, `rp-os-text-serif`, `rp-os-mono`). The page's
square corners and type scale stop at the frame.

When a rebuild is done, screenshot it beside its capture and fix the
differences before using it.

## Charts and motion

- A chart or diagram shows a quantity or a sequence. Before drawing one,
  name what it shows: how much, in what order, or what the real thing
  looks like. If the answer is "a set of names," it is a list. Put the
  names in a sentence or cut them. No grids of labels in boxes.
- `Figure` frames a chart: a title that states the finding, an optional
  one-line subtitle, the marks, and a one-line source.
- One mark is orange. The rest are ink or `--rp-rule-strong`.
- Text inside a chart or screen is at least 12px and stays under 40 words.
- `Play` triggers motion once, when the block is about a third on screen.
  Descendants use `rp-m-fade`, `rp-m-grow`, `rp-m-rise`, `rp-m-pop` and
  `rp-m-draw`, staggered with `delay(ms)`.
- Timing: 160ms for hover and focus, 350ms and 8px for a block arriving
  (`Reveal`), 700ms for a mark being drawn.
- With reduced motion or no JavaScript, everything shows in its final
  state.
- Never: loops, autoplay, play and pause buttons, scroll hijacking beyond
  the opening snap, or text that exists only during an animation.

## Product pages

Foundation, Decisions and Papercuts each have a page built on the pattern
Papercuts set. A vertical site writes them for its own trade
(`/mechanical/foundation`, `/mechanical/decisions`).

1. **Opening.** The product's name beside the wordmark, as the product's own
   header shows it; the promise as the headline; one sentence; one plain
   fact and one link (down to the film, or to the client story). The picture
   is a documentary photograph of the trade with one screen (a capture, a
   `CaptureCrop` or a drawn screen) over the photograph's quiet corner.
   Generate the photograph with that corner empty.
2. **What it does, on the real screens.** A `ScreenTour`, or steps that each
   sit under the crop of the screen that does it.
3. **The number or sequence that proves it.** A `Figure`.
4. **Where it fits.** The four steps of a build (Papercuts, Decisions,
   Foundation, after launch), this product's step in orange.
5. **Its film** when the film speaks to that trade, then the ask.

On a trade site, Foundation is shown as the field service software we build
for contractors, never as Runpoint OS (our own professional services
system). Its screens are in `src/components/system/screens/field/`: drawn
in the style contractors know from ServiceTitan, carrying the contractor's
name and no Runpoint branding, and captioned as illustrative with fictional
data until a client's real screens can be captured.

## Navigation

Four groups in the rail: Work, Technology, Thinking, Company. Second-level
links sit behind a chevron. Contact is the button at the foot of the rail.
A vertical site drops Industries and points its wordmark at its own home.
Each page publishes its sections to the rail with `RailSections`.

## Checks

`npm run check:design` fails on copy over budget, two-beat headlines, em
dashes, page code that sets its own font sizes, widths or colors, and a
product screen that does not name the capture it was rebuilt from.
It cannot judge a composition. Look at the page.

## Adding a vertical

1. Copy `src/content/mechanical.ts` and rewrite it for the industry.
2. Add its images to `scripts/site-images.mjs` and generate them.
3. Add a route that renders `HomePage` with the new content.
4. Add the site to `SITES` in `navigation.ts`.
