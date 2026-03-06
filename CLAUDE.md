# CLAUDE.md — true-shuffle-site

## Project Overview

**true-shuffle** is a landing page for a music shuffle service that plays every song in a playlist exactly once in a truly random order (Fisher–Yates algorithm), solving the problem of streaming platforms repeating tracks or skipping songs.

- Live: https://mikamcflurry.github.io/true-shuffle-site/
- Status: Beta / waitlist phase
- Company: true-shuffle GmbH, Bayreuth, Germany

---

## Tech Stack

**Zero dependencies. Pure HTML/CSS/JavaScript.** No build tools, no npm, no package managers, no frameworks.

| Layer | Technology |
|---|---|
| Markup | Vanilla HTML5 |
| Styling | Inline CSS with CSS custom properties (variables) |
| Scripting | Vanilla JavaScript (ES6+), no libraries |
| Backend | Google Apps Script Web App (for forms & analytics) |
| Hosting | GitHub Pages |

---

## Repository Structure

```
true-shuffle-site/
├── index.html          # German landing page (primary, lang="de")
├── en.html             # English landing page (lang="en")
├── index beta.html     # Work-in-progress / staging version
├── robots.txt          # Allows all crawlers, references sitemap
├── sitemap.xml         # Two URLs: index.html and en.html
├── README.md           # Project overview
├── CLAUDE.md           # This file
└── assets/
    ├── favicon.png
    ├── logo_dark.png           # Header/footer logo (dark mode)
    ├── logo_light.png          # Header/footer logo (light mode)
    ├── brand_logo_loop.gif     # Hero animated logo (dark mode)
    ├── brand_logo_loop_light.gif # Hero animated logo (light mode)
    ├── hero_phone_dark.png     # Hero phone mockup (dark mode)
    ├── hero_phone_light.png    # Hero phone mockup (light mode)
    ├── og_true_shuffle_1200x630.png  # Open Graph / Twitter Card image
    ├── product_01_one-tap.PNG  # Product screenshot slider image 1
    ├── product_02_features-card.PNG  # Product screenshot slider image 2
    ├── product_03_comparison.PNG     # Product screenshot slider image 3
    ├── product_04_10000-tracks.PNG   # Product screenshot slider image 4
    ├── true-shuffel_dark.svg         # Legacy SVG logo (dark)
    ├── true-shuffel_dark_1024.png    # Legacy PNG logo (dark, 1024px)
    ├── true-shuffel_icon_dark.svg    # Legacy icon SVG (dark)
    └── true-shuffel_icon_dark_512.png # Legacy icon PNG (dark, 512px)
```

---

## HTML Page Structure

Both `index.html` (DE) and `en.html` (EN) share the same layout and JavaScript logic. They differ only in language (text content, `lang` attribute, hreflang, canonical URLs, and locale-specific strings).

### Sections (in document order)

1. **`<head>`** — Meta tags, SEO, Open Graph, Twitter Card, JSON-LD structured data, all inline CSS
2. **`<header>`** — Sticky nav with logo, language switcher, dark/light theme toggle, mobile menu button, CTA button
3. **`#drawer`** — Mobile slide-in navigation menu
4. **Hero section** — Animated logo GIF + headline + sub-headline + CTA button
5. **Features section** — Feature cards (no-repeat shuffle, play all tracks, streaming service compatibility)
6. **`#impressions`** — Product screenshot slider (swipeable, dot navigation, arrow buttons)
7. **`#beta`** — Beta waitlist signup form (name, email, GDPR consent checkbox)
8. **Reviews section** — Star-rating form + dynamically loaded reviews list
9. **Contact section** — Contact form (name, email, message, GDPR consent)
10. **`<footer>`** — Logo, nav links, legal links (Impressum, Datenschutz, Cookies), copyright, cookie reset link
11. **Legal modal (`#legalModal`)** — Overlay for Impressum, Datenschutz, Cookies content (rendered inline)
12. **Cookie banner (`#cookieBanner`)** — Shown on first visit, acknowledged via localStorage

---

## CSS Architecture

All CSS is inlined in `<style>` blocks in each HTML file (no external stylesheets). Uses CSS custom properties for theming.

### CSS Variables (`:root` = dark mode, `body.light` = light mode)

