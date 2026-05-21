# AUDIT.md

## Executive Summary

`mywebsite` is a small, static bilingual (EN/FR) personal portfolio served as two standalone HTML files. The structure is simple and works, but there is significant duplication between `index.html` and `index-fr.html`, several HTML correctness issues, and a few user-experience and privacy concerns worth addressing before promoting the site as a professional portfolio.

To answer the focus questions briefly:

- **GitHub Pages vs VPS**: For a static portfolio, GitHub Pages is free, fast (CDN-backed), HTTPS by default, and entirely sufficient. A VPS only becomes useful if you need server-side code, databases, custom auth, or full control over the stack. Recommendation: **stay on GitHub Pages** until you have a concrete reason to move.
- **More pages, animations, etc. on GitHub Pages?**: Yes — GitHub Pages serves any static assets (HTML/CSS/JS, images, videos, WebGL, fonts). You can add multi-page navigation, CSS animations, JS interactivity, even SPAs (React/Vue/Astro) via a build step. The only real limitations: no server-side code and a soft 1 GB / 100 GB-month bandwidth budget.
- **Privacy**: A portfolio should reveal *enough to be credible*, not your full identity. Avoid full address, personal phone, birthdate, photo of family, etc. A first name + professional domain + LinkedIn/GitHub link + contact form is enough.
- **Useful or useless?**: Useful **if** it shows concrete artifacts (projects, code, write-ups). A portfolio that only lists tools and interests is weaker than one well-documented project. Lean toward fewer pages with more substance.

## Findings (by severity)

### 🔴 High — Privacy / professional credibility
- Both pages list **"28.5 years of experience"** combined with a first name and a public GitHub handle. This is enough to identify you and infer your age. Decide intentionally what level of disclosure you want.
- The portfolio currently lists **tools and interests but no shipped work**. Recruiters skim for evidence (repos, demos, screenshots, write-ups). The dynamic GitHub repo list helps, but it will show *every* repo including experiments.

### 🔴 High — HTML correctness in `index.html`
- The `<script>` that fetches GitHub repos is placed **inside `<head>` before the target `<div id="github-projets">` exists**. On a fast network the fetch resolves after DOM is ready, so it usually works, but it's fragile. Move it to the end of `<body>` or wrap in `DOMContentLoaded`.
- The form `<form action="https://formspree.io/f/mjgewrld">` is **outside any section** and uses French placeholders on the English page (`"Ton nom"`, `"Pourquoi tu m'écris ?"`). Inconsistent UX.
- The closing structure has stray blank lines and the `<div id="github-projets">` is not wrapped in a `<section>` or under a heading — visually it appears with no context.

### 🟠 Medium — Massive duplication between `index.html` and `index-fr.html`
- The CSS block (~30 lines) is copy-pasted. Any color tweak requires editing two files.
- Content structure is duplicated. A small refactor (extract CSS to `styles.css`) would already help; longer term consider a static site generator (Eleventy, Astro, Hugo) or even a simple JS i18n object.

### 🟠 Medium — Image asset confusion
- `index.html` references `watermarked-00008-2970267479.png`, while `index-fr.html` references `00008-2970267479.png` (no watermark). Why does the French version omit the watermark? If watermarks are intentional, both versions should use them.
- Filenames like `00008-2970267479.png` suggest raw Stable Diffusion outputs. Rename to semantic names (`learning-ai.png`, `studies.png`, `values.png`) for SEO, accessibility, and maintainability.

### 🟠 Medium — Accessibility & SEO
- Missing `<meta name="description">` on both pages.
- Missing Open Graph / Twitter card tags (no preview when shared).
- No `favicon.ico` link.
- No `lang`-switch with `hreflang` (`<link rel="alternate" hreflang="fr" href="index-fr.html">` and vice-versa) — useful for search engines and for users.
- Color contrast: neon `#00ff88` on `#0a0a0f` is fine, but the body text `#e0e0e0` is OK; verify with a contrast checker. The neon glow on h1/h2 may reduce readability for some users — consider `prefers-reduced-motion`/contrast handling.
- Form inputs lack `<label>` elements (placeholders are not a substitute).

