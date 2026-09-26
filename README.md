# The Dent Apps

Static website for **https://apps.thedent.net**, the home for small applications made by Andy Nicolaides. The main blog at https://thedent.net is hosted separately on Pika and is not part of this repository.

Plain HTML and one stylesheet. No framework, build step, JavaScript, cookies, analytics or third-party requests.

## Structure

```
/
├── index.html              The Dent Apps home page
├── 404.html                Not-found page (also stops Cloudflare Pages treating the site as a single-page app)
├── styles.css              The only stylesheet, shared by every page
├── _headers                Cloudflare Pages response headers (strict CSP, etc.)
├── favicon.png             64 px The Dent logo
├── apple-touch-icon.png    180 px, on white (iOS fills transparency with black)
├── assets/images/
│   ├── the-dent-logo.png       876 px master (transparent)
│   └── the-dent-logo-192.png   header logo, shown at 44 px
└── estia/
    ├── index.html          /estia/          product page
    ├── support/index.html  /estia/support/  support and FAQ
    ├── privacy/index.html  /estia/privacy/  privacy policy
    └── assets/             Estia icon and screenshots
```

Each page is a directory with an `index.html`, so URLs are clean and end in `/`. All internal links are root-relative (`/estia/`), which is correct when the site is served from the root of apps.thedent.net.

## Preview locally

Root-relative links need a web server, so opening the files directly won't work. From the repository root:

```sh
python3 -m http.server 8000
```

Then open http://localhost:8000. (`_headers` only applies on Cloudflare.)

The header shows The Dent logo (`alt="The Dent"`) followed by the word "Apps", so it reads as "The Dent Apps". The logo is black on transparent, so in dark mode `styles.css` puts it on a small light tile.

## Estia assets

The icon and screenshot are currently CSS placeholders. To replace them, add these files to `estia/assets/`:

| File | Recommended size | Notes |
| --- | --- | --- |
| `estia-icon.png` | 512 × 512 px | Exported from the macOS app icon, with its rounded shape, shadow and transparent padding intact. Shown at up to 128 CSS px (so 512 covers Retina with room to spare). |
| `estia-screenshot.png` | 2880 × 1800 px (16:10) | A Retina capture of the main window. Aim for under ~600 KB; a high-quality `.jpg` or `.webp` is fine too if you update the filename. |

Then, in `estia/index.html` (and for the icon, the Estia row in `/index.html`), delete the placeholder `<div>` and uncomment the `<img>` beside it. For the screenshot, write real `alt` text describing what it shows. The icon's `alt` stays empty because the app name is right next to it.

## Adding another app

1. Copy `estia/` to a new folder, e.g. `quest-journal/`, and update the text, `<title>`, `canonical` URL, app name in the app navigation, and footer links.
2. Set `data-app="quest-journal"` on the new pages' `<html>` element.
3. In `styles.css`, under **App themes**, add a `[data-app="quest-journal"]` block overriding whichever tokens the app needs (`--paper`, `--accent`, `--font-body`, …), plus a dark-mode block. Everything else is shared.
4. Add an `<li data-app="quest-journal">` entry to the app list on `/index.html`, and add the app to each page's footer.

## Deploying to Cloudflare Pages

Create a Pages project connected to this Git repository:

- **Framework preset:** None
- **Production branch:** `main`
- **Build command:** *(leave empty)*
- **Build output directory:** `/` (the repository root)

Then add **apps.thedent.net** as a custom domain for the project (Custom domains → Set up a custom domain). Cloudflare issues and renews the HTTPS certificate automatically.

- If thedent.net's DNS is managed by Cloudflare, it creates the `apps` CNAME record for you.
- If DNS is elsewhere, add a `CNAME` record for `apps` pointing to `<project-name>.pages.dev` at your DNS provider. This does not affect the root domain or Pika.

If the domain is on Cloudflare, make sure **Email Address Obfuscation** (Scrape Shield) is off for the zone, or exclude the site from it: it rewrites `mailto:` links and injects a script, which the site's CSP blocks.
