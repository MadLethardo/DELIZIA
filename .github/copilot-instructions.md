# Repository instructions for Copilot sessions

## Purpose
Guide Copilot to provide accurate, context-aware assistance for DELIZIA, a static single-page website (HTML/CSS/JS) with no build toolchain or package manager. Responses should be tailored to this architecture and follow repository conventions.

## Build / Test / Lint commands
- **Architecture**: This is a static single-page site. No package.json, pyproject.toml, or build scripts present.
- **Preview locally** (before pushing changes):
  - Recommended: Run an HTTP server from the repo root to properly serve static assets and detect any XHR/fetch issues:
    - Python: `python -m http.server 8000`
    - Node (if installed): `npx http-server -p 8000`
  - Opening index.html directly via file:// protocol is acceptable only for static-only changes without XHR/fetch operations.
- **Testing**: None. No test framework or lint pipeline exists for this repository.
- **Coding style**: Use 2-space indentation, single quotes in JavaScript, and semicolons. Write clean, vanilla JavaScript without polyfills for IE11.

## High-level architecture
- Single-page static site (root-level index.html) with separate stylesheet (index.css) and JavaScript (index.js).
- Third-party UI libraries loaded via CDN (Swiper for carousels, Remix Icons). No package manager/dependency manifest present.
- Assets live under `RESOURCES/IMAGES/` — images, icons, and blobs used by the page.
- index.js contains UI behaviors: mobile nav show/hide, Swiper initializations, scroll-based header style changes, and thumbs/pagination logic for product carousels.
- **Browser support**: Target evergreen Chrome, Firefox, Safari, and Edge (latest versions) and mobile WebKit/Chromium. Do not add polyfills for IE11. If a change requires modern APIs, include feature-detection fallbacks.
- **CDN fallbacks**: If a CDN resource (Swiper, Remix Icons) fails to load, detect missing globals (e.g., `typeof Swiper === 'undefined'`) and gracefully disable carousel features, replace icons with inline SVG placeholders, and log a clear console warning.
- **Error handling in index.js**: Before initializing Swiper or accessing DOM elements, check if they exist. If a required element is not found (querySelector returns null), skip that behavior and log a descriptive `console.warn`. Example: `const el = document.querySelector(...); if (!el) { console.warn('Missing #nav-toggle — skipping nav init'); return; }`

## Key conventions and patterns
**Priority order when editing files:**
1. Preserve JS-linked IDs and classes — if you rename any ID or class that `index.js` references, update `index.js` in the same change. Run a global search in the repo first.
2. Preserve ARIA attributes — accessibility takes precedence over minor style updates.
3. Follow BEM naming for new classes.
4. Update multiple affected files (HTML/CSS/JS) in the same commit.

**Naming conventions:**
- **BEM classes**: Use `block__element` and `block__element--modifier` format. All lowercase with double underscore for elements and double hyphen for modifiers (e.g., `product__card`, `product__card--featured`, `home__data`).
- **IDs**: All IDs must be kebab-case (lowercase with hyphens). Examples: `home`, `about`, `product`, `nav-toggle`. Renaming any ID requires updating `index.js` references. Perform a global search for the ID in the repo and update all occurrences.

**Images and performance:**
- Images currently use `loading="eager"`. Change images below the initial viewport and decorative images (icons, thumbnails not in first-screen view) to `loading="lazy"`. Keep hero and above-the-fold images as `loading="eager"`.
- Performance targets: aim for LCP < 2.5s on 3G simulated mobile. When making changes, include before/after Lighthouse scores and note any regressions.

**Swiper usage:**
- Home uses a creative effect swiper (`.home__swiper`) initialized in `index.js`.
- Product tabs use a thumbed swiper pattern: `.product__tabs` (thumbs) and `.product__content` (main content).
- When adding slides, keep these classes unchanged: `.home__swiper`, `.product__tabs`, `.product__content`, `.swiper-slide`, `.swiper-wrapper`, `.swiper-container`. Do not rename these classes without updating `index.js` and CSS.

**Accessibility:**
- Preserve existing ARIA attributes and add appropriate ARIA roles/labels for new controls. For each interactive element, add an `aria-label` or `aria-labelledby` and an appropriate `role` (e.g., `role="button"`).
- Test assumptions against the current state of `index.html`, `index.css`, and `index.js` before proposing changes.

## Where to change common things
- Content/markup: `index.html`
- Styles: `index.css`
- Behavior: `index.js`
- Images and media: `RESOURCES/IMAGES/`

## CI / AI assistant configs found
- No existing Copilot/AI assistant config files detected (CLAUDE.md, .cursorrules, AGENTS.md, .windsurfrules, CONVENTIONS.md, etc.).

## Default focus
Unless the user specifies otherwise, prioritize **accessibility** in all recommendations and code changes. When suggesting modifications, always:
- Verify that existing ARIA labels and semantic HTML are preserved or enhanced.
- Ensure interactive elements remain keyboard-accessible.
- Test assumptions against the current state of index.html, index.css, and index.js before proposing changes.

(End of copilot-instructions.md)