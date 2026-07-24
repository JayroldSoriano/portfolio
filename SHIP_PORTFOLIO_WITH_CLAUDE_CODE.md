# Shipping the Portfolio to a Live Website with Claude Code

A complete guide for taking `jayrold-soriano-portfolio.html` from a single file to a deployed website at a custom domain, using Claude Code as the delivery agent — **without altering the existing UI design in any way**.

---

## 1. What we're shipping

The portfolio is a **single-file, zero-dependency static site**. Everything — markup, styles, and the ticker animation — lives in one HTML file. There is no build step, no JavaScript framework, and no external assets beyond Google Fonts. This is a feature, not a limitation: it deploys anywhere that can serve a static file, loads fast, and can't break from dependency rot.

| Item | Value |
|---|---|
| Source file | `jayrold-soriano-portfolio.html` |
| External dependencies | Google Fonts only (Archivo Black, Instrument Sans, Space Mono) |
| Build step required | None |
| JS required | None (pure HTML/CSS; `<details>` powers the project rows) |

---

## 2. The Design Preservation Contract

This is the most important section. Claude Code is capable and eager — which means it may "helpfully" refactor, re-theme, or componentize the site. **Paste the rules below into the repo's `CLAUDE.md` so every session inherits them.** They define exactly what the design is and forbid changes to it.

### 2.1 Locked design tokens

```css
--paper:  #F3F3F0;   /* page background — cool bone, NOT warm cream */
--ink:    #121317;   /* primary text and borders */
--cobalt: #2438FF;   /* the accent — links, hovers, open project rows */
--cobalt-tint: #E6E9FF;  /* hover fill on build cards */
--steel:  #7C7F87;   /* muted metadata text */
--line:   #D6D6D1;   /* hairline dividers */
--max:    1200px;    /* content max-width */
```

### 2.2 Locked typography

| Role | Font | Usage |
|---|---|---|
| Display | **Archivo Black** | All-caps headlines, project titles, hero name |
| Body | **Instrument Sans** | Paragraphs, descriptions |
| Utility | **Space Mono** | Uppercase labels, tags, chips, nav links, footer |

### 2.3 Locked structural signatures

These are the identity of the site. None may be removed or restyled:

1. **Hero** — stacked name with the second line rendered as outlined (stroke-only) type; two-column sub-block; cobalt eyebrow label.
2. **Ticker strip** — black band under the hero with an infinitely scrolling project marquee (duplicated content, `translateX(-50%)` keyframe). Must keep its `prefers-reduced-motion` fallback.
3. **Project index** — full-width `<details>` rows with hairline `--ink` borders that invert to **solid cobalt with white text** on hover/open. Titles in giant Archivo Black caps.
4. **Build grid** — 2-column card grid separated by 1px `--line` gaps, cards tint to `--cobalt-tint` on hover.
5. **Section rhythm** — generous 80px vertical padding, mono uppercase section metadata on the right of each heading.

### 2.4 Standing prohibitions for Claude Code

```
NEVER: introduce a CSS framework (Tailwind, Bootstrap), a JS framework
       (React, Vue), or a static site generator — the site stays vanilla.
NEVER: change colors, fonts, spacing scale, border weights, or animations.
NEVER: convert <details>/<summary> project rows into JS-driven accordions.
NEVER: add a dark mode, theme switcher, or alternative palettes unless
       explicitly requested by Jayrold.
NEVER: "clean up" or reformat the CSS in ways that change rendered output.
ALWAYS: verify visual parity after any change (see §6 QA).
ALWAYS: ask before adding any new visual element.
```

---

## 3. Repository setup

Run these once, locally:

```bash
mkdir portfolio && cd portfolio
git init
# Rename to index.html — required for every static host to serve it at "/"
cp /path/to/jayrold-soriano-portfolio.html index.html
```

Create `CLAUDE.md` in the repo root containing: the Design Preservation Contract (§2 above, verbatim), plus:

```markdown
# CLAUDE.md — Portfolio Site

## Project
Single-file static portfolio for Jayrold Soriano (index.html).
No build step. No frameworks. Deploys as-is.

## Commands
- Preview locally:  python3 -m http.server 8000
- Deploy:           git push (CI/CD handles the rest — see SHIP guide §4)

## Rules
[paste §2.1–§2.4 here]
```

Then commit:

```bash
git add index.html CLAUDE.md
git commit -m "Portfolio v1 — locked design baseline"
```

That commit is your **design baseline**. Any future visual diff is measured against it.

---

## 4. Choosing a host

All three options below are free, serve static files over a global CDN, auto-provision HTTPS, and deploy on `git push`. Pick one.

### Option A — GitHub Pages (simplest, recommended first)

Best when: you want the fastest path and the repo already lives on GitHub.

```bash
gh repo create JayroldSoriano/portfolio --public --source=. --push
gh api repos/JayroldSoriano/portfolio/pages -X POST \
  -f 'source[branch]=main' -f 'source[path]=/'
```

Site appears at `https://jayroldsoriano.github.io/portfolio/` within a minute or two. Bonus: a public portfolio repo with a clean single-file site is itself a portfolio artifact.

### Option B — Cloudflare Pages

