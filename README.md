# mtdonnell.com

Personal portfolio site. Plain HTML/CSS/JS, no build step, deployed on Netlify.

---

## Structure

```
mtdonnell.com/
├── index.html          # Entire site (hash-routed tabs: home/portfolio/about/contact)
├── resume.pdf          # Linked from nav "Resume" button and /resume
├── netlify.toml        # Security headers, caching, redirects
├── robots.txt
├── sitemap.xml
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
behavior for absolute paths like `/assets/favicon.svg`. Pick one:

```bash
# Python (already on most machines)
python3 -m http.server 8000

# Node
npx serve .

# Netlify CLI — closest to production, applies netlify.toml rules
npm install -g netlify-cli
netlify dev
```

Then visit `http://localhost:8000`.

---

## First-time deploy

1. Create a new GitHub repo (suggested name: `mtdonnell.com`).
2. From this folder:

   ```bash
   git init
   git add .
   git commit -m "Initial site"
   git branch -M main
   git remote add origin https://github.com/USERNAME/mtdonnell.com.git
   git push -u origin main
   ```

3. In Netlify: **Add new site → Import an existing project → GitHub → select the repo.**
   - Build command: leave empty
   - Publish directory: `.`
4. **Domain management → Add custom domain →** `mtdonnell.com`.
   Follow Netlify's DNS instructions (either point your registrar's nameservers at
   Netlify DNS, or add the A/CNAME records it gives you).
5. Wait for HTTPS to provision. Netlify issues a free Let's Encrypt certificate
   automatically once DNS resolves. Then enable **Force HTTPS**.
6. Set `mtdonnell.com` as the primary domain so `www` redirects to it (or vice versa —
   just pick one and be consistent).

After this, every `git push` to `main` redeploys automatically.

---

## Routine updates

```bash
# edit index.html
git add .
git commit -m "Add Docker migration write-up"
git push
```

Netlify rebuilds in seconds.

---

## Before you share the link publicly

- [ ] Replace `resume.pdf` with your current resume
- [ ] Verify the link preview renders: paste `https://mtdonnell.com` into the
      [LinkedIn Post Inspector](https://www.linkedin.com/post-inspector/)
- [ ] Uncomment the GitHub links in `index.html` (3 spots) and set your username
- [ ] Add a headshot to the About page
- [ ] Confirm the roadmap phases still reflect reality

---

## Maintenance notes

**Keep the roadmap honest.** The Portfolio page publishes a public roadmap. That is
an asset when it's current and a liability when it's stale. When you ship something,
move it from the roadmap to a project card and change the badge to `active`.

**Update the footer date.** `index.html` has a hardcoded "Last updated" string in the
footer. Change it when you make a meaningful update. The copyright year updates itself.

**Update `sitemap.xml`** whenever you add a page — add a `<url>` block and refresh
`<lastmod>`.

**Regenerating the OG image.** `assets/og-image.png` must stay 1200×630. If you edit
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
