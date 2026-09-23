# Health City (المدينة الصحية)

Arabic, right-to-left static web app for the Healthy City program's committees. It is a
PWA served as plain files (GitHub Pages; `.nojekyll` is present). There is no build step,
no package manager, no bundler, and no test suite — edit the HTML files directly.

## Files

| File | What it is |
|---|---|
| `index.html` | Standards & requirements registry by committee (search, filter). Links to `map.html`. |
| `map.html` | Interactive SVG map of workflow and coordination between committees. |
| `portal.html` | Committee portal: Firebase Auth + Firestore (members, tasks, meetings, reports, messages). |
| `sw.js` | Service worker: network-first, falls back to cache for offline. |
| `manifest.webmanifest` | PWA manifest (`lang: ar`, `dir: rtl`, shortcuts to portal and map). |
| `icons/` | App icons (192, 512, apple-touch, svg). |

Each page is self-contained: CSS in a `<style>` block, JS in inline `<script>` blocks,
fonts (Cairo, Tajawal) from Google Fonts. Only `portal.html` uses ES modules, importing the
Firebase SDK (v10.12.2) from `www.gstatic.com`. There are no local JS modules.

## Data lives inside the pages

- `REGISTRY` (index.html) — requirements grouped by committee → criteria → items.
- `CHARTERS` — committee charters by axis. **Identical copies in `index.html` and `map.html`;
  change both.**
- `DOMAINS` — the four domains and their committees, with colors. **Defined in both
  `index.html` and `map.html`; change both.**
- `COMMITTEES` (portal.html) — signup dropdown. Names are stored in Firestore `users`,
  `tasks`, `meetings` and `reports`, so renaming one orphans existing records. Note it
  uses `المنسق العام` where the other pages use `المنسق العام للمدينة الصحية`.

## Portal (Firebase)

- Project `healthy-city-4a090`; the web config in `portal.html` is public by design.
  Access control depends on Firestore security rules, which are **not** in this repo.
- Collections: `users`, `tasks`, `meetings`, `reports`, `messages`.
- Roles: `head` and `member`. New users sign up with `status: 'pending'`; heads approve
  members of their committee. Heads are auto-approved on login.

## Service worker

`sw.js` precaches the files listed in `ASSETS`. When adding, renaming or removing a page
or icon, update `ASSETS` and bump `CACHE` (`health-city-vN`) so old caches are cleared.
Every page registers `sw.js` in the `<head>`; new pages should too, along with the
manifest link.

## Conventions

- All user-facing text is Arabic; keep `lang="ar" dir="rtl"` and check layout in RTL.
- Reuse the CSS variables in each page's `:root` (`--teal-deep`, `--gold`, `--paper`, …)
  rather than new hard-coded colors.
- File names are lowercase (`index.html`, not `Index.html`).
- Commit messages: short, imperative, English (e.g. "Make Health City an installable PWA").

## Checking changes

No automated tests. Serve the folder locally (`python3 -m http.server`) and open the
pages in a browser — the service worker and ES module imports do not work from `file://`.
Check the page at phone width, since most users open it as an installed PWA.