Best when: you want the custom domain on Cloudflare DNS (fastest global edge, and you already use R2 for PickleEye, so the dashboard is familiar).

1. Cloudflare dashboard → **Workers & Pages → Create → Pages → Connect to Git**.
2. Select the repo. Build command: *(leave empty)*. Output directory: `/`.
3. Every push to `main` auto-deploys; PRs get preview URLs.

### Option C — Vercel

Best when: you may later grow the site into Next.js (though per §2.4, only if you explicitly decide to).

```bash
npm i -g vercel
vercel --prod
```

### Custom domain (any host)

Buy/manage the domain (e.g. `jayroldsoriano.dev`) at your registrar, then:

- **GitHub Pages:** repo → Settings → Pages → Custom domain; add a `CNAME` record pointing to `jayroldsoriano.github.io`, enable "Enforce HTTPS".
- **Cloudflare Pages:** Pages project → Custom domains → add; DNS is handled automatically if the domain is on Cloudflare.
- **Vercel:** `vercel domains add jayroldsoriano.dev`.

Since you've deployed to Namecheap shared hosting before: that works too (upload `index.html` to `public_html/` via cPanel), but you lose git-push deploys and preview URLs, so prefer one of the three above.

---

## 5. The Claude Code workflow

With the repo prepared, hand delivery to Claude Code. Suggested session flow, as literal prompts:

### Session 1 — Repo + deploy

```
Read CLAUDE.md first. This is a single-file static portfolio whose design
is locked — your job is delivery, not redesign.

1. Verify index.html renders standalone: serve it locally and confirm
   no console errors and no 404s.
2. Create the GitHub repo and push (Option A in
   SHIP_PORTFOLIO_WITH_CLAUDE_CODE.md), then enable GitHub Pages.
3. Report the live URL when the deploy is green.
Do not modify index.html in this session.
```

### Session 2 — Metadata & SEO (no visual changes)

```
Read CLAUDE.md. Make ONLY non-visual, <head>-level additions to index.html:

1. Open Graph + Twitter card meta tags (title, description, og:type=website).
2. A favicon: generate a simple 32x32 SVG favicon — the letter "J" in
   Archivo Black, ink #121317 on paper #F3F3F0 — and inline it as a data
   URI so the site stays single-file.
3. JSON-LD Person schema (name, jobTitle "Full-Stack Developer",
   url, sameAs: the GitHub profile).
4. robots.txt and sitemap.xml at the repo root.

After editing, diff the rendered page against the design baseline commit:
take before/after screenshots at 1440px and 390px widths and confirm they
are pixel-identical. If anything visual changed, revert.
```

### Session 3 — Optional enhancements (each requires explicit approval)

Only if and when you want them — each is compatible with the locked design:

- **Résumé link**: a PDF résumé in the repo, linked from the contact section using the existing `.cta` button style (a second button, identical styling).
- **Analytics**: a privacy-friendly, script-light option (Cloudflare Web Analytics or Plausible) — one `<script>` tag, zero UI.
- **Contact email**: swap or add alongside the GitHub CTA once you decide which address to publish.

### Prompt patterns that protect the design

When asking Claude Code for anything on this project, the two magic phrases are:

- *"Read CLAUDE.md first"* — loads the contract into every session.
- *"No visual changes; verify with before/after screenshots"* — makes parity a completion criterion, not an afterthought.

If Claude Code proposes a redesign, framework migration, or "modernization," the answer is in the contract: decline and proceed with delivery only.

---

## 6. QA checklist (run before calling any deploy done)

```
[ ] Loads over HTTPS at the production URL with no mixed-content warnings
[ ] Fonts render (Archivo Black headline is NOT falling back to system sans)
[ ] Ticker scrolls smoothly; stops when OS "reduce motion" is on
[ ] All five project rows open/close and invert to cobalt correctly
[ ] All four GitHub repo links resolve (MacShift, Arcway-Nexus,
    Automation-Dashboard, carwashApp)
[ ] Mobile (390px): hero stacks, nav collapses to name-only, project
    summaries stack, build grid is single-column
[ ] Keyboard: tab order reaches every link and every <summary>;
    focus rings (3px cobalt) are visible
[ ] Lighthouse: Performance ≥ 95, Accessibility ≥ 95, SEO ≥ 95
[ ] og:image/title/description preview correctly (test with a link
    paster like Slack or Messenger)
```

---

## 7. Versioning & rollback

Because the whole site is one file, rollback is trivial and should be used liberally:

```bash
git log --oneline index.html          # find the last good version
git checkout <sha> -- index.html      # restore it
git commit -m "Revert to design baseline"
git push                              # host redeploys automatically
```

Tag meaningful milestones (`git tag v1-launch`) so "the version that looked right" always has a name.

---

## 8. Future content updates

New projects will come. The update path that keeps the design intact:

1. Ask Claude Code to *"add a new build card / project row following the exact existing markup pattern — copy an existing block and change only the text content and links."*
2. The chips, mono labels, and grid handle any number of entries — the design scales without modification.
3. Re-run the §6 checklist, push, done.

---

*Guide prepared July 2026 for the v1 portfolio. The single source of truth for what the site should look like is the design baseline commit — when in doubt, diff against it.*
