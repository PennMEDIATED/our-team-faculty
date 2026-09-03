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

The `--fs-*`/`--lh-*` tokens are declared in `:root` and **only the page header uses them**: `#hdr h1` is `--fs-h3` (20-24px fluid) and `#hdr p.hdr-desc` is `--fs-small`. That one clamp replaced the two breakpoint step-downs the header used to carry.

Everything below the header keeps its own sizes, deliberately:

- **The app body is a fixed viewport.** `html, body { height: 100%; overflow: hidden }` and `#main` is `overflow: hidden` too, so type that grows has nowhere to go — it clips rather than pushing the page down. The dense card and panel sizes (down to 9px on `.a-tag` and `.a-faculty-label`) are calibrated to columns that cannot grow.
- **The SVG diagram sizes its text with presentation attributes**, not CSS — `font-size="9.5"` and `font-size="11"` on `<text>` nodes placed at hardcoded coordinates. Those are user-space units inside a scaled `viewBox`, so they are not CSS pixels on screen and the 12px floor does not apply. Enlarging them would push labels out of the shapes they sit in.
- **This page is a fork** of [`penn-mediated/Faculty-Research-Network`](https://github.com/penn-mediated/Faculty-Research-Network). Every divergence from upstream is listed above; a wholesale type rewrite would add a large one and make future merges painful.

The two families (`--f-serif` / `--f-sans`) and the `--space-*` scale **do** apply here in full, as does the no-monospace rule.

**One CSS gotcha if you touch `#svg-wrap`'s sizing rule:** it must be `#svg-wrap > svg` (direct child), never `#svg-wrap svg` (descendant). The diagram nests small icon `<svg>` elements many levels deep inside `<g>`s for each actor and crowd figure; a descendant selector stretches every one of those to `width: 100%` too, blowing the icons up to fill the whole diagram.

## Hyperlinks

One taxonomy, five categories, shared by every page repo. Pick the category by what the link *is*, not by which repo you happen to be editing.

**1. In-text links** — embedded mid-sentence in flowing prose.

| ground | text | underline | hover |
| --- | --- | --- | --- |
| white / light | `--c-dark` | `border-bottom: 1px solid rgba(13, 13, 12, 0.35)` | text and underline both turn `--c-red` |
| colour / gradient | `--c-white` | `border-bottom: 1px solid rgba(255, 255, 255, 0.5)` | fade to `opacity: 0.7` — no colour swap |

The underline is a `border-bottom`, not `text-decoration`, so its colour can be transitioned independently of the text on hover. Pair it with `transition: color 0.15s, border-color 0.15s` on light grounds and `transition: opacity 0.15s` on coloured ones.

White-to-anything reads poorly on a saturated ground, which is why the coloured case fades instead of changing hue.

**2. Independent links** — a standalone text link that isn't inside a sentence ("Learn More About the Center", "Download the Full Schedule"). Same colours, decoration and hover as category 1, **plus a thin arrow** `⟶` after the text. Use `⟶` (`&#10230;`), not the `↗` badge from category 4.

**3. Document buttons** — an independent link that opens a document (a PDF, a report). A filled button box, not text:

| ground | box | text |
| --- | --- | --- |
| white / light | `--c-red` | `--c-white` |
| colour / gradient | `--c-white` | `--c-dark` |

Hover is **movement, not colour** — a lift or nudge. Do not darken or recolour the box.

**4. Links to another web page** — this site or an external one. The containing box carries the shared `.card-arrow`: a 26px dark circle with a white `↗`, in the box's top corner. On hover the arrow scales slightly and its background becomes a sliding purple-to-orange gradient (`@keyframes card-arrow-slide`), and the box itself animates. No separate text button — the whole box is the link.

**Exception:** a link to a research paper is category 2, not this — thin arrow, no badge.

**5. Hyperlinked headings** — a heading that is itself a link (a post title, a card title). Colour shift on hover per the ground rules above, and **no arrow and no underline**.

### Dropdowns and disclosures

A dropdown, `<details>` block or expand/collapse control uses one affordance sitewide: a **chevron SVG** (`M2 5l5 5 5-5`, 13×13, `--c-red` stroke, `stroke-width: 1.8`) beside a `--c-red` label at `--fs-small`, rotating `180deg` on open with `transition: transform 0.25s`. See `llm-civic-discourse`'s "Full summary & details" toggle for the reference implementation.

Never leave the marker to the browser — style `<select>` with `appearance: none` and supply the chevron, and hide the native `<summary>` marker. The `↗` circle badge is category 4's language and does not belong on a disclosure control.

**This page does not currently follow the standard above.** It is a fixed-viewport app forked from `penn-mediated/Faculty-Research-Network`, and its link treatments (`#fb-bio a`, `#pd-desc a`, `#pd-read-btn`) predate this taxonomy. Bring it in line deliberately, as its own piece of work — don't half-migrate it.

### Keeping the repos in sync

`about`, `home`, `grants`, and `faculty` are separate repos with duplicated CSS, not a shared stylesheet — so consistency is a discipline, not something enforced automatically. When you change a shared token in one repo, check whether the same change belongs in the others before considering the task done. Separately, this repo also needs to stay in sync with `penn-mediated/Faculty-Research-Network` on faculty content (see "Porting from the source repo" above) — two different sync relationships, both manual.
