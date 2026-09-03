# Penn MEDIATED — Global Footer

Hand-coded HTML/CSS for the sitewide footer on the new mediated.upenn.edu
WordPress site — built as a plain `index.html` + `styles.css` pair, the
same direct-to-disk convention the other PennMEDIATED page repos use
(`home`, `about`, `grants`, `data`, `team-leadership`), not a Divi/page
builder module. See "Installing in WordPress" below for how this
actually reaches the live site.

Footers are mostly static (links, social icons, copyright), so — unlike
the header/nav, which stays wired to WordPress's native menu system for
easy wp-admin editing — this one is fully hardcoded. Updates go through
git, not wp-admin: edit here, commit, push to GitHub, then `git pull` on
the eniac deploy target.

## What's in this repo

- **`index.html`** — the `<footer>` markup, linking `styles.css`.
- **`styles.css`** — all of the footer's CSS (scoped under
  `.pm-footer`, all custom properties prefixed `--pm-*`). Split out
  from `index.html` to match the convention the other PennMEDIATED
  page repos (`home`, `about`, `grants`, `data`, `team-leadership`)
  already use — an `index.html` + `styles.css` pair, not one file
  with an inline `<style>` block.
- **`assets/`** — reference/backup copies of the three logo images
  (`knight-foundation-logo.png`, `upenn-logo-full.png`,
  `mediated-white-transparent.svg`). `index.html` itself doesn't
  read from this folder — its `<img>` tags point directly at the real
  WordPress media-library URLs (all three images are already uploaded
  and live). Keep this folder in sync if any of the three images gets
  re-exported or re-uploaded to a new URL.

## Design system

Colors, type, spacing, and the brand gradient are pulled from the style
guide documented in the `PennMEDIATED/home` repo's README (shared with
`about`) so this footer stays visually consistent with the rest of the
site and with whatever the header/nav work lands on. Summary:

| Token | Value |
|---|---|
| Brand gradient | `linear-gradient(150deg, #5533ee 0%, #df3611 81%)` |
| Dark / footer bg | `#0d0d0c` |
| Accent purple | `#5533ee` |
| Accent red | `#f03d1f` |
| Serif (headlines) | `EB Garamond` |
| Sans (everything else) | `DM Sans` |
| Page max-width | `1440px`, 40px side padding (24px < 900px, 16px < 480px) |

If a token changes in `home`'s style guide, update it here too — these
are duplicated, not shared, across repos (same discipline `home` and
`about` already use with each other).

## Installing in WordPress

No Divi/page builder — confirmed as of now, the site is plain
WordPress, so this repo has one delivery destination, same mechanism
as every other PennMEDIATED page repo (`about`, `grants`, `data`,
`team-leadership`, `home`):

A GitHub webhook triggers `git pull` on eniac, cloned into place at
the path the static pages are served from. Each static page pulls
this footer in via an Apache Server-Side Include
(`<!--#include virtual="..." -->`), the same way they already
include the shared nav. Automated once wired up — a `git pull` here
*is* the deploy, no manual copy-paste step after.

Confirm the "Before you ship" items below, then preview a real page.

## Before you ship

A few things are placeholders because they depend on the live
WordPress instance or on decisions outside this repo — search
`index.html` for these markers before installing:

- **Nav link slugs** — every real link in the three nav columns is
  confirmed live on infodem.upenn.edu today (`/research-compendium/`,
  `/llm-civic-discourse/`, `/grants/`, `/about/`, `/team/`,
  `/faculty/`, `/job-openings/`, `/data/`, `/events/`). These are
  today's infodem.upenn.edu paths — reconfirm against
  mediated.upenn.edu once that site is live, in case anything gets
  renamed in the move.
- **Two pending nav items** — "Democracy & Computational Narratives"
  (Research) and "Grants Request for Proposals"
  (Resources) don't have pages yet, so they're rendered as plain
  `<span class="pm-footer__nav-pending">` text instead of dead links.
  Once each page exists, swap its `<span>` for an `<a href="...">` in
  `index.html` (same list markup, just change the tag) — search for
  `pm-footer__nav-pending` to find both.
- **Social links** — all four (YouTube, Bluesky, GitHub, LinkedIn) are
  confirmed handles/URLs.

## Previewing locally

`index.html` opens directly in a browser — no build step, no server
needed — and picks up `styles.css` automatically as long as the two
files stay in the same folder. Its three `<img>` tags point at live
WordPress media-library URLs (`infodem.upenn.edu/wp-content/uploads/...`),
so a local preview needs network access to load them; if you're
offline, or those files ever move, the local copies in `assets/` are
there as a fallback — swap the `src` values back to `assets/...`
temporarily to check layout without network.

## Updating

This footer is static by design, so most changes are a direct edit to
`index.html` and/or `styles.css`:

```
edit index.html / styles.css → open index.html in a browser to check
it → commit → push → git pull on the eniac deploy target
```

**Note on deploy:** `git pull` on eniac *is* the deploy — nothing else
to do, same as `about`/`grants`/etc. No separate copy living anywhere
else to keep in sync.

## Embedding this footer

Unlike the page repos, **this one is not embedded with an iframe.** It is a body fragment (no `<html>`, `<head>` or `<body>`), designed to be pasted into a Divi Theme Builder Code module so it renders as part of the WordPress page rather than inside a frame of its own. Paste `styles.css` and then `index.html` into that module.

`https://pennmediated.github.io/footer/` exists so you can preview the fragment in a browser; it is not an embed target. For the same reason this repo carries no auto-resize script — there is no parent frame to report a height to.


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
| Brand lockup (`.pm-footer__logo-img`) | ~340px wide | SVG (currently `mediated-white-transparent.svg`) |
| Supporter logo (`--knight`, `--upenn`) | 30px tall, width follows the ratio | ~2× the rendered width |

This footer ships into WordPress as a Divi Code module, so its `<img src>` values point at WordPress media-library URLs rather than at `assets/`. `assets/` holds reference copies. If you replace a logo, upload it to WordPress **and** update the copy here, and put the new dimensions on the tag.

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

The footer's tokens are `--pm-*` namespaced because it is injected into Divi, so the colour values above are `--pm-c-red` / `--pm-c-white` / `--pm-c-dark` here. The categories themselves are unchanged.