| Variable | Dark | Light | Purpose |
|---|---|---|---|
| `--bg` | `#24252a` | `#f7f7f7` | Page background |
| `--bg2` | `#17181c` | `#ffffff` | Secondary background (gradient end) |
| `--panel` | `rgba(255,255,255,.06)` | `rgba(0,0,0,.05)` | Card/panel backgrounds |
| `--stroke` | `rgba(255,255,255,.12)` | `rgba(0,0,0,.12)` | Borders |
| `--text` | `rgba(255,255,255,.92)` | `rgba(0,0,0,.90)` | Primary text |
| `--muted` | `rgba(255,255,255,.72)` | `rgba(0,0,0,.70)` | Secondary text |
| `--muted2` | `rgba(255,255,255,.56)` | `rgba(0,0,0,.55)` | Tertiary text |
| `--shadow` | `0 18px 55px rgba(0,0,0,.55)` | lighter variant | Box shadows |
| `--radius` | `18px` | same | Standard border radius |
| `--radius2` | `26px` | same | Large border radius |
| `--max` | `1120px` | same | Max content width |
| `--ring` | white-tinted box-shadow | black-tinted | Focus rings |
| `--btnh` | `44px` | same | Standard button height |

### Key Layout Classes

- `.wrap` — Full-width flex row centring `.container`
- `.container` — `min(1120px, 100% - 40px)` with auto margins
- `.btn` — Base button style
- `.btn-primary` — Accent button (white background with dark text in dark mode)
- `.slide`, `.slide-media` — Product image slider cells
- `.review` — Individual review card
- `.modal`, `.modal-panel` — Legal information overlay

---

## JavaScript Architecture

All JavaScript is inlined at the bottom of each HTML file (before `</body>`). No modules, no bundling.

### Key Constants

```js
const WEBAPP_URL = "https://script.google.com/macros/s/.../exec"; // Google Apps Script endpoint
const PRIVACY_VERSION = "2026-02-21";
const LOGO_DARK = "assets/logo_dark.png";
const LOGO_LIGHT = "assets/logo_light.png";
const HERO_MEDIA_DARK = "assets/brand_logo_loop.gif";
const HERO_MEDIA_LIGHT = "assets/brand_logo_loop_light.gif";
const PRODUCT_IMAGES = ["assets/product_01_one-tap.PNG", ...]; // slider images
```

### Features Implemented in JS

| Feature | Description |
|---|---|
| **Theme toggle** | Dark/light mode via `body.classList.toggle("light")`. Preference stored in `localStorage` key `true_shuffle_theme`. Respects `prefers-color-scheme` on first visit. Swaps logo and hero GIF src. |
| **Mobile menu** | `#menuBtn` toggles `#drawer` open/close, sets `aria-expanded`. |
| **Product slider** | `buildSlides()` creates slide elements from `PRODUCT_IMAGES`. Arrow buttons (`#prevSlide`, `#nextSlide`) and dot navigation. Swipe/drag supported via `pointerdown`/`pointerup` events (threshold: 40px). |
| **Beta signup form** | Posts to `WEBAPP_URL` via iframe trick (form `target` attribute). Handles response via `postMessage`. 12-second fallback timer for slow connections. |
| **Review form** | Same pattern as beta form. On success, calls `loadReviews()` after 700ms delay. |
| **Contact form** | Same pattern as beta/review forms. |
| **Review loading** | JSONP via dynamically injected `<script>` tag. Calls `WEBAPP_URL?action=reviews&callback=tsReviewsCb`. 2.5-second timeout for load failure detection. |
| **Analytics / event tracking** | `trackEvent(name, props)` — privacy-friendly, no cookies. Respects `Do Not Track` header. Uses `navigator.sendBeacon` with fetch fallback. Session ID generated once per page load (`Math.random() + Date.now()`). Tracked events: `cta_join_beta_click`, `impressions_view`, `signup_success`. |
| **Cookie banner** | Shown when `localStorage.getItem("ts_cookie_ack_public") !== "1"`. Dismissed by clicking OK. Reset via footer link. |
| **Legal modal** | `openLegal(type)` renders Impressum / Datenschutz / Cookies content inline into `#legalBody`. Closes with `closeLegal()`. Body scroll locked while open. |
| **IntersectionObserver** | Fires `impressions_view` analytics event once when `#impressions` section reaches 35% viewport visibility. |

### DOM Helper

```js
const $ = (id) => document.getElementById(id);
```

### Form Pattern (used for all three forms)

1. Form has `target` attribute pointing to a hidden iframe
2. On submit, iframe `load` event is used (with fallback timer)
3. Google Apps Script responds with `postMessage` to the parent window
4. JS listens for `window.addEventListener("message", ...)` and checks `ev.data.ts === true` and `ev.data.action === "beta"|"review"|"contact"`
5. Success/error message is shown via `setMsg()` helper

---

## Backend: Google Apps Script

The backend is a Google Apps Script Web App deployed at `WEBAPP_URL`. It handles:

- **Beta signups** (`action=beta` POST) — stores email/name in a Google Sheet
- **Review submissions** (`action=review` POST) — stores review data
- **Contact messages** (`action=contact` POST) — stores contact form data
- **Review retrieval** (`action=reviews` GET with `callback` param) — returns JSONP with approved reviews
- **Analytics events** (POST with `action=event`) — logs events

