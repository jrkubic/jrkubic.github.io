# Site Redesign Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Rebuild jrkubic.github.io as a modern dark developer/terminal-styled single-page portfolio + services site in plain hand-editable HTML/CSS/JS.

**Architecture:** One scrolling `index.html` (hero → about → projects → work → papers → services → contact) backed by a single new stylesheet (`assets/css/style.css`, CSS custom properties) and a tiny vanilla JS file (`assets/js/main.js`). `software.html` and `contact.html` are restyled to match, keeping their working Formspree forms. No build step, no framework, no jQuery on new pages.

**Tech Stack:** Plain HTML5, CSS (custom properties, grid, flexbox), vanilla JS (IntersectionObserver), JetBrains Mono via Google Fonts, inline SVG icons, Formspree endpoint `xrbkwdla`, GitHub Pages.

**Design doc:** `docs/plans/2026-08-06-site-redesign-design.md`

**Branch:** All work happens on the `redesign` branch (already created). Do NOT push or merge to `main` — Jake reviews first.

**Deviation from design doc (flag to Jake at final review):** The design doc says to delete jQuery/Font Awesome/webfonts. But the parked pages `chicken.html` and `animals.html` (kept per Jake's request) load `assets/css/main.css`, `assets/css/fontawesome-all.min.css`, the webfonts, and the jQuery scripts. Deleting those would break the parked pages. This plan therefore deletes only truly-dead files (`assets/sass/`, `README.txt`) and leaves the legacy runtime assets in place, unreferenced by any new page. Zero user-facing weight cost — new pages never load them.

**Verification approach:** This is a static site — "tests" are rendered-page checks. After each page change: open the page via the Playwright MCP tools (`mcp__playwright__browser_navigate` to the `file:///` URL), take a screenshot, and read `mcp__playwright__browser_console_messages` expecting zero errors. No test framework is added.

---

### Task 1: New stylesheet

**Files:**
- Create: `assets/css/style.css`

**Step 1: Create `assets/css/style.css` with exactly this content**

```css
/* ============================================================
   jrkubic.github.io — hand-written stylesheet, no build step.
   All colors/fonts are design tokens below: tweak them here.
   ============================================================ */
:root {
  --bg: #0d1117;
  --bg-alt: #161b22;
  --bg-inset: #010409;
  --border: #30363d;
  --text: #c9d1d9;
  --text-dim: #8b949e;
  --accent: #3fb950;
  --accent-soft: rgba(63, 185, 80, 0.14);
  --red: #ff5f56;
  --yellow: #ffbd2e;
  --green: #27c93f;
  --mono: "JetBrains Mono", ui-monospace, SFMono-Regular, Consolas, "Liberation Mono", monospace;
  --sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  --maxw: 960px;
}

* { margin: 0; padding: 0; box-sizing: border-box; }

html { scroll-behavior: smooth; }

body {
  background: var(--bg);
  color: var(--text);
  font-family: var(--sans);
  font-size: 1rem;
  line-height: 1.65;
  -webkit-font-smoothing: antialiased;
}

a { color: var(--accent); text-decoration: none; }
a:hover { text-decoration: underline; }

img { max-width: 100%; }

.container { max-width: var(--maxw); margin: 0 auto; padding: 0 1.25rem; }

/* ---------- skip link ---------- */
.skip-link {
  position: absolute; left: -9999px; top: 0;
  background: var(--accent); color: var(--bg-inset);
  padding: 0.5rem 1rem; font-family: var(--mono); z-index: 100;
}
.skip-link:focus { left: 0; }

/* ---------- nav ---------- */
.site-nav {
  position: sticky; top: 0; z-index: 50;
  background: rgba(13, 17, 23, 0.92);
  backdrop-filter: blur(8px);
  border-bottom: 1px solid var(--border);
}
.site-nav .container {
  display: flex; align-items: center; justify-content: space-between;
  min-height: 3.25rem;
}
.brand {
  font-family: var(--mono); font-weight: 700; font-size: 0.95rem;
  color: var(--text);
}
.brand .prompt { color: var(--accent); }
.brand:hover { text-decoration: none; color: var(--accent); }
.nav-links {
  display: flex; gap: 1.1rem; list-style: none;
  font-family: var(--mono); font-size: 0.85rem;
}
.nav-links a { color: var(--text-dim); }
.nav-links a:hover { color: var(--accent); text-decoration: none; }
.nav-links a.active { color: var(--accent); }
.nav-toggle {
  display: none; background: none; border: 1px solid var(--border);
  border-radius: 6px; color: var(--text); font-family: var(--mono);
  font-size: 0.85rem; padding: 0.3rem 0.6rem; cursor: pointer;
}

/* ---------- terminal window (hero) ---------- */
.hero { padding: 3.5rem 0 1rem; }
.term {
  background: var(--bg-alt); border: 1px solid var(--border);
  border-radius: 10px; overflow: hidden;
  box-shadow: 0 16px 48px rgba(1, 4, 9, 0.55);
}
.term-bar {
  display: flex; align-items: center; gap: 0.45rem;
  background: var(--bg-inset); border-bottom: 1px solid var(--border);
  padding: 0.55rem 0.9rem;
}
.dot { width: 12px; height: 12px; border-radius: 50%; }
.dot.red { background: var(--red); }
.dot.yellow { background: var(--yellow); }
.dot.green { background: var(--green); }
.term-title {
  margin-left: 0.6rem; font-family: var(--mono);
  font-size: 0.75rem; color: var(--text-dim);
}
.term-body { padding: 1.5rem 1.75rem 1.75rem; }
.term-line { font-family: var(--mono); font-size: 0.9rem; color: var(--text-dim); margin: 0.9rem 0 0.35rem; }
.prompt { color: var(--accent); font-weight: 700; }
.term-body h1 { font-family: var(--mono); font-size: clamp(1.6rem, 4.5vw, 2.4rem); line-height: 1.2; }
.subtitle { color: var(--accent); font-family: var(--mono); font-size: clamp(0.95rem, 2.5vw, 1.1rem); margin-top: 0.25rem; }
.hero-links { display: flex; gap: 0.9rem; margin-top: 1.1rem; }
.hero-links a {
  display: inline-flex; align-items: center; gap: 0.4rem;
  color: var(--text-dim); font-family: var(--mono); font-size: 0.85rem;
}
.hero-links a:hover { color: var(--accent); text-decoration: none; }
.hero-links svg { width: 18px; height: 18px; fill: currentColor; }
.hero-actions { display: flex; flex-wrap: wrap; gap: 0.75rem; margin-top: 1.35rem; }

.cursor {
  display: inline-block; width: 0.55em; height: 1.1em;
  background: var(--accent); vertical-align: text-bottom;
  animation: blink 1.1s steps(1) infinite;
}
@keyframes blink { 50% { opacity: 0; } }

/* ---------- buttons ---------- */
.btn {
  display: inline-block; font-family: var(--mono); font-size: 0.9rem;
  padding: 0.55rem 1.1rem; border-radius: 6px;
  border: 1px solid var(--border); color: var(--text);
  background: var(--bg-alt); cursor: pointer;
}
.btn:hover { border-color: var(--accent); color: var(--accent); text-decoration: none; }
.btn.primary { background: var(--accent); border-color: var(--accent); color: var(--bg-inset); font-weight: 700; }
.btn.primary:hover { filter: brightness(1.1); color: var(--bg-inset); }

/* ---------- sections ---------- */
main section { padding: 3rem 0 0.5rem; }
.sec-title {
  font-family: var(--mono); font-size: 1.15rem; font-weight: 500;
  color: var(--text); margin-bottom: 1.25rem;
  padding-bottom: 0.5rem; border-bottom: 1px solid var(--border);
}
.sec-title .prompt { margin-right: 0.4rem; }
section p + p { margin-top: 0.85rem; }
.dim { color: var(--text-dim); }

/* ---------- project cards ---------- */
.card-grid {
  display: grid; grid-template-columns: repeat(auto-fill, minmax(270px, 1fr));
  gap: 1rem; margin-top: 1.25rem;
}
.card {
  background: var(--bg-alt); border: 1px solid var(--border);
  border-radius: 8px; padding: 1.1rem 1.2rem;
  display: flex; flex-direction: column; gap: 0.6rem;
}
.card:hover { border-color: var(--accent); }
.card h3 { font-family: var(--mono); font-size: 1rem; }
.card h3 a { color: var(--text); }
.card h3 a:hover { color: var(--accent); text-decoration: none; }
.card p { font-size: 0.92rem; color: var(--text-dim); flex-grow: 1; }
.badge {
  font-family: var(--mono); font-size: 0.7rem; color: var(--yellow);
  border: 1px solid var(--yellow); border-radius: 4px;
  padding: 0.05rem 0.4rem; margin-left: 0.5rem; vertical-align: middle;
}
.tags { display: flex; flex-wrap: wrap; gap: 0.4rem; list-style: none; }
.tags li {
  font-family: var(--mono); font-size: 0.72rem; color: var(--accent);
  background: var(--accent-soft); border-radius: 4px; padding: 0.12rem 0.5rem;
}
.card-links { display: flex; gap: 0.9rem; font-family: var(--mono); font-size: 0.82rem; }

/* ---------- work highlights ---------- */
.work-list { display: flex; flex-direction: column; gap: 1rem; margin-top: 1.25rem; }
.work-item {
  border-left: 3px solid var(--accent); background: var(--bg-alt);
  border-radius: 0 8px 8px 0; padding: 1rem 1.25rem;
}
.work-item h3 { font-family: var(--mono); font-size: 0.98rem; margin-bottom: 0.35rem; }
.work-item p { font-size: 0.93rem; color: var(--text-dim); }

/* ---------- papers / file list ---------- */
.file-list { list-style: none; margin-top: 1rem; }
.file-list li {
  display: flex; align-items: baseline; gap: 0.6rem;
  padding: 0.55rem 0; border-bottom: 1px dashed var(--border);
  font-family: var(--mono); font-size: 0.9rem;
}
.file-list .ext { color: var(--text-dim); font-size: 0.75rem; }

/* ---------- services ---------- */
.svc-grid {
  display: grid; grid-template-columns: repeat(auto-fill, minmax(230px, 1fr));
  gap: 0.6rem 1.5rem; list-style: none; margin: 1.1rem 0 1.5rem;
  font-family: var(--mono); font-size: 0.9rem;
}
.svc-grid li::before { content: "> "; color: var(--accent); font-weight: 700; }

/* ---------- forms ---------- */
form { margin-top: 1.25rem; }
.form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 0.8rem; margin-bottom: 0.8rem; }
.form-row.single { grid-template-columns: 1fr; }
input, textarea {
  width: 100%; background: var(--bg-alt); border: 1px solid var(--border);
  border-radius: 6px; color: var(--text); font-family: var(--mono);
  font-size: 0.9rem; padding: 0.6rem 0.75rem;
}
input:focus, textarea:focus { outline: none; border-color: var(--accent); }
::placeholder { color: var(--text-dim); }
textarea { resize: vertical; }

/* ---------- footer ---------- */
.site-footer {
  margin-top: 3.5rem; border-top: 1px solid var(--border);
  padding: 1.5rem 0 2rem; text-align: center;
  font-family: var(--mono); font-size: 0.8rem; color: var(--text-dim);
}

/* ---------- responsive ---------- */
@media (max-width: 700px) {
  .nav-toggle { display: block; }
  .nav-links {
    display: none; position: absolute; top: 100%; left: 0; right: 0;
    flex-direction: column; gap: 0; background: var(--bg-inset);
    border-bottom: 1px solid var(--border);
  }
  .nav-links.open { display: flex; }
  .nav-links li { border-top: 1px solid var(--border); }
  .nav-links a { display: block; padding: 0.8rem 1.25rem; }
  .form-row { grid-template-columns: 1fr; }
  .term-body { padding: 1.2rem 1.1rem 1.4rem; }
}

@media (prefers-reduced-motion: reduce) {
  html { scroll-behavior: auto; }
  .cursor { animation: none; }
}
```

**Step 2: Commit**

```powershell
git add assets/css/style.css
git commit -m "feat: add new dark terminal stylesheet"
```

---

### Task 2: New JavaScript

**Files:**
- Create: `assets/js/main.js`

**Step 1: Create `assets/js/main.js` with exactly this content**

```js
// Mobile nav toggle
var toggle = document.querySelector(".nav-toggle");
var links = document.querySelector(".nav-links");
if (toggle && links) {
  toggle.addEventListener("click", function () {
    links.classList.toggle("open");
  });
  links.addEventListener("click", function (e) {
    if (e.target.tagName === "A") links.classList.remove("open");
  });
}

// Scroll-spy: highlight the nav link for the section in view
var sections = document.querySelectorAll("main section[id]");
var navLinks = document.querySelectorAll('.nav-links a[href^="#"]');
if (sections.length && navLinks.length && "IntersectionObserver" in window) {
  var observer = new IntersectionObserver(
    function (entries) {
      entries.forEach(function (entry) {
        if (!entry.isIntersecting) return;
        navLinks.forEach(function (a) {
          a.classList.toggle(
            "active",
            a.getAttribute("href") === "#" + entry.target.id
          );
        });
      });
    },
    { rootMargin: "-40% 0px -55% 0px" }
  );
  sections.forEach(function (s) { observer.observe(s); });
}
```

**Step 2: Commit**

```powershell
git add assets/js/main.js
git commit -m "feat: add nav toggle and scroll-spy script"
```

---

### Task 3: Rewrite index.html

**Files:**
- Modify: `index.html` (full replacement)

**Step 1: (Optional, 1 fetch) Confirm movie-board-sync details**

WebFetch `https://github.com/jrkubic/movie-board-sync` and ask for the repo description and primary language. If it differs from the card text below, adjust the card's blurb/tags. If the fetch fails, keep the text below as-is.

**Step 2: Replace the entire contents of `index.html` with**

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<title>Jonathan "Jake" Kubicki — Platform Engineer</title>
	<meta charset="utf-8" />
	<meta name="viewport" content="width=device-width, initial-scale=1" />
	<meta name="description" content="Jonathan 'Jake' Kubicki — platform engineer building real-time, scalable systems. Projects, work highlights, papers, and software services." />
	<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' rx='16' fill='%230d1117'/%3E%3Ctext x='50' y='68' font-size='52' text-anchor='middle' fill='%233fb950' font-family='monospace'%3E%26gt;_%3C/text%3E%3C/svg%3E" />
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
	<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet" />
	<link rel="stylesheet" href="assets/css/style.css" />
</head>
<body>
	<a class="skip-link" href="#about">Skip to content</a>

	<nav class="site-nav" aria-label="Main">
		<div class="container">
			<a class="brand" href="#"><span class="prompt">jake@kubicki</span>:~$</a>
			<button class="nav-toggle" aria-label="Toggle navigation">menu</button>
			<ul class="nav-links">
				<li><a href="#about">about</a></li>
				<li><a href="#projects">projects</a></li>
				<li><a href="#work">work</a></li>
				<li><a href="#papers">papers</a></li>
				<li><a href="#services">services</a></li>
				<li><a href="#contact">contact</a></li>
			</ul>
		</div>
	</nav>

	<header class="hero">
		<div class="container">
			<div class="term">
				<div class="term-bar">
					<span class="dot red"></span><span class="dot yellow"></span><span class="dot green"></span>
					<span class="term-title">jake@kubicki: ~</span>
				</div>
				<div class="term-body">
					<p class="term-line"><span class="prompt">$</span> whoami</p>
					<h1>Jonathan "Jake" Kubicki</h1>
					<p class="subtitle">Platform Engineer — real-time, scalable systems</p>
					<p class="term-line"><span class="prompt">$</span> cat mission.txt</p>
					<p>I build data-driven platforms used in factory automation, national security, and consumer tech.</p>
					<div class="hero-links">
						<a href="https://github.com/jrkubic" aria-label="GitHub">
							<svg viewBox="0 0 16 16" aria-hidden="true"><path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27s1.36.09 2 .27c1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.01 8.01 0 0 0 16 8c0-4.42-3.58-8-8-8z"/></svg>
							github
						</a>
						<!-- TODO(jake): replace # with your LinkedIn profile URL -->
						<a href="#" aria-label="LinkedIn">
							<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M20.45 20.45h-3.56v-5.57c0-1.33-.02-3.04-1.85-3.04-1.85 0-2.14 1.45-2.14 2.94v5.67H9.34V9h3.42v1.56h.05c.48-.9 1.64-1.85 3.37-1.85 3.6 0 4.27 2.37 4.27 5.46v6.28zM5.34 7.43a2.06 2.06 0 1 1 0-4.13 2.06 2.06 0 0 1 0 4.13zM7.12 20.45H3.55V9h3.57v11.45zM22.22 0H1.77C.79 0 0 .77 0 1.72v20.55C0 23.22.79 24 1.77 24h20.45c.98 0 1.78-.78 1.78-1.73V1.72C24 .77 23.2 0 22.22 0z"/></svg>
							linkedin
						</a>
						<a href="mailto:jakekubicki2@gmail.com" aria-label="Email">
							<svg viewBox="0 0 24 24" aria-hidden="true"><path d="M20 4H4c-1.1 0-1.99.9-1.99 2L2 18c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 4-8 5-8-5V6l8 5 8-5v2z"/></svg>
							email
						</a>
					</div>
					<div class="hero-actions">
						<a class="btn primary" href="#projects">./view-projects</a>
						<a class="btn" href="#contact">./hire-me</a>
					</div>
					<p class="term-line"><span class="prompt">$</span> <span class="cursor"></span></p>
				</div>
			</div>
		</div>
	</header>

	<main>
		<section id="about">
			<div class="container">
				<h2 class="sec-title"><span class="prompt">$</span> cat about.txt</h2>
				<p>I'm an experienced platform engineer with a strong background in building real-time, scalable systems. Over the past 4+ years, I've developed and deployed data-driven platforms used in factory automation, national security, and consumer tech.</p>
				<p>I've led feature delivery from stakeholder conversations to front-end UX and backend architecture. I focus on designing efficient, scalable systems that reduce complexity, improve workflows, and deliver measurable results.</p>
			</div>
		</section>

		<section id="projects">
			<div class="container">
				<h2 class="sec-title"><span class="prompt">$</span> ls projects/</h2>
				<div class="card-grid">
					<article class="card">
						<h3><a href="https://github.com/jrkubic/movie-board-sync">movie-board-sync</a></h3>
						<p>Automated pipeline that syncs my Real-Debrid watch activity into a Google Sheets dashboard — 137+ titles tracked and enriched, hands-free.</p>
						<ul class="tags"><li>Python</li><li>Google Sheets API</li><li>Real-Debrid</li></ul>
						<div class="card-links"><a href="https://github.com/jrkubic/movie-board-sync">source ↗</a></div>
					</article>
					<article class="card">
						<h3>stremio-ratings<span class="badge">in progress</span></h3>
						<p>Public Stremio addon for rating titles 1–5★ without leaving the app — personal ratings, synced and queryable.</p>
						<ul class="tags"><li>Stremio SDK</li><li>Node.js</li></ul>
						<div class="card-links"><span class="dim">coming soon</span></div>
					</article>
					<!-- TODO(jake): add cards for additional repos / open-source contributions.
					     Copy an <article class="card"> block above and edit. -->
				</div>
			</div>
		</section>

		<section id="work">
			<div class="container">
				<h2 class="sec-title"><span class="prompt">$</span> cat work-highlights.md</h2>
				<div class="work-list">
					<article class="work-item">
						<h3>Factory automation platform</h3>
						<p>Built and deployed real-time, data-driven platforms for factory automation — reducing complexity and improving operator workflows. <span class="dim">[placeholder — Jake to supply scale/outcome detail]</span></p>
					</article>
					<article class="work-item">
						<h3>National security systems</h3>
						<p>Developed scalable backend systems in the national security space, with delivery led end-to-end from stakeholder requirements to production. <span class="dim">[placeholder — Jake to supply detail]</span></p>
					</article>
					<article class="work-item">
						<h3>Consumer tech</h3>
						<p>Shipped data-driven features for consumer products, spanning front-end UX through backend architecture. <span class="dim">[placeholder — Jake to supply detail]</span></p>
					</article>
				</div>
			</div>
		</section>

		<section id="papers">
			<div class="container">
				<h2 class="sec-title"><span class="prompt">$</span> ls papers/</h2>
				<p class="dim">Algorithm optimization &amp; analysis writeups.</p>
				<ul class="file-list">
					<!-- TODO(jake): drop PDFs into papers/ and update these entries -->
					<li><span class="prompt">-rw-</span> <span class="dim">algorithm-optimization-analysis.pdf — coming soon</span> <span class="ext">PDF</span></li>
				</ul>
			</div>
		</section>

		<section id="services">
			<div class="container">
				<h2 class="sec-title"><span class="prompt">$</span> ./services --list</h2>
				<p>Available for freelance and contract work:</p>
				<ul class="svc-grid">
					<li>Full Stack Development</li>
					<li>Backend Architecture</li>
					<li>IoT Solutions</li>
					<li>Security Audits</li>
					<li>Technical Maintenance</li>
					<li>At-Home Tech Support</li>
				</ul>
				<a class="btn primary" href="software.html">./start-a-project</a>
				<!-- Parked for later:
				<a href="animals.html">Animal Services</a>
				<a href="printing.html">3D Printing Services</a>
				-->
			</div>
		</section>

		<section id="contact">
			<div class="container">
				<h2 class="sec-title"><span class="prompt">$</span> ./contact</h2>
				<p>Always interested in new opportunities and meeting new people — don't hesitate to reach out.</p>
				<form method="post" action="https://formspree.io/f/xrbkwdla">
					<div class="form-row">
						<input type="text" name="name" placeholder="Name *" required />
						<input type="email" name="email" placeholder="Email *" required />
					</div>
					<div class="form-row">
						<input type="text" name="phone" placeholder="Phone" />
						<input type="text" name="company" placeholder="Company" />
					</div>
					<div class="form-row single">
						<input type="text" name="subject" placeholder="Subject *" value="General Inquiry: " required />
					</div>
					<div class="form-row single">
						<textarea name="message" placeholder="Message *" rows="6" required></textarea>
					</div>
					<button class="btn primary" type="submit">./send-message</button>
				</form>
			</div>
		</section>
	</main>

	<footer class="site-footer">
		<div class="container">
			<p>&copy; 2026 Jonathan Kubicki · hand-built with plain HTML/CSS · <a href="https://github.com/jrkubic/jrkubic.github.io">view source</a></p>
		</div>
	</footer>

	<script src="assets/js/main.js"></script>
</body>
</html>
```

**Step 3: Verify rendering**

- `mcp__playwright__browser_navigate` → `file:///c:/Projects/Jake_Site/jrkubicsite/index.html`
- `mcp__playwright__browser_take_screenshot` — expect: dark page, terminal hero, all six sections visible while scrolling.
- `mcp__playwright__browser_console_messages` — expect: no errors (a favicon or font warning from `file://` is acceptable).
- Click each nav link (`mcp__playwright__browser_click`) — expect scroll to matching section.

**Step 4: Commit**

```powershell
git add index.html
git commit -m "feat: rebuild index as dark terminal single-page portfolio"
```

---

### Task 4: Mobile check for index

**Step 1: Resize and screenshot**

- `mcp__playwright__browser_resize` → width 375, height 812
- `mcp__playwright__browser_navigate` → same `file:///` URL
- `mcp__playwright__browser_take_screenshot`

Expected: single column; nav collapsed to `menu` button; cards stacked; no horizontal scrollbar.

**Step 2: Test the mobile menu**

Click the `menu` button → nav links appear stacked; click `projects` → menu closes and page scrolls.

**Step 3: Fix any layout breaks found, re-screenshot, then commit fixes (skip commit if no changes)**

```powershell
git add -A
git commit -m "fix: mobile layout adjustments"
```

---

### Task 5: Restyle software.html

**Files:**
- Modify: `software.html` (full replacement)

**Step 1: Replace the entire contents of `software.html` with**

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<title>Software Services — Jonathan Kubicki</title>
	<meta charset="utf-8" />
	<meta name="viewport" content="width=device-width, initial-scale=1" />
	<meta name="description" content="Software and technical services from Jonathan 'Jake' Kubicki — full-stack development, backend architecture, IoT, security audits." />
	<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' rx='16' fill='%230d1117'/%3E%3Ctext x='50' y='68' font-size='52' text-anchor='middle' fill='%233fb950' font-family='monospace'%3E%26gt;_%3C/text%3E%3C/svg%3E" />
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
	<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet" />
	<link rel="stylesheet" href="assets/css/style.css" />
</head>
<body>
	<nav class="site-nav" aria-label="Main">
		<div class="container">
			<a class="brand" href="index.html"><span class="prompt">jake@kubicki</span>:~$</a>
			<button class="nav-toggle" aria-label="Toggle navigation">menu</button>
			<ul class="nav-links">
				<li><a href="index.html">home</a></li>
				<li><a href="index.html#projects">projects</a></li>
				<li><a href="software.html" class="active">services</a></li>
				<li><a href="contact.html">contact</a></li>
			</ul>
		</div>
	</nav>

	<main>
		<section>
			<div class="container">
				<h2 class="sec-title"><span class="prompt">$</span> ./services --software</h2>
				<p>I'm an experienced software developer with a strong background in building real-time, scalable systems. I offer a range of technical services to help businesses and individuals leverage technology effectively.</p>
				<ul class="svc-grid">
					<li>Full Stack Development</li>
					<li>Backend Architecture</li>
					<li>IoT Solutions</li>
					<li>Security Audits</li>
					<li>Technical Maintenance</li>
					<li>At-Home Tech Support</li>
				</ul>

				<h2 class="sec-title"><span class="prompt">$</span> ./start-a-project</h2>
				<form method="post" action="https://formspree.io/f/xrbkwdla">
					<div class="form-row">
						<input type="text" name="name" placeholder="Name *" required />
						<input type="email" name="email" placeholder="Email *" required />
					</div>
					<div class="form-row single">
						<input type="text" name="subject" placeholder="Subject *" value="Software Services Inquiry: " required />
					</div>
					<div class="form-row single">
						<textarea name="message" placeholder="Describe your project or technical needs... *" rows="6" required></textarea>
					</div>
					<button class="btn primary" type="submit">./submit-inquiry</button>
				</form>
			</div>
		</section>
	</main>

	<footer class="site-footer">
		<div class="container">
			<p>&copy; 2026 Jonathan Kubicki · <a href="index.html">back to home</a></p>
		</div>
	</footer>

	<script src="assets/js/main.js"></script>
</body>
</html>
```

**Step 2: Verify** — navigate to `file:///c:/Projects/Jake_Site/jrkubicsite/software.html`, screenshot, console clean, form fields present with Formspree action.

**Step 3: Commit**

```powershell
git add software.html
git commit -m "feat: restyle software services page to match redesign"
```

---

### Task 6: Restyle contact.html

**Files:**
- Modify: `contact.html` (full replacement)

**Step 1: Replace the entire contents of `contact.html` with**

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<title>Contact — Jonathan Kubicki</title>
	<meta charset="utf-8" />
	<meta name="viewport" content="width=device-width, initial-scale=1" />
	<meta name="description" content="Contact Jonathan 'Jake' Kubicki." />
	<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' rx='16' fill='%230d1117'/%3E%3Ctext x='50' y='68' font-size='52' text-anchor='middle' fill='%233fb950' font-family='monospace'%3E%26gt;_%3C/text%3E%3C/svg%3E" />
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
	<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet" />
	<link rel="stylesheet" href="assets/css/style.css" />
</head>
<body>
	<nav class="site-nav" aria-label="Main">
		<div class="container">
			<a class="brand" href="index.html"><span class="prompt">jake@kubicki</span>:~$</a>
			<button class="nav-toggle" aria-label="Toggle navigation">menu</button>
			<ul class="nav-links">
				<li><a href="index.html">home</a></li>
				<li><a href="index.html#projects">projects</a></li>
				<li><a href="software.html">services</a></li>
				<li><a href="contact.html" class="active">contact</a></li>
			</ul>
		</div>
	</nav>

	<main>
		<section>
			<div class="container">
				<h2 class="sec-title"><span class="prompt">$</span> ./contact</h2>
				<p>Always interested in new opportunities and meeting new people — don't hesitate to reach out.</p>
				<form method="post" action="https://formspree.io/f/xrbkwdla">
					<div class="form-row">
						<input type="text" name="name" placeholder="Name *" required />
						<input type="email" name="email" placeholder="Email *" required />
					</div>
					<div class="form-row">
						<input type="text" name="phone" placeholder="Phone" />
						<input type="text" name="company" placeholder="Company" />
					</div>
					<div class="form-row single">
						<input type="text" name="subject" placeholder="Subject *" value="General Inquiry: " required />
					</div>
					<div class="form-row single">
						<textarea name="message" placeholder="Message *" rows="6" required></textarea>
					</div>
					<button class="btn primary" type="submit">./send-message</button>
				</form>
			</div>
		</section>
	</main>

	<footer class="site-footer">
		<div class="container">
			<p>&copy; 2026 Jonathan Kubicki · <a href="index.html">back to home</a></p>
		</div>
	</footer>

	<script src="assets/js/main.js"></script>
</body>
</html>
```

**Step 2: Verify** — navigate to the `file:///` URL, screenshot, console clean.

**Step 3: Commit**

```powershell
git add contact.html
git commit -m "feat: restyle contact page to match redesign"
```

---

### Task 7: Remove dead template files

**Files:**
- Delete: `assets/sass/` (entire folder), `README.txt`
- Keep (parked pages depend on them): `assets/css/main.css`, `assets/css/fontawesome-all.min.css`, `assets/webfonts/`, `assets/js/jquery.min.js`, `assets/js/jquery.scrollex.min.js`, `assets/js/jquery.scrolly.min.js`, `assets/js/browser.min.js`, `assets/js/breakpoints.min.js`, `assets/js/util.js`, `LICENSE.txt`, `images/`, `chicken.html`, `animals.html`, `google2a837fe0ad3d1b38.html`

**Step 1: Delete**

```powershell
git rm -r assets/sass
git rm README.txt
```

**Step 2: Verify no new page references deleted or legacy assets**

Grep `index.html`, `software.html`, `contact.html` for `sass`, `jquery`, `main.css`, `fontawesome` — expect zero matches.

**Step 3: Commit**

```powershell
git commit -m "chore: remove unused SASS sources and template readme"
```

---

### Task 8: Final QA sweep

**Step 1: Full-page checks on all three new pages**

For each of `index.html`, `software.html`, `contact.html` at desktop (1280px) and mobile (375px):
- Navigate, screenshot, console-messages check (no errors).

**Step 2: Link inventory**

Verify every `href` in the three new pages resolves:
- internal anchors (`#about` … `#contact`) exist as element ids
- `software.html`, `contact.html`, `index.html` cross-links exist
- external: `github.com/jrkubic`, `github.com/jrkubic/movie-board-sync`, Formspree action URLs, `mailto:`
- The only intentional dead link is the LinkedIn `#` placeholder (TODO for Jake).

**Step 3: Confirm branch state**

```powershell
git log --oneline main..redesign
git status --short
```

Expected: clean tree; commits from Tasks 1–7 present. Do NOT push or merge.

**Step 4: Report to Jake**

Present desktop + mobile screenshots of all three pages, list the placeholder TODOs (LinkedIn URL, paper PDFs, work-highlight details, extra project cards), and flag the Task-7 deviation (legacy assets kept for parked pages). Jake approves → merge `redesign` into `main` and push to deploy.
