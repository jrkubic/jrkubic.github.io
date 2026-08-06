# Site Redesign — Design Doc

**Date:** 2026-08-06
**Site:** https://jrkubic.github.io (repo: jrkubic/jrkubic.github.io)
**Status:** Approved by Jake

## Goal

Replace the 2015-era HTML5 UP "Read Only" template with a modern, dark
developer/terminal-styled site that serves two purposes with equal billing:

1. **Job-search portfolio** — projects, work case studies, papers, for recruiters
   and hiring managers.
2. **Freelance services hub** — service offerings and inquiry form for clients.

## Constraints & decisions

- **Plain hand-editable HTML/CSS/JS.** No build step, no framework, no jQuery.
  Adding a project later = copy-pasting a ~10-line card.
- **Dark-only, terminal aesthetic.** GitHub-dark palette: near-black background
  (`#0d1117`), soft gray text, terminal-green accent. JetBrains Mono (Google
  Fonts) for headings/nav/tags; system sans for body text. Section headings
  styled as shell prompts (`$ cat about.txt`). No fake typing delays or gimmicks
  that block content.
- **Keep working Formspree forms** (endpoint `xrbkwdla`) on software.html,
  contact.html, and embedded on index.
- **Social links:** GitHub (jrkubic), LinkedIn, email (jakekubicki2@gmail.com)
  only. Dead Twitter/Facebook/Instagram links removed.
- **Parked for later:** animals.html, chicken.html stay in repo, unlinked.
  Animal/3D-print service entries stay as HTML comments. Google site
  verification file stays.

## Structure: single page + detail pages

`index.html` sections, in order:

1. **Hero** — terminal-window card (`jake@kubicki:~$`), name, "Platform
   Engineer — real-time, scalable systems", blinking cursor, icon links
   (GitHub/LinkedIn/email), buttons `./view-projects` and `./hire-me`.
2. **`$ cat about.txt`** — existing bio, tightened, typos fixed.
3. **`$ ls projects/`** — card grid. Launch content: movie-board-sync (GitHub
   link + outcome blurb), Stremio ratings addon `[in progress]`. Slots for
   additional repos / open-source contributions Jake supplies. Cards: name,
   one-liner, monospace tech tags, links. Optional deep-dive pages under
   `projects/`.
4. **`$ cat work-highlights.md`** — three no-code case studies: factory
   automation, national security, consumer tech. Outcome-first, with
   `[placeholder — Jake to supply detail]` markers.
5. **`$ ls papers/`** — algorithm-optimization analysis papers, PDF links into
   `papers/` (files added when supplied).
6. **`$ ./services`** — blurb with the six expertise areas + button to
   software.html (full inquiry form).
7. **`$ ./contact`** — embedded Formspree contact form; contact.html stays for
   direct links, restyled.
8. **Footer** — copyright + "hand-built with plain HTML/CSS".

## Files

| File | Action |
|---|---|
| `index.html` | Rewrite as single scrolling page |
| `software.html`, `contact.html` | Restyle, keep forms |
| `assets/css/style.css` | New single stylesheet, CSS custom properties |
| `assets/js/main.js` | New: mobile nav toggle, scroll-spy, hero cursor |
| `projects/`, `papers/` | New folders for detail pages / PDFs |
| jQuery, scrollex, scrolly, breakpoints, browser.min, SASS, Font Awesome | Delete (~500KB); icons become inline SVG |
| `animals.html`, `chicken.html`, google verification | Keep, unlinked |

## Testing & rollout

- All work on `redesign` branch; live site untouched until merge.
- Preview via local HTTP server; Playwright screenshots at desktop + mobile
  widths, iterate, show Jake.
- Verify nav anchors, Formspree form markup (no test submissions), no broken
  links, no references to deleted assets.
- Jake reviews on branch → merge to `main` → GitHub Pages deploys.

## Content Jake supplies later

- LinkedIn profile URL
- Paper PDFs → `papers/`
- Work-highlight specifics (scale, outcomes)
- Additional repos + open-source contributions for project cards