The script is **not in this repository**. It lives in Google Drive.

---

## Internationalization

| File | Language | hreflang |
|---|---|---|
| `index.html` | German (de-DE) | `de` |
| `en.html` | English | `en` |

Both files link to each other via `<link rel="alternate" hreflang="...">` tags and include language-specific content throughout (section headings, form labels, error messages, legal texts, review rendering strings).

When making content changes, **always update both files** to keep them in sync.

---

## SEO & Metadata

Each page includes:
- `<title>` and `<meta name="description">`
- Open Graph tags (`og:title`, `og:description`, `og:image`, `og:url`, `og:type`)
- Twitter Card tags (`twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`)
- JSON-LD structured data: `Organization` and `WebSite` schema
- `<link rel="canonical">` pointing to GitHub Pages URL
- `robots.txt` allowing all crawlers
- `sitemap.xml` with both page URLs

---

## Development Workflow

### Editing the Site

Since this is a zero-dependency static site, editing is direct:

1. Open `index.html` or `en.html` in a text editor
2. Make changes to HTML, CSS (inline `<style>`), or JS (inline `<script>`)
3. Test by opening the file in a browser (or with a local server)
4. **Always update both `index.html` and `en.html`** for content/feature changes

### Local Preview

No build step required. Options:

```bash
# Python (simplest)
python3 -m http.server 8080

# Node.js (if available)
npx serve .
```

Then open `http://localhost:8080`.

### Adding Product Screenshots

Add images to `assets/` and update the `PRODUCT_IMAGES` array in both `index.html` and `en.html`:

```js
const PRODUCT_IMAGES = [
  "assets/product_01_one-tap.PNG",
  "assets/product_02_features-card.PNG",
  // add new entries here
];
```

### Changing the Backend URL

If the Google Apps Script is redeployed, update `WEBAPP_URL` in both HTML files:

```js
const WEBAPP_URL = "https://script.google.com/macros/s/NEW_ID/exec";
```

---

## Deployment

The site is deployed via **GitHub Pages** from the `main` branch root.

- German page: `https://mikamcflurry.github.io/true-shuffle-site/index.html`
- English page: `https://mikamcflurry.github.io/true-shuffle-site/en.html`

Pushing to `main` triggers an automatic deploy (no CI/CD pipeline needed). The `sitemap.xml` and `robots.txt` reference the GitHub Pages URLs.

---

## Key Conventions

1. **No external dependencies** — Do not introduce npm, bundlers, CSS frameworks, or JS libraries. Keep everything self-contained in the HTML files.

2. **Inline everything** — CSS goes in `<style>` in `<head>`. JS goes in `<script>` at the end of `<body>`. No separate `.css` or `.js` files.

3. **Bilingual parity** — `index.html` (DE) and `en.html` (EN) must always be kept in sync for structure, features, and bug fixes. Only translate user-facing text; keep all IDs, class names, and JS logic identical.

4. **CSS variables for theming** — Never hardcode colors directly in element styles. Always use `var(--bg)`, `var(--text)`, etc. to ensure dark/light mode works consistently.

5. **Privacy-first analytics** — The `trackEvent()` function must always respect `navigator.doNotTrack`. Never add third-party tracking scripts (Google Analytics, Facebook Pixel, etc.).

6. **Accessibility** — Maintain `aria-*` attributes on interactive elements (e.g., `aria-expanded` on mobile menu button, `role="dialog"` on modal). Buttons must have descriptive labels.

7. **No localStorage for PII** — The theme preference and cookie acknowledgment flag are stored in localStorage. Do not store email addresses, names, or any personal data in localStorage.

8. **Asset naming** — New assets go in `assets/`. Product screenshots use the pattern `product_NN_descriptor.PNG`. Logos use `logo_dark.png` / `logo_light.png` pattern.

9. **Form security** — All user-submitted text displayed back to the user must be passed through the `escapeHTML()` helper to prevent XSS.

10. **Year in footer** — The copyright year is set dynamically: `$("year").textContent = new Date().getFullYear()`. Never hardcode the year.

---

## Legal & Compliance

- **Company**: true-shuffle GmbH, Am Schwarzen Steg 5a, 95448 Bayreuth, Germany
- **Contact**: hello@true-shuffle.com, +49 176 6447 2296
- **GDPR**: Forms include explicit consent checkboxes. Privacy policy is accessible via the legal modal.
- **Cookie policy**: No tracking cookies are used. Only technically necessary localStorage entries. Cookie banner informs users on first visit.
- **Do Not Track**: Respected — analytics events are suppressed when DNT is active.

---

## Planned Features (Roadmap)

- OAuth login with Spotify, Apple Music, YouTube Music, Tidal, Deezer
- Playlist import & selection
- Playback control
- Cross-device sync
- Advanced shuffle modes
- Mobile app