### 🟡 Low — Minor issues
- No `README.md` describing the project.
- No `LICENSE` (probably fine for a personal site, but explicit is better).
- `Title` is generic: `"Florence - My Site"`. Prefer `"Florence — Electrical Engineer & AI Tinkerer"` for SEO.
- The GitHub fetch shows the latest 5 *updated* repos, which may include throwaway test repos. Filter by topic or pin specific repo names.
- Inline `<style>` blocks should move to an external stylesheet to leverage browser caching.
- Form `<textarea>` has no `required` attribute but the other two fields do — inconsistent.

## Concrete Recommendations

### 1. Fix the script placement (`index.html`)
Move the `<script>` block from inside `<head>` to just before `</body>`, **after** the target div. Or:
```html
<script>
  document.addEventListener('DOMContentLoaded', () => {
    fetch('https://api.github.com/users/FlyingGull/repos?sort=updated&per_page=5')
      // ...
  });
</script>
```

### 2. Extract shared CSS
Create `styles.css` and replace both `<style>` blocks with:
```html
<link rel="stylesheet" href="styles.css">
```

### 3. Add proper meta tags (both files)
```html
<meta name="description" content="Florence — Electrical engineer exploring AI, local LLMs and power-grid optimization.">
<link rel="alternate" hreflang="en" href="https://flyinggull.github.io/mywebsite/index.html">
<link rel="alternate" hreflang="fr" href="https://flyinggull.github.io/mywebsite/index-fr.html">
<link rel="icon" href="favicon.ico">
<meta property="og:title" content="Florence — Portfolio">
<meta property="og:description" content="Electrical engineer exploring AI and grid optimization.">
<meta property="og:image" content="https://flyinggull.github.io/mywebsite/preview.png">
```

### 4. Localize the contact form on the English page
In `index.html`:
```html
<input type="text" name="name" placeholder="Your name" required>
<input type="email" name="email" placeholder="Your email" required>
<textarea name="message" placeholder="What brings you here?" rows="4" required></textarea>
<button type="submit">Send</button>
```
Add `<label>` elements for accessibility.

### 5. Add at least one real project section
Replace "My Web Page, Continuous Learning in AI" in `index.html` with a featured project card: short paragraph, screenshot, link to the repo, technologies used, what you learned. Even one solid case study outweighs a long list of tools.

### 6. Decide on the disclosure level
- If you want anonymity: drop "28.5 years of experience" and replace with "Senior electrical engineer".
- If you want credibility: add LinkedIn + a CV download, and keep the experience metric.

### 7. Rename images and unify watermarking
Either watermark both language versions or neither. Rename to descriptive filenames and update `alt` text accordingly.

### 8. Add a `README.md`
Briefly state: what the site is, how to preview locally, how to deploy (GitHub Pages settings → branch `main` → `/` root).

## Next-Steps Checklist

- [ ] Move GitHub-fetch `<script>` to end of `<body>` or wrap in `DOMContentLoaded`
- [ ] Translate the contact form placeholders in `index.html`
- [ ] Add `required` to the textarea consistently
- [ ] Add `<label>` elements for all form fields
- [ ] Extract inline `<style>` to `styles.css`
- [ ] Add `<meta name="description">`, Open Graph tags, favicon, and `hreflang` cross-links
- [ ] Decide on watermark policy; unify image filenames between EN/FR
- [ ] Replace placeholder project text with at least one real, documented project
- [ ] Review what personal information you really want public (experience years, photos, location)
- [ ] Add a `README.md` and (optionally) a `LICENSE`
- [ ] Set a more descriptive `<title>` on both pages
- [ ] Filter the dynamic GitHub repo list (e.g., by topic `portfolio`) so only intentional projects appear
- [ ] Run the site through Lighthouse (Performance, Accessibility, SEO) and fix any quick wins
- [ ] Keep hosting on **GitHub Pages** — no VPS needed for this use case