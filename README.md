# X-TRANS website (www.x-trans.gr)

Modern, bilingual (Ελληνικά / English) static website for **X-TRANS Ε.Π.Ε.**, a transport &
logistics company in Piraeus. Built with [Hugo](https://gohugo.io) + Tailwind CSS on top of the
[Hugoplate](https://github.com/zeon-studio/hugoplate) theme (MIT).

## Requirements

- [Hugo **extended**](https://gohugo.io/installation/) ≥ 0.158 (developed on 0.162)
- [Go](https://go.dev) ≥ 1.23 (Hugo Modules)
- [Node.js](https://nodejs.org) ≥ 18 + npm (Tailwind build pipeline)

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
layouts/              Project overrides (logo, header, services, financial, contact, json-ld, head)
assets/images/        Source images (Hugo processes → responsive WebP) — provenance in CREDITS.md
static/_redirects     301s from the old Blogger URLs (Cloudflare Pages)
static/docs/          Drop balance-sheet PDFs here
themes/hugoplate/     Theme (do not edit; override in /layouts instead)
```

### Pages & URLs

| Section | Greek (default) | English |
|---|---|---|
| Home | `/` | `/en/` |
| Company / Η Εταιρεία | `/etaireia/` | `/en/about/` |
| Services / Υπηρεσίες | `/ypiresies/` | `/en/services/` |
| Balance sheet / Ισολογισμός | `/isologismos/` | `/en/financial-statements/` |
| Contact / Επικοινωνία | `/epikoinonia/` | `/en/contact/` |

Greek and English versions are linked via `translationKey` in each page's front matter.

## Common edits

- **Company contact details / map** — `config/_default/params.toml` → `[company]` block (address,
  phones, email, hours, `map_lat`/`map_lon`). Used by the contact page, footer and JSON-LD schema.
- **Services** — edit the `service_groups` list in `content/{greek,english}/…` front matter.
- **Balance-sheet PDFs** — put files in `static/docs/`, then uncomment/add the `documents:` list in
  `content/greek/isologismos.md` and `content/english/financial-statements.md`.
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
| Environment variables | `HUGO_VERSION = 0.162.0`, `GO_VERSION = 1.23.3`, `NODE_VERSION = 20` |

Then add `www.x-trans.gr` as a custom domain (HTTPS is automatic). The
[`static/_redirects`](static/_redirects) file 301-redirects the old Blogger URLs to preserve SEO.

> Enable **Cloudflare Web Analytics** for the site and paste the beacon token into
> `params.toml` (`custom_script`).
