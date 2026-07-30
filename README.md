# true-shuffle-site

Landing page for **true-shuffle** — shuffle that plays your playlist like a deck
of cards: every playable, unique track exactly once per run, no repeats until
the deck is done.

- German: <https://mikamcflurry.github.io/true-shuffle-site/index.html>
- English: <https://mikamcflurry.github.io/true-shuffle-site/en.html>

> **Spelling:** the product is `true-shuffle`. `true-shuffel` was an early
> misspelling and survives only in legacy asset filenames, which are kept so
> existing references do not break. Never use it in new content or filenames.

---

## What this repository is

A zero-dependency static site: two HTML files with inline CSS and JavaScript, an
`assets/` folder, and nothing else. No npm, no build step, no framework.

```
index.html        German landing page (lang="de")
en.html           English landing page (lang="en")
index beta.html   Staging scratch copy, not deployed
robots.txt        Allows all crawlers, points at the sitemap
sitemap.xml       Both page URLs
assets/           Logos, hero media, Open Graph image, shuffle-study.json
CLAUDE.md         Working notes for AI assistants
```

`index.html` and `en.html` are structurally identical. **Every content or bug
fix must be applied to both** — only the human-readable text differs.

---

## Page structure

1. Sticky header — logo, DE/EN switch, dark/light toggle, mobile menu, CTA
2. Hero — animated logo, headline, sub-headline, CTA
3. Features
4. `#data` — "Zahlen lügen nicht" / "Numbers don't lie" comparison section
5. `#beta` — waitlist form
6. `#reviews` — rating form plus reviews loaded from the backend
7. `#faq`
8. `#contact` — contact form
9. Footer — nav, legal links, cookie reset
10. Legal modal (Impressum / Datenschutz / Cookies) and cookie banner

---

## Forms and backend

All three forms POST to a **Google Apps Script Web App** (`WEBAPP_URL`, defined
in both HTML files). The script is not in this repository — it lives in Google
Drive.

Submissions are sent through a hidden iframe, and the script replies via
`postMessage`. Reviews are read back over JSONP and are only shown after
approval.

Nothing is stored in the browser except the theme preference and the cookie
acknowledgement — **no email addresses or names in localStorage, ever.**

---

## Analytics

Cookie-less, and suppressed entirely when the browser sends Do Not Track.

| Event | Fired when |
|---|---|
| `cta_join_beta_click` | A "join the beta" CTA is clicked |
| `data_section_view` | The data section reaches 35% visibility, once per page load |
| `waitlist_submit_success` | The backend confirms a waitlist signup |
| `waitlist_submit_pending` | The iframe loaded but no confirmation arrived yet — **not** a success |
| `review_submit_success` | The backend confirms a review |
| `contact_submit_success` | The backend confirms a contact message |

The three form events used to share one `signup_success` name, which made
waitlist conversion impossible to measure — a review or contact message counted
as a signup. Keep them distinct.

---

## Local preview

```bash
python3 -m http.server 8080     # then open http://localhost:8080
```

---

## Deployment

GitHub Pages, from the root of `main`. Pushing to `main` deploys. Update
`lastmod` in `sitemap.xml` when the content changes meaningfully.

---

## Conventions

1. **No external dependencies.** No npm, bundlers, CSS frameworks or JS libraries.
2. **Inline everything.** CSS in `<style>`, JS in `<script>` at the end of `<body>`.
3. **Bilingual parity.** Keep IDs, classes and JS logic identical across both
   files; translate only visible text.
4. **CSS variables for theming.** Never hardcode colours; dark/light both matter.
5. **Privacy-first analytics.** Always respect `navigator.doNotTrack`. Never add
   a third-party tracking script.
6. **Escape user content.** Anything from a form that is rendered back goes
   through `escapeHTML()`.
7. **Never hardcode the year.** The footer year is set from `new Date()`.
8. **Claims need evidence.** The data section is a probability model, and is
   labelled as one. Do not present it as a measurement of any streaming
   service's real behaviour.

---

## Related

The product itself lives in
[true-shuffle-PoC](https://github.com/MikaMcFlurry/true-shuffle-PoC) — connectors
for Spotify, Apple Music and YouTube Music, the shuffle engine and the run
state machine. Check that repository's `STATUS.md` before making any public
claim about which services are supported.
