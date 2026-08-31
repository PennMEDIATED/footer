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
