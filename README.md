# X-TRANS website (www.x-trans.gr)

Modern, bilingual (Ελληνικά / English) static website for **X-TRANS Ε.Π.Ε.**, a transport &
logistics company in Piraeus. Built with [Hugo](https://gohugo.io) + Tailwind CSS on top of the
[Hugoplate](https://github.com/zeon-studio/hugoplate) theme (MIT).

## Requirements

- [Hugo **extended**](https://gohugo.io/installation/) ≥ 0.158 (developed on 0.162)
- [Go](https://go.dev) ≥ 1.23 (Hugo Modules)
- [Node.js](https://nodejs.org) **≥ 24** + npm (Tailwind CSS v4 needs Node's `--permission` flag, introduced in Node 23.5 — see Deployment notes below)

## Local development

```bash
npm install        # once
npm run dev        # Tailwind watch + hugo server at http://localhost:1313
```

Production build (what the host runs):

```bash
npm run build      # generates theme CSS, then `hugo --gc --minify` → ./public
```

## Project structure

```
config/_default/      Site config (languages, menus.el/en, params, modules)
content/greek/        Greek pages (default language, served at /)
content/english/      English pages (served under /en/)
data/theme.json       Brand colours + fonts (re-run build after editing)
i18n/{el,en}.yaml     UI string translations
layouts/              Project overrides (logo, header, services, documents, contact, json-ld, head)
assets/images/        Source images (Hugo processes → responsive WebP) — provenance in CREDITS.md
static/_redirects     301s from old/renamed URLs (Cloudflare Pages)
static/docs/          Drop downloadable files here (PDFs, xlsx, docx, …)
themes/hugoplate/     Theme (do not edit; override in /layouts instead)
```

### Pages & URLs

| Section | Greek (default) | English |
|---|---|---|
| Home | `/` | `/en/` |
| Company / Η Εταιρεία | `/etaireia/` | `/en/about/` |
| Services / Υπηρεσίες | `/ypiresies/` | `/en/services/` |
| Downloads / Λήψεις | `/lipseis/` | `/en/downloads/` |
| Contact / Επικοινωνία | `/epikoinonia/` | `/en/contact/` |

Greek and English versions are linked via `translationKey` in each page's front matter.

## Common edits

- **Company contact details / map** — `config/_default/params.toml` → `[company]` block (address,
  phones, email, hours, `map_lat`/`map_lon`). Used by the contact page, footer and JSON-LD schema.
  English overrides (street / city / country_name) live in `config/_default/languages.toml` under
  `[en.params.company]`; the remaining fields (phone, fax, email, postal code, coordinates) are
  inherited automatically. The footer `copyright` uses the same pattern (`[en.params].copyright`
  overrides the Greek default).
- **Services** — edit the `service_groups` list in `content/{greek,english}/…` front matter.
- **Downloadable files** (Λήψεις / Downloads page) — put files in `static/docs/`, then uncomment /
  add entries in the `documents:` list in `content/greek/lipseis.md` and
  `content/english/downloads.md`. Each entry takes `label`, `url`, optional `icon:`
  (`fa-regular fa-file-pdf` / `…-excel` / `…-word` / `…-lines` / `…-image` / `…-zipper`; defaults to a
  generic file icon) and optional `external: true` (opens in a new tab).
- **Photos** — replace files in `assets/images/` (keep the same names) or update the `image:` paths.
- **Brand colours / fonts** — `data/theme.json` (re-run `npm run build`).
- **Analytics** — set the Cloudflare Web Analytics token in `params.toml` → `custom_script`
  (replace `REPLACE_WITH_CLOUDFLARE_TOKEN`). Cookieless, so no consent banner is needed.

## Deployment — Cloudflare Pages

Connect the repo in the Cloudflare dashboard with:

| Setting | Value |
|---|---|
| Framework preset | Hugo |
| Build command | `npm run build` |
| Build output directory | `public` |
| Environment variables | `HUGO_VERSION = 0.162.0`, `GO_VERSION = 1.23.3`, `NODE_VERSION = 24`, `HUGO_BASEURL = <deployment URL>` |

> **Node 24+ is required.** Hugo's Tailwind CSS v4 step runs the Tailwind CLI with Node's
> `--permission` flag, which only exists from Node 23.5 onward — Node 20/22 builds fail with
> `node: bad option: --permission`. The `.nvmrc` pins Node 24; note the dashboard `NODE_VERSION`
> environment variable **overrides** `.nvmrc`, so it must also be 24.

**baseURL / `HUGO_BASEURL`.** The `build` script sets Hugo's `baseURL` from, in order of priority:
`HUGO_BASEURL` → `CF_PAGES_URL` → the config default (`https://www.x-trans.gr`). Getting this right
matters because absolute references (canonical, `og:image`, the search index `searchindex.json`)
must match the host being served, or the browser makes cross-origin requests to the wrong domain.
Cloudflare's current build system does **not** reliably expose `CF_PAGES_URL`, so set `HUGO_BASEURL`
explicitly per environment:
- **While testing on the pages.dev URL:** `HUGO_BASEURL = https://x-trans-gr-site.pages.dev`
- **At launch (custom domain):** `HUGO_BASEURL = https://www.x-trans.gr`

Add `www.x-trans.gr` as a custom domain (HTTPS is automatic) when going live. The
[`static/_redirects`](static/_redirects) file 301-redirects the old Blogger URLs to preserve SEO.

> Enable **Cloudflare Web Analytics** for the site and paste the beacon token into
> `params.toml` (`custom_script`).
