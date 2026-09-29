# amn-advisory-site

Static site for [Ailbhe McNeela Advisory](https://www.amnadvisory.ie/), hosted on GitHub Pages. Ported from Squarespace in September 2026.

Plain HTML and CSS: no build step, no dependencies.

- `index.html` is the whole site (single page).
- `assets/css/site.css` holds the styles. The palette is defined as custom properties at the top.
- `assets/fonts/` holds self-hosted PT Serif and Almarai (latin subset, woff2), so the site makes no calls to Google Fonts.
- `assets/img/` holds each photo at 600/800/1200px in WebP, with JPEG fallbacks.

## Preview locally

```sh
python3 -m http.server 8000
```

Then open http://localhost:8000.

## Deploy

Pushing to `main` publishes via GitHub Pages (Settings → Pages → Deploy from branch → `main` / root).
