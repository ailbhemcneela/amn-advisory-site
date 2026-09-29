# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static HTML/CSS website for Ailbhe McNeela Advisory, Ailbhe's DTC and e-commerce strategy consultancy. It's a port of the original Squarespace site at `amnadvisory.ie`, hosted on GitHub Pages. No build step, no framework, no bundler: files are served as-is.

## Current status: not yet live on the domain

As of 2026-09-29 the site is being moved off Squarespace:

1. **GitHub Pages** needs enabling (Settings → Pages → Deploy from a branch → `main`, `/ (root)`). Once it's on, the site is served at `https://ailbhemcneela.github.io/amn-advisory-site/`, while `amnadvisory.ie` still serves the old Squarespace site.
2. **Domain cutover** happens once the github.io version is approved:
    - DNS for `amnadvisory.ie` is at **Blacknight**.
    - Email runs through **Google Workspace**, so never change or remove the MX records.
    - Point `www` (CNAME) at `ailbhemcneela.github.io` and the apex (A records) at GitHub Pages' IPs.
    - Add a `CNAME` file containing `www.amnadvisory.ie`, then enable "Enforce HTTPS".
3. Cancel Squarespace only after the domain is confirmed working on GitHub Pages.

**Don't add the `CNAME` file before the DNS change.** Doing so redirects the github.io preview to the domain, which still points at Squarespace.

Until the cutover, "the live site" in conversation with Ailbhe means the github.io address, not amnadvisory.ie. Update this section once the cutover is done.

## Pages

- `index.html`: the whole site (single page). Sections are hero, "what I've learned", services, about, testimonials, and contact (`#contact`).

## Contact

There is no form backend. The contact section and footer invite visitors to email **ailbhe@amnadvisory.ie** via `mailto:` links. Don't add a form unless a backend (e.g. Formspree) is agreed first.

## Development

Serve locally with any static server:

```bash
npx serve .
# or
python3 -m http.server 8080
```

All asset paths are **relative** (no leading `/`) so the site works both at the github.io project URL (`/amn-advisory-site/`) and at the root of the custom domain. Keep new links and assets relative.

## Structure conventions

- Styles live in `assets/css/site.css`. The palette is defined as CSS custom properties at the top (`--sand`, `--espresso`, `--linen`, `--umber`, `--cream`); use those rather than new hex values.
- Fonts are self-hosted in `assets/fonts/`: PT Serif (headings) and Almarai (body), latin woff2 only. Don't add Google Fonts `<link>`s, because self-hosting avoids a third-party request and a GDPR question.
- Images live in `assets/img/` as `<name>-{600,800,1200}.{webp,jpg}`, used via `<picture>` with `srcset`/`sizes`. When adding a photo, generate all six variants (WebP quality ~72, JPEG ~78) with Python/PIL. The source images aren't in the repo.
- Give every `<img>` `width`/`height` (to prevent layout shift) and descriptive `alt` text. Use `loading="lazy"` for everything below the hero.
- No inline styles; keep presentation in the CSS file.
- Mobile layout is single-column below 800px. Check any change at ~390px width as well as desktop.

## Quality bar

The site scores 100 on Lighthouse (Performance, Accessibility, Best Practices, SEO) on both mobile and desktop. After any non-trivial change, re-run it against the local server and fix any regressions before publishing:

```bash
lighthouse http://localhost:8080/ --quiet --chrome-flags="--headless=new" --view
lighthouse http://localhost:8080/ --preset=desktop --quiet --chrome-flags="--headless=new" --view
```

Cache-policy and text-compression warnings from the local server can be ignored, because GitHub Pages handles both. Watch colour contrast in particular: `--sand` text on `--umber` fails, which is why the dark testimonial card uses a lighter caption colour.

---

## Working with Ailbhe: important guidance for Claude

Ailbhe is the site owner and is not technical. She uses Claude Code on her laptop to make changes and preview them. Adapt all communication accordingly.

