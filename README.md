# mtdonnell.com

Personal portfolio site. Plain HTML/CSS/JS, no build step, deployed on Cloudflare
with static assets served from the repo root.

Live at <https://mtdonnell.com>.

---

## Structure

```
mtdonnell.com/
├── index.html          # Entire site (hash-routed tabs: home/portfolio/about/contact)
├── resume.pdf          # Linked from the nav Resume button and /resume
├── _headers            # Security headers and per-path cache rules
├── _redirects          # /resume and /linkedin vanity links
├── robots.txt
├── sitemap.xml
├── .gitattributes
├── .gitignore
└── assets/
    ├── og-image.png        # 1200x630 link preview (LinkedIn, Slack, iMessage)
    ├── favicon.svg
    ├── favicon.ico
    ├── apple-touch-icon.png
    └── icon-512.png
```

There is no framework and no `npm install`. Open `index.html` in a browser and it works.

---

## Local development

Opening the file directly (`file://`) works, but a local server matches production
behaviour for absolute paths like `/assets/favicon.svg`. Pick one:

```bash
python -m http.server 8000    # already on most machines
npx serve .
```

Then visit <http://localhost:8000>.

Note that `_headers` and `_redirects` are **not** applied by a plain static server.
Neither `/resume` nor the security headers will work locally; both are applied at
the edge. Verify those against the deployed URL, not localhost.

---

## Deploying

Deployment is automatic. Push to `main` and Cloudflare rebuilds:

```bash
git add .
git commit -m "Add Docker migration write-up"
git push
```

Build configuration: no framework preset, no build command, output directory `/`.

### `_headers` and `_redirects`

These replace the `netlify.toml` this site previously used. The syntax differs, and
one rule does not carry over:

> **Do not add `/* /index.html 200`.** Netlify needs that line for SPA fallback.
> Cloudflare rejects it with error `100324` (infinite loop) because it already
> strips `.html` and `/index` itself, so the rule re-triggers on its own output.
> The deploy fails outright. It is unnecessary here anyway — navigation is
> hash-based, so only `/`, `/resume.pdf` and `/assets/*` reach the server.

A consequence of not having that rule: unknown paths return a bare 404 rather than
falling back to the homepage. Add a `404.html` if that matters.

### DNS

Registered at Namecheap, DNS delegated to Cloudflare. The domain is attached to the
Worker as a **Custom Domain**, not a Route — Cloudflare manages that record itself,
so do not hand-create an `A` or `CNAME` for the apex. A stale proxied `A` record
pointing anywhere else produces a 522.

The `MX` and SPF `TXT` records are Namecheap email forwarding and are unrelated to
hosting. Leave them alone.

---

## Before sharing the link widely

- [ ] Verify the link preview renders: paste the URL into the
      [LinkedIn Post Inspector](https://www.linkedin.com/post-inspector/)
- [ ] Add a DMARC record — without one, `@mtdonnell.com` can be spoofed
- [ ] Add a headshot to the About page

---

## Maintenance notes

**Keep the roadmap honest.** The Portfolio page publishes a public roadmap. That is
an asset when it's current and a liability when it's stale. When you ship something,
move it from the roadmap to a project card and change the badge to `active`.

**Keep `resume.pdf` current.** The nav button, the `/resume` redirect, and a
`Content-Disposition` header all point at it.

**Update the footer date.** `index.html` has a hardcoded "Last updated" string in the
footer. Change it when you make a meaningful update. The copyright year updates itself.

**Update `sitemap.xml`** whenever you add a page — add a `<url>` block and refresh
`<lastmod>`.

**Regenerating the OG image.** `assets/og-image.png` must stay 1200x630. If you edit
it, re-check it in the LinkedIn Post Inspector, which caches aggressively.

---

## Future: migrating to a static site generator

The current site is a single HTML file with hash-based tabs (`#portfolio`, `#about`).
That's fine now, but it means search engines index one page and you can't link
directly to an individual project.

When you're ready to publish project write-ups, migrate to **Astro** or **Eleventy**.
Both let you write posts as Markdown — which pairs directly with the Obsidian vault,
so drafting a note and publishing an article become the same action.

Do this *after* the first write-up is drafted, not before. A build system is not
allowed to block shipping.
