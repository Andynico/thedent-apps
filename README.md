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
│   ├── the-dent-apps-logo-light.png  header logo (dark lettering), 320 px, shown at 64–80 px
│   └── the-dent-apps-logo-dark.png   header logo for dark mode (light lettering)
├── estia/
│   ├── index.html          /estia/          product page
│   ├── support/index.html  /estia/support/  support and FAQ
│   ├── privacy/index.html  /estia/privacy/  privacy policy
│   └── assets/             Estia icon and screenshots
└── quest-journal/
    ├── index.html          /quest-journal/          product page
    ├── support/index.html  /quest-journal/support/  support and FAQ
    ├── privacy/index.html  /quest-journal/privacy/  privacy policy
    └── assets/             Quest Journal icon
```

Each page is a directory with an `index.html`, so URLs are clean and end in `/`. All internal links are root-relative (`/estia/`), which is correct when the site is served from the root of apps.thedent.net.

## Preview locally

Root-relative links need a web server, so opening the files directly won't work. From the repository root:

```sh
python3 -m http.server 8000
```

Then open http://localhost:8000. (`_headers` only applies on Cloudflare.)

The header shows The Dent Apps logo (`alt="The Dent Apps"`), which already contains the name. Each page uses a `<picture>` with a `(prefers-color-scheme: dark)` source, so the browser picks the light or dark logo itself, with no JavaScript. The Estia icon uses the same pattern.

## Estia assets

In `estia/assets/`:

| File | Size | Notes |
| --- | --- | --- |
| `estia-icon-light.png` | 512 × 512 px | App icon with transparent padding, shown at up to 128 CSS px. |
| `estia-icon-dark.png` | 512 × 512 px | Dark-mode variant, chosen by `<picture>`. |
| `estia-screenshot.webp` | 2000 × 1150 px | Real capture of the main window, including its own window chrome, so the page adds only a thin frame. |

When replacing an image, keep the `width` and `height` attributes in the HTML matching its pixel size so the aspect ratio is reserved. The icon's `alt` stays empty because the app name is right next to it; the screenshot's `alt` should describe what it shows.

## Quest Journal assets

In `quest-journal/assets/`:

| File | Size | Notes |
| --- | --- | --- |
| `quest-journal-icon-light.png` | 512 × 512 px | The approved iOS app icon, resized from the app's 1024 px `AppIcon-Light.png`. |
| `quest-journal-icon-dark.png` | 512 × 512 px | Dark-mode variant, from `AppIcon-Dark.png`, chosen by `<picture>`. |
| `quest-journal-hero-light.webp` | 1536 × 1024 px | Product page artwork for light mode. |
| `quest-journal-hero-dark.webp` | 1536 × 1024 px | Dark-mode variant, chosen by `<picture>`. |

iOS icons are full-bleed squares that the system masks, so these get their rounded corners from the `.app-icon--ios` class in `styles.css` rather than from the image.

## Adding another app

1. Copy `estia/` to a new folder, e.g. `quest-journal/`, and update the text, `<title>`, `canonical` URL, app name in the app navigation, and footer links.
2. Set `data-app="quest-journal"` on the new pages' `<html>` element.
3. Colours are shared by every page, in light and dark mode, so the apps read as one site. If the app needs different typography, add a `[data-app="quest-journal"]` block under **App themes** in `styles.css` overriding `--font-body` or `--font-display`.
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
