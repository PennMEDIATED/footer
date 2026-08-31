# Penn MEDIATED — Global Footer

Hand-coded HTML/CSS for the sitewide footer on the new mediated.upenn.edu
WordPress site. This is a **basic WordPress install — no Divi or other
page builder** — so the footer isn't a builder module; it's a static
snippet that gets installed sitewide by one of a few methods (see
"Installing in WordPress" below).

Footers are mostly static (links, social icons, copyright), so — unlike
the header/nav, which stays wired to WordPress's native menu system for
easy wp-admin editing — this one is fully hardcoded. Updates go through
git, not wp-admin: edit here, commit, push to GitHub, then `git pull` on
the eniac deploy target.

## What's in this repo

- **`index.html`** — the deployable snippet. A single `<style>` block
  (scoped under `.pm-footer`, all custom properties prefixed `--pm-*`)
  followed by the `<footer>` markup. This is the file's entire
  contents you install sitewide, however that ends up happening (see
  "Installing in WordPress" below).
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

No page builder here, so `index.html` doesn't drop into a module —
its contents (style block and all) need to render sitewide via
whichever of these ends up being used. Not settled yet; pick one:

- **`wp_footer` hook** — a few lines of PHP that echo this file's
  contents into the `wp_footer` action, placed in a small mu-plugin
  (`wp-content/mu-plugins/`) or the active child theme's
  `functions.php`. Most durable option: works with any theme, no
  extra plugin, and (if it's an mu-plugin) survives theme switches
  and updates. Slightly more setup than the alternatives below.
- **Code-snippets plugin** (e.g. WPCode, Insert Headers and Footers)
  — paste `index.html`'s contents into a snippet scoped to "footer."
  No PHP file to touch, but adds a plugin dependency.
- **Directly in the theme's `footer.php`** — paste the markup into
  the template. Fastest to wire up, but only safe in a *child*
  theme — a parent-theme update overwrites it otherwise.
- **Footer widget area** — only an option if the current theme
  actually exposes one; many minimal/basic themes don't. If it does,
  a Custom HTML widget there works too.

Whichever is chosen, confirm the "Before you ship" items below, then
preview a real page.

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
needed. Its three `<img>` tags point at live WordPress media-library
URLs (`infodem.upenn.edu/wp-content/uploads/...`), so a local preview
needs network access to load them; if you're offline, or those files
ever move, the local copies in `assets/` are there as a fallback —
swap the `src` values back to `assets/...` temporarily to check layout
without network.

## Updating

This footer is static by design, so most changes are a direct edit to
`index.html`:

```
edit index.html → open it in a browser to check it → commit → push
→ git pull on the eniac deploy target → re-apply wherever this is
installed, if the change needs to reach the live site (see note
below)
```

**Note on deploy:** whether `git pull` alone is enough to go live
depends on which install method gets picked (see "Installing in
WordPress"). If it's the `wp_footer` mu-plugin reading straight from
this repo's checked-out files, `git pull` on eniac *is* the deploy —
nothing else to do. If it's a code-snippets plugin or a hand-pasted
`footer.php`, WordPress is storing its own separate copy (database or
theme file), so `git pull` only updates the *repo* — someone still
has to copy the updated `index.html` into that plugin/template for
the change to actually go live. Worth deciding the install method
partly on this: the mu-plugin option is the only one of the four
where a `git pull` is a real deploy on its own.
