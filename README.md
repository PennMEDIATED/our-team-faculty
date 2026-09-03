# Penn MEDIATED — Faculty

The Faculty page for the [Center on Media, Technology and Democracy](https://infodem.upenn.edu). Static HTML/CSS/JS, no build step. Mirrors the content at https://infodem.upenn.edu/faculty/.

This page **is** the interactive Faculty Research Network diagram — the same tool published at [penn-mediated/Faculty-Research-Network](https://github.com/penn-mediated/Faculty-Research-Network), rebuilt here in the shared Penn MEDIATED style guide (same design tokens, fonts, and brand colors as [`about`](https://github.com/PennMEDIATED/about), [`home`](https://github.com/PennMEDIATED/home), and [`grants`](https://github.com/PennMEDIATED/grants)) so it reads as part of the same site instead of a visually separate tool. Like the source repo, this page is meant to be embedded via iframe in WordPress — it posts its rendered height to the parent frame on load/resize so the iframe can auto-size.

- `index.html` — page markup + the diagram's data/logic (inline `<script>`, unchanged from the source repo except for recolored SVG literals and local photo paths — see below)
- `styles.css` — all styling (design tokens live at the top in `:root`)
- `assets/faculty/` — faculty headshots (source: the Faculty Research Network repo's `Affiliated Faculty Photos/`, renamed to `lowercase-hyphenated.png`)
- `assets/` — Knight Foundation and Penn logos (currently unused on this page — kept for parity with the sibling repos in case a supporters row is added later)

## What this page is

A force-diagram-style visualization of the Center's 25 affiliated faculty (2 Center Co-Directors, 8 Faculty Advisors, 15 Affiliated Faculty), each linked to the "actors" in the information ecosystem their research studies (Governments, Digital News, Social Media, Broadcast TV, LLMs, Elites, the General Public). Hovering or clicking a faculty name in the right-hand panel — or an actor node or arrow in the diagram itself — surfaces that person's photo, bio, and a representative paper in the left column. The page auto-cycles through faculty every 10 seconds until a person interacts with it.

## Porting from the source repo

This page's `<script>` block is a direct port of `penn-mediated/Faculty-Research-Network`'s `index.html`, with three kinds of changes and nothing else:

1. **Recolored SVG literals.** The original hardcoded a navy/gold Penn palette and one orange accent (`rgb(204,85,0)`) for every active/hover state. Those are now brand tokens: the six actor categories each get a distinct hue rooted in the brand palette (`--actor-gov` through `--actor-public`, defined in `styles.css`), and every active/hover state uses a single accent, `--c-red` (`#f03d1f`). The source repo's orange box-shadow "pulse" flash on the paper-detail panel (on selecting a faculty member) was dropped rather than recolored — it read as a glitch against this page's calmer, more restrained hover language, so the panel now just appears/updates without the flash.
2. **Local photo paths.** `PHOTOS` now points at `assets/faculty/*.png` in this repo instead of the source repo's URL-encoded `Affiliated%20Faculty%20Photos/*.png`.
3. **Fonts.** Merriweather / Merriweather Sans → EB Garamond / DM Sans (the shared `--f-serif` / `--f-sans` tokens).

The data (`ACTORS`, `PAPERS`, `WEBSITES`, `BIOS`, `CARD_ORDER`, etc.) and every interaction handler (hover/click locking, the arrow draw-on animation, auto-cycle, mobile scroll-to-bio, the resize-observer that posts height to the parent iframe) are unchanged. **When the source repo's faculty roster, bios, or papers change, port those same data updates here** — this page doesn't fetch from the source repo at runtime, so the two will drift unless someone applies the same edit twice.

### Updating a faculty member

Find their key in `ACTORS`/`PAPERS`/`WEBSITES`/`BIOS`/`PHOTOS`/`CARD_ORDER` inside `index.html`'s `<script>` and edit in place, matching how the source Faculty Research Network repo represents the same person — that keeps the two repos structurally identical and any future re-port straightforward. Add a new headshot to `assets/faculty/` as `lowercase-hyphenated-name.png` (square- or portrait-oriented; it displays at 72×72 with `object-fit: cover; object-position: center top`).

## Style guide (shared across `about`, `home`, `grants`, and `faculty`)

All four repos are static HTML/CSS/JS built off the same design system. If you're adding or editing anything, pull values from here rather than guessing new ones.

### Design tokens (`:root` in `styles.css`)

**Spacing** — Atlassian's 8px scale:

```
--space-025: 2px   --space-100: 8px   --space-300: 24px  --space-600: 48px
--space-050: 4px   --space-150: 12px  --space-400: 32px  --space-800: 64px
--space-075: 6px   --space-200: 16px
```

**Color:**

| Token | Hex | Use |
|---|---|---|
| `--c-dark` | `#0d0d0c` | Primary text, dark UI chrome |
| `--c-accent` | `#5533ee` | Brand purple — `#hdr h1` (page heading), plus implicitly via `--actor-gov` |
| `--c-red` | `#f03d1f` | The tool's single active/hover accent |
| `--c-gray` | `#888680` | Secondary/muted text; also `--actor-public` |
| `--c-gray-dark` | `#54534f` | Body copy, bios, descriptions |
| `--c-light-bg` | `#f8f7f4` | Panel backgrounds, the diagram canvas |
| `--c-white` | `#ffffff` | Card/panel surfaces |
| `--c-border` | `rgba(13,13,12,0.08)` | Shared hairline, matches every sibling repo |
| `--c-pale-red` | `#fce4dc` | Active-card and callout-box tint — same value as `grants`' `--c-pale-orange` |

**Type:** `--f-serif` (`'EB Garamond', Georgia, 'Times New Roman', serif`) for headings, bios, and paper titles; `--f-sans` (`'DM Sans', system-ui, -apple-system, sans-serif`) for everything else.

**Actor palette:** six categorical colors rooted in the brand's purple/red pair plus enough distinct hues for a data-vis with more categories than the two-color brand system provides on its own — see `--actor-*` tokens. If a new actor category is ever added, pick a hue that's visually distinct from all existing ones at a glance, not just numerically different.

### A note on this page's layout

Unlike `about`/`home`/`grants`, this page is a fixed-viewport 3-column app (`html, body { height: 100%; overflow: hidden; }`), not a scrolling editorial page — it has no nav bar, no footer, and no shared newsletter/supporters block, matching the actual page currently live at infodem.upenn.edu/faculty/ (which is this diagram, embedded via iframe, with WordPress supplying the surrounding chrome). Below 820px it becomes a normal scrolling page (diagram → faculty panel → bio/detail). Don't backport the fixed-viewport pattern to the other repos or the shared newsletter pattern to this one — they solve different problems.

### Typography

This repo is a documented exception to the sitewide type scale.

The `--fs-*`/`--lh-*` tokens are declared in `:root`. Two regions use them:

- **The page header.** `#hdr h1` is `--fs-h3` (20-24px fluid) and `#hdr p.hdr-desc` is `--fs-small`. That one clamp replaced the two breakpoint step-downs the header used to carry.
- **The left column's two detail boxes** (`#faculty-bio`, `#paper-detail`). Both scroll — `#left-col` is `overflow-y: auto` and `#paper-detail` has its own — so unlike the rest of the app body, type here has somewhere to go. Their headers stay EB Garamond (`#fb-name`, `#pd-title`, both `--fs-lede`/`--lh-title`); their running copy is DM Sans at `--fs-small`/`--lh-body` (`#fb-bio`, `#pd-desc`), which is the sitewide serif-for-headings, sans-for-prose rule. `#fb-school` and `#fb-website` moved from 12px/11px to `--fs-micro`, clearing the 12px floor. The narrow-viewport `#fb-bio`/`#pd-desc` step-downs were dropped — `--fs-small` is already that size.

Everything else below the header keeps its own sizes, deliberately:

- **The app body is a fixed viewport.** `html, body { height: 100%; overflow: hidden }` and `#main` is `overflow: hidden` too, so type that grows has nowhere to go — it clips rather than pushing the page down. The dense card and panel sizes (down to 9px on `.a-tag` and `.a-faculty-label`) are calibrated to columns that cannot grow. The left column is the exception, and is on the scale — see above.
- **The SVG diagram sizes its text with presentation attributes**, not CSS — `font-size="9.5"` and `font-size="11"` on `<text>` nodes placed at hardcoded coordinates. Those are user-space units inside a scaled `viewBox`, so they are not CSS pixels on screen and the 12px floor does not apply. Enlarging them would push labels out of the shapes they sit in.
- **This page is a fork** of [`penn-mediated/Faculty-Research-Network`](https://github.com/penn-mediated/Faculty-Research-Network). Every divergence from upstream is listed above; a wholesale type rewrite would add a large one and make future merges painful.

The two families (`--f-serif` / `--f-sans`) and the `--space-*` scale **do** apply here in full, as does the no-monospace rule.

**One CSS gotcha if you touch `#svg-wrap`'s sizing rule:** it must be `#svg-wrap > svg` (direct child), never `#svg-wrap svg` (descendant). The diagram nests small icon `<svg>` elements many levels deep inside `<g>`s for each actor and crowd figure; a descendant selector stretches every one of those to `width: 100%` too, blowing the icons up to fill the whole diagram.

## Images and video

This applies to every image, GIF and video added to any Penn MEDIATED repo. It is written to be followed directly — by a person or by a Claude session — without further instruction.

### The one rule that is never optional

**Every `<img>` and `<video>` carries explicit `width` and `height` attributes, holding the file's real intrinsic pixel dimensions.**

```html
<img src="assets/example.webp" width="640" height="334" alt="…">
```

They do not set the display size — CSS does. They give the browser the aspect ratio *before* the file downloads, so it reserves a correctly shaped box instead of collapsing to nothing and shoving everything below it down the page as each file lands. That shift is measured by search engines (Cumulative Layout Shift) and is worse for a reader, who loses their place or clicks a link that just moved.

Every repo has a global `img, video { max-width: 100%; height: auto; display: block; }` reset, so the CSS keeps winning and the attributes only ever contribute the ratio. **Never guess the numbers** — read them off the file.

### Pick the format by what the file is

| Content | Format | Never use |
| --- | --- | --- |
| Photo, screenshot, artwork | **WebP**, quality 88 | PNG or JPEG at full camera resolution |
| Logo, wordmark, icon | **SVG** if you have it, else WebP | — |
| Anything that moves | **MP4** (H.264) + a WebP poster | **GIF, ever** |

GIF is the big one. It has no interframe compression, so a screen recording is roughly ten times the size it needs to be: `research-compendium.gif` was 11.3MB for 290 frames; the identical recording as H.264 is 1.2MB.

### Size it to the box it displays in, not to what you were sent

Find the CSS box the image renders into, then export at **2×** that width for retina. Anything beyond that is bytes the browser downloads and immediately throws away. (`gni-membership.png` was 7992px wide, rendering into a 319px box — a 470KB file doing a 33KB job.)

In this repo:

| Where | CSS box at 1440px | Export at |
| --- | --- | --- |
| Faculty portrait (`#fb-photo`) | 72px square, cropped | ~144px square |

`#fb-photo` has an empty `src` filled in by JavaScript per faculty member, so it carries no `width`/`height` attributes — its box is already reserved by a fixed 72×72 CSS rule. Portraits live in `assets/faculty/`.

If you are adding an image somewhere not listed, measure the box first (`getBoundingClientRect().width` in the browser, at a 1440px viewport) and double it.

### Commands

Stills — resize and convert in one pass:

```python
from PIL import Image
TARGET = 640                      # 2x the CSS box
im = Image.open('source.png')
w, h = im.size
if w > TARGET:
    im = im.resize((TARGET, round(h * TARGET / w)), Image.LANCZOS)
im.save('out.webp', quality=88, method=6)
print(im.size)                    # <- these are the width/height attributes
```

Animation — MP4 plus a poster frame:

```bash
ffmpeg -i source.gif -movflags +faststart -pix_fmt yuv420p \
       -vf "scale=1280:-2:flags=lanczos" -crf 24 out.mp4
ffmpeg -i source.gif -frames:v 1 -vf "scale=1280:-2:flags=lanczos" poster.png
python3 -c "from PIL import Image; Image.open('poster.png').convert('RGB').save('out-poster.webp', quality=80, method=6)"
ffprobe -v error -show_entries stream=width,height -of default=nw=1 out.mp4
```

`-crf 24` is a good default; raise it toward 30 for a smaller file, lower it toward 20 for a sharper one. `-pix_fmt yuv420p` is required for Safari and iOS.

### Markup for video

```html
<video src="assets/name.mp4" poster="assets/name-poster.webp" width="1280" height="622"
       autoplay muted loop playsinline preload="metadata" aria-label="…"></video>
```

Each attribute earns its place: `muted` is what permits autoplay at all, `playsinline` stops iOS opening it fullscreen, `poster` means the slot is never empty while the video loads, and `aria-label` replaces `alt` (a `<video>` has no `alt`).

CSS cannot stop autoplay, so **a page with video needs the reduced-motion script** at the end of `<body>`. If the page already has one, leave it alone; if you are adding the first video to a page, add it:

```html
<script>
  if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
    document.querySelectorAll('video[autoplay]').forEach(function (v) {
      v.autoplay = false; v.pause(); v.currentTime = 0; v.removeAttribute('loop');
    });
  }
</script>
```

Also check the CSS: any rule that sizes or crops an image needs to name `video` too, or the video slot will not match the image slot it replaced (`.card__image img` becomes `.card__image img, .card__image video`).

### Before you call it done

- [ ] File is WebP, SVG or MP4 — no GIF, no full-resolution PNG or JPEG
- [ ] Its width is about 2× the CSS box it renders into
- [ ] `width`/`height` attributes match the file's real dimensions
- [ ] Real `alt` text (or `aria-label` on a video) that describes the image; empty `alt=""` only if it is purely decorative
- [ ] Lives in this repo's `assets/`, not hotlinked from another site
- [ ] Page opened in a browser at 1440px and ~400px — nothing overflows, nothing jumps on load
- [ ] Originals are not committed alongside the optimised file; git history is the backup

Do not commit an unoptimised original "just in case" — the previous commit already holds it, and a duplicate in the working tree also ships to the server.

## Hyperlinks

One taxonomy, five categories, shared by every page repo. Pick the category by what the link *is*, not by which repo you happen to be editing.

**1. In-text links** — embedded mid-sentence in flowing prose.

| ground | text | underline | hover |
| --- | --- | --- | --- |
| white / light | `--c-red-dark` | none | fade to `opacity: 0.7` |
| colour / gradient | `--c-white` | `border-bottom: 1px solid rgba(255, 255, 255, 0.5)` | fade to `opacity: 0.7` |

Both grounds use `font-weight: 500` and `transition: opacity 0.15s`, and both fade rather than change hue. On a white ground **colour is the affordance** — no underline; the underline is category 2's job. On a coloured ground the red is invisible, so the link goes white and takes the hairline rule instead. Where an underline is used it is a `border-bottom`, never `text-decoration`.

#### Why interactive red is `--c-red-dark`, not `--c-red`

`--c-red-dark` (`#df3611`) is the closing stop of `--c-gradient`, promoted to a token of its own and declared in all twelve repos.

`--c-red` (`#f03d1f`) measures roughly **3.9:1** against white — under the 4.5:1 WCAG AA threshold for body text, and the same 3.9:1 applies to white text sitting on a `--c-red` fill. `--c-red-dark` measures about **4.5:1** either way and clears it. The two are near-indistinguishable at text sizes, so this is a contrast fix, not a visual change.

**The rule: anything you click is `--c-red-dark`.** Links and buttons take it wherever they would otherwise be red-orange — as text colour, as a box fill, as a hover or active state, and on the markers inside them (disclosure chevrons and their labels). It applies in every category and every state.

**`--c-red` stays the brand accent for everything you don't click**: section headings, eyebrow and metadata labels, tag and pill backgrounds, accent bars and card borders, full-width colour bands, the `.card-arrow` hover gradient, and focus rings. These are either large text, non-text UI at the 3:1 threshold, or sit on a tinted rather than white ground.

The one deliberate hold-out is red link text on a **dark** ground (`home`'s `.footer__email`), where the darker red would *reduce* contrast rather than improve it. That link has a separate outstanding issue — on a dark ground the standard is white text with an opacity fade, not red at all.

**2. Independent links** — a standalone text link that isn't inside a sentence ("Learn More About the Center", "Download the Full Schedule"). Unlike category 1 these carry the underline and are set in the body colour, so they read as a control rather than as emphasis inside a sentence:

| ground | text | underline | hover |
| --- | --- | --- | --- |
| white / light | `--c-dark`, `font-weight: 600` | `border-bottom: 1px solid rgba(13, 13, 12, 0.35)` | text and underline both turn `--c-red-dark` (`transition: color 0.15s, border-color 0.15s`) |
| colour / gradient | `--c-white`, `font-weight: 600` | `border-bottom: 1px solid rgba(255, 255, 255, 0.5)` | fade to `opacity: 0.7` |

Plus a **thin arrow** `⟶` after the text. Use `⟶` (`&#10230;`), not the `↗` badge from category 4.

**3. Document buttons** — an independent link that opens a document (a PDF, a report). A filled button box, not text:

| ground | box | text |
| --- | --- | --- |
| white / light | `--c-red-dark` | `--c-white` |
| colour / gradient | `--c-white` | `--c-dark` |

Hover is **movement, not colour** — a lift or nudge. Do not darken or recolour the box.

**4. Links to another web page** — this site or an external one. The containing box carries the shared `.card-arrow`: a 26px dark circle with a white `↗`, in the box's top corner. On hover the arrow scales slightly and its background becomes a sliding purple-to-orange gradient (`@keyframes card-arrow-slide`), and the box itself animates. No separate text button — the whole box is the link.

**Exception:** a link to a research paper is category 2, not this — thin arrow, no badge.

**5. Hyperlinked headings** — a heading that is itself a link (a post title, a card title). Sits in the body colour and shifts to `--c-red-dark` on hover (or fades, on a coloured ground), with **no arrow and no underline**.

### Dropdowns and disclosures

A dropdown, `<details>` block or expand/collapse control uses one affordance sitewide: a **chevron SVG** (`M2 5l5 5 5-5`, 13×13, `--c-red-dark` stroke, `stroke-width: 1.8`) beside a `--c-red-dark` label at `--fs-small`, rotating `180deg` on open with `transition: transform 0.25s`. See `llm-civic-discourse`'s "Full summary & details" toggle for the reference implementation.

Never leave the marker to the browser — style `<select>` with `appearance: none` and supply the chevron, and hide the native `<summary>` marker. The `↗` circle badge is category 4's language and does not belong on a disclosure control.

**This page is now mostly in line, by coincidence rather than by migration.** It is a fixed-viewport app forked from `penn-mediated/Faculty-Research-Network`, and its link treatments predate this taxonomy — but the red/no-underline/opacity-fade form they were already using is what category 1 became. `#fb-bio a`, `#pd-desc a` and `.card-desc a` match category 1; `#pd-read-btn` matches category 2, arrow included.

One outstanding discrepancy: **`#fb-website`** ("Faculty Website" beside the name) is a category-2 independent link carrying the right colour, weight and underline but **no `⟶` arrow**. Fix that deliberately, alongside whatever else this page needs — don't half-migrate it.

### Keeping the repos in sync

`about`, `home`, `grants`, and `faculty` are separate repos with duplicated CSS, not a shared stylesheet — so consistency is a discipline, not something enforced automatically. When you change a shared token in one repo, check whether the same change belongs in the others before considering the task done. Separately, this repo also needs to stay in sync with `penn-mediated/Faculty-Research-Network` on faculty content (see "Porting from the source repo" above) — two different sync relationships, both manual.