### Language to use

Never use technical terms like "push", "commit", "branch", "main", "repo", "git", or "deploy" with Ailbhe. Use plain equivalents instead:

| Instead of... | Say... |
|---|---|
| push / deploy | publish to the live site |
| commit | save a version |
| main branch | the live site |
| git history / previous commits | saved versions / previous versions |
| roll back / revert | go back to a previous version |
| repository | the site files |

### How a session works

Every session should follow this pattern: **experiment locally → preview → save checkpoints → publish when ready.**

Ailbhe's local preview is completely separate from the live site. Nothing she does locally affects the live site until she explicitly chooses to publish. She should feel free to experiment without worrying about breaking anything live.

**At the start of a session:**
- Start the local preview server (`npx serve .`) so Ailbhe can see changes in her browser at `http://localhost:3000`.
- Say something like: *"Your preview is running — any changes we make will show up here. Nothing will affect your live site until you decide to publish."*

**During a session, save checkpoints frequently:**
- After each meaningful change (a new section, a colour tweak, a layout adjustment), save a local checkpoint automatically without asking permission. Describe it in plain English (e.g. "Tried larger heading", "Added new testimonial").
- These checkpoints only exist on her laptop; they do not affect the live site.
- Ailbhe doesn't need to know this is happening. Only mention it if it becomes relevant (e.g. she wants to go back).

**At the end of a session, offer to publish:**
- Run the Lighthouse check (see "Quality bar") quietly first and fix any regressions. Only mention it to Ailbhe if something needs her decision.
- When Ailbhe is happy with how things look in the preview, proactively offer: *"Everything looks great in the preview — would you like me to publish this to your live site? It'll go live within a minute or two."*
- Only push to the remote (publish) when she confirms.
- After publishing, confirm: *"Done! Your changes are now live."*

**If she wants to abandon the session:**
- If Ailbhe wants to discard everything from the current session and go back to how the live site looks, run `git fetch origin && git reset --hard origin/main` to restore her local copy to match the live site.
- Frame it as: *"No problem — I've reset your laptop back to match the live site. Nothing has changed on your live site."*

### Going back to a previous version

If Ailbhe wants to undo changes or go back to something she preferred:
1. Run `git log --oneline -20` to get recent saved versions.
2. Present them as a simple numbered list with plain-English descriptions. Never show hashes or technical details.
3. Let her pick which version to restore, then run `git checkout <hash> -- .` followed by a new checkpoint save.
4. If she wants that version published, push it. Explain: *"I've restored your site to the version where [description]."*
5. If she just wants to see it in preview first, do that before offering to publish.

### No pull requests

Always commit directly to main. Never create branches or pull requests. The site's collaborators are Ailbhe and Martin, and it doesn't need a review workflow.

### Git setup note (for first-time setup on Ailbhe's laptop)

If the site files aren't on her laptop yet, she needs to clone the repo once:
```bash
git clone https://github.com/ailbhemcneela/amn-advisory-site.git
cd amn-advisory-site
```
Explain this as: *"I'll download your site files to your laptop so we can work on them."*

### Publishing credentials (not yet set up for this site)

Ailbhe's laptop has a fine-grained GitHub token in her Mac Keychain for `yogawithailbhe-site`, but that token is **scoped to that repo only** and won't work here. The first time she publishes this site, a push will likely fail with an authentication error. When it does:
- Walk her through either editing the existing token on GitHub.com to add `amn-advisory-site` to its repository access, or generating a new fine-grained token (Settings → Developer settings → Personal access tokens → Fine-grained tokens, scoped to this repo, "Contents: Read and write").
- Store it with `git credential-osxkeychain store`.
- Keep the `origin` remote as the plain `https://github.com/ailbhemcneela/amn-advisory-site.git` URL, with no credentials embedded.
- Once it works, update this section to say it's set up, with the date.
