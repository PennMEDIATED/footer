# Penn MEDIATED — Global Footer

Hand-coded HTML/CSS for the sitewide footer on the new mediated.upenn.edu
WordPress site. Per the migration plan, this is installed as a **Divi
Theme Builder Code module**, assigned globally, replacing Divi's default
drag-and-drop footer everywhere on the site.

Footers are mostly static (links, social icons, copyright), so — unlike
the header/nav, which stays wired to WordPress's native menu system for
easy wp-admin editing — this one is fully hardcoded. Updates go through
git, not wp-admin: edit here, commit, push to GitHub, then `git pull` on
the eniac deploy target.

## What's in this repo

- **`index.html`** — the deployable snippet. A single `<style>` block
  (scoped under `.pm-footer`, all custom properties prefixed `--pm-*`)
  followed by the `<footer>` markup. This is the file you paste into
  the Divi Code module, in full.
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

## Installing in Divi

1. **Theme Builder → Global (Default Website Template) → Footer area
   → add a Code module.**
2. Paste the **entire contents of `index.html`** into that module
   (style block and all — Divi Code modules render raw HTML as-is).
3. Assign the template scope so it applies sitewide, the same way
   Divi's old default footer was scoped.
4. Confirm the "Before you ship" items below, then preview a real
   page.

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
→ git pull on the eniac deploy target → re-paste into the Divi Code
module if the change needs to reach the live module (Divi doesn't
read this repo directly — see note below)
```

**Note on deploy:** Divi's Code module stores whatever HTML was pasted
into it inside the WordPress database, not a file on disk — `git pull`
updates the *repo* on eniac, but someone still has to copy the updated
`index.html` into the Theme Builder module in wp-admin for a change
to actually go live. If that becomes a frequent enough workflow to be
annoying, the phased plan's "future upgrade path" (push-to-deploy via
a `post-receive` hook, or a small mu-plugin that reads this file
directly instead of a hand-pasted Code module) is worth revisiting.
