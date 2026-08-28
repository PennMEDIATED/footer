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

- **`footer.html`** — the deployable snippet. A single `<style>` block
  (scoped under `.pm-footer`, all custom properties prefixed `--pm-*`)
  followed by the `<footer>` markup. This is the file you paste into
  the Divi Code module, in full.
- **`assets/`** — three images: the Knight Foundation and Penn
  "Supported by" logos (`knight-foundation-logo.png`,
  `upenn-logo-full.png`, copied from the `PennMEDIATED/home` repo so
  this repo doesn't depend on another one at build time), and the
  brand lockup used in the footer's own logo
  (`mediated-white-transparent.svg`). Upload all three to the
  WordPress media library as part of installing the footer (see
  below).

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
| Page max-width | `1440px`, 80px side padding (32px < 900px, 20px < 480px) |

If a token changes in `home`'s style guide, update it here too — these
are duplicated, not shared, across repos (same discipline `home` and
`about` already use with each other).

## Installing in Divi

1. **Theme Builder → Global (Default Website Template) → Footer area
   → add a Code module.**
2. Paste the **entire contents of `footer.html`** into that module
   (style block and all — Divi Code modules render raw HTML as-is).
3. Assign the template scope so it applies sitewide, the same way
   Divi's old default footer was scoped.
4. Confirm the "Before you ship" items below, then preview a real
   page.

## Before you ship

A few things are placeholders because they depend on the live
WordPress instance or on decisions outside this repo — search
`footer.html` for these markers before installing:

- **`REPLACE_WITH_WP_MEDIA_URL`** (3 occurrences) — the Knight
  Foundation logo, Penn logo, and the MEDIATED brand lockup all
  point at a placeholder path. Upload the three files in `assets/`
  to the WordPress media library and swap in the real URLs.
- **Newsletter form action** — the subscribe form defaults to the
  same e2ma signup link the homepage already links out to
  (`https://signup.e2ma.net/signup/2017639/1984707/`), submitted as a
  GET with an `email` field. This is a best-effort default, not a
  confirmed integration — verify the field name against e2ma's actual
  form (or swap in whatever embed/plugin the team settles on) before
  launch.
- **Nav link slugs** — `LLM Civic Discourse`, `Compendium` (→
  `/research-compendium/`), `About`, and `Affiliated Faculty` (→
  `/faculty/`) use paths confirmed live on infodem.upenn.edu today.
  The rest (`Media Fragmentation`, `Hate Speech & LLMs`, `Our Team`,
  `Job Openings`, `Data & PennMAP`, `Grants`, `Events`) are best-guess
  slugs — the migration plan notes the page list is still growing, so
  confirm final paths on mediated.upenn.edu once that's settled.
- **Social links** — YouTube, Bluesky, GitHub, and LinkedIn URLs are
  filled in with the center's known handles; double-check each still
  resolves before launch.

## Previewing locally

`footer.html` opens directly in a browser — no build step, no server
needed — **once the three `REPLACE_WITH_WP_MEDIA_URL` image paths are
swapped for real URLs** (see "Before you ship"). Until then, opening
it locally will show broken-image icons where the Knight Foundation
logo, Penn logo, and the MEDIATED brand lockup go; everything else in
the layout will still render correctly, so that's usually enough to
check spacing, copy, and responsive behavior.

## Updating

This footer is static by design, so most changes are a direct edit to
`footer.html`:

```
edit footer.html → open it in a browser to check it → commit → push
→ git pull on the eniac deploy target → re-paste into the Divi Code
module if the change needs to reach the live module (Divi doesn't
read this repo directly — see note below)
```

**Note on deploy:** Divi's Code module stores whatever HTML was pasted
into it inside the WordPress database, not a file on disk — `git pull`
updates the *repo* on eniac, but someone still has to copy the updated
`footer.html` into the Theme Builder module in wp-admin for a change
to actually go live. If that becomes a frequent enough workflow to be
annoying, the phased plan's "future upgrade path" (push-to-deploy via
a `post-receive` hook, or a small mu-plugin that reads this file
directly instead of a hand-pasted Code module) is worth revisiting.
