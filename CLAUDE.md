# CLAUDE.md — Portfolio Site (Jayrold Soriano)

## Project
Single-file static portfolio (index.html). No build step. No frameworks. Deploys as-is via GitHub Pages.

## Commands
- Preview locally: python3 -m http.server 8000
- Deploy: git push (GitHub Pages serves main branch root)

## Design Preservation Contract — LOCKED
Tokens:
--paper:#F3F3F0 --ink:#121317 --cobalt:#2438FF --cobalt-tint:#E6E9FF --steel:#7C7F87 --line:#D6D6D1 --max:1200px

Typography: Archivo Black (display, all-caps), Instrument Sans (body), Space Mono (utility labels/chips).

Structural signatures (identity of the site — never remove or restyle):
1. Hero: stacked name, second line stroke-outlined; two-column sub-block; cobalt eyebrow.
2. Ticker: black marquee strip under hero, duplicated content, translateX(-50%) loop, prefers-reduced-motion fallback.
3. Project index: full-width <details> rows, hairline ink borders, invert to solid cobalt + white on hover/open, giant Archivo Black titles.
4. Build grid: 2-col cards with 1px line gaps, cobalt-tint hover.
5. Section rhythm: 80px vertical padding, mono uppercase metadata right of headings.

NEVER: add CSS/JS frameworks or site generators; change colors, fonts, spacing, borders, animations; convert <details> rows to JS accordions; add dark mode or themes unless Jayrold explicitly asks; reformat CSS in ways that change rendered output.
ALWAYS: verify visual parity with before/after screenshots (1440px + 390px) after any change; ask before adding new visual elements.
