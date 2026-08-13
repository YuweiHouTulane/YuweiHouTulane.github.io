# yuweihoutulane.github.io

Personal academic site for Yuwei Hou, served by GitHub Pages at
<https://yuweihoutulane.github.io>.

Plain HTML and one CSS file. No Jekyll, no npm, no build step — push to the
default branch and GitHub Pages serves the files as-is, usually within a minute.

## Preview locally

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Opening the `.html` files directly with `file://` also works, but the `404.html`
page uses absolute paths and only looks right when served.

## Layout

```
index.html            Home — bio, research interests, news, selected papers
research.html         Projects in more detail
publications.html     Full publication list, preprints, presentations
cv.html               Education, experience, awards, skills, service
404.html              Shown for any URL that doesn't exist
assets/css/main.css   All styling (light + dark, responsive, print)
assets/img/           Profile photo lives here
assets/cv/            Drop your CV PDF here as yuwei-hou-cv.pdf
.nojekyll             Tells GitHub Pages to skip Jekyll and serve raw files
robots.txt, sitemap.xml
```

## What's still outstanding

Anything still unwritten is wrapped in `<span class="ph">`, which renders with a
dashed olive underline so it's obvious in the browser. Hovering shows a note
about what belongs there. To finish a piece: replace the text, then **remove the
`ph` class** (or the whole `<span>`).

Find what's left — note the `\bph\b` pattern, which also catches spans carrying a
second class like `class="cv__note ph"`:

```bash
grep -rnE 'class="[^"]*\bph\b[^"]*"' *.html
```

As of August 2026 **none remain** — every page carries real content, and the
command above should return nothing.

Still worth adding when it exists:

- **The osteoblast heterogeneity atlas** (Pan Z … Hou Y … Deng H-W), held back
  while the manuscript is in preparation. Uncomment the Preprints section in
  `publications.html` once it's public.
- **A CV PDF** — drop it at `assets/cv/yuwei-hou-cv.pdf` and uncomment the
  download link near the top of `cv.html`.

### Sections hidden in HTML comments

These sections have no content yet and are commented out rather than deleted,
so the markup survives as a template:

| File | Sections |
|---|---|
| `publications.html` | Preprints |
| `cv.html` | Awards & funding, Teaching & mentoring, Service |
| `research.html` | Software |

To bring one back, delete the `<!--` before it and the `-->` after it, then
replace the sample text. Note that HTML comments cannot contain `--`, so avoid
double hyphens if you edit inside a commented block.

### Replacing the photo

The sidebar photo is `assets/img/profile.jpg` — an 800×800 centre crop, about
160 KB. It's loaded on all five pages, so keep any replacement small; the
original 1122×1402 PNG was 3.1 MB, which is far too heavy for a 136 px avatar.
`object-fit: cover` in the CSS crops to a circle, so a square source needs no
special treatment.

To swap in a different file, save it as `assets/img/profile.jpg` and you're
done — no markup changes. If you use a different filename or format:

```bash
sed -i '/rel="icon"/!s|assets/img/profile\.jpg|assets/img/YOUR-FILE|g' *.html
```

That updates the sidebar `<img>` and the `og:image` link-preview tag on all five
pages, and the `/rel="icon"/!` guard leaves the favicon on the SVG — a photo
makes a poor 16px icon. Swap in a real `.ico` or `.png` favicon if you'd rather.

`404.html` is covered by the same command: its paths are absolute, but the
substring still matches.

### The sidebar is duplicated

Each page carries its own copy of the sidebar — the cost of having no build
step. When you change it, change it in all five files. Only the
`aria-current="page"` attribute differs between them, marking which nav item is
the current page.

## Adding a publication

Copy one `<li class="pub">` block in `publications.html`, paste it under the
right year, and edit four things: title, authors, venue, links. Keep
`<span class="me">Hou Y</span>` around your own name so it renders bold. Delete
any `<a>` for a link you don't have — a live link to `#` is worse than no link.

## Adding a news item

Newest first, at the top of the `<ul class="news">` in `index.html`:

```html
<li>
  <time datetime="2026-09">Sep 2026</time>
  <span>Paper accepted at [venue].</span>
</li>
```

Keep the list to five or six items; delete the oldest as you add new ones.

## Design notes

- Colors follow the **Tulane University Brand Guide (August 2024)**. Brand
  colors are used at their published values; the few derived shades are
  labelled as such in `main.css`. Change `--accent` to restyle the whole site.

  | Role | Light | Dark |
  |---|---|---|
  | Page | Tulane White `#FFFFFF` | Black `#000000` |
  | Body text | Basin `#343333` | Moon `#EDECE8` |
  | Links / accent | Tulane Green `#255C4E` | Tulane Green relit `#49B69A` |
  | Cards, callouts | Moon `#EDECE8` | `#171717` |
  | Tag background | Lichen `#E7F2E0` | `#0A1A16` |

  The page is plain white and plain black; the brand palette is carried in
  the text, accent and surfaces rather than the background. Dark mode keeps
  **green** as the identity color rather than switching to Tulane Blue, so
  the site reads the same in both themes. The green is relit in HSL (hue and
  saturation preserved) rather than tinted with white, which would grey it
  out.
- **Every text pairing meets WCAG AA** (4.5:1 body text, 3:1 borders and the
  placeholder marker) — verified in both themes. If you change `--accent` or
  either background, re-check contrast rather than assuming.
- Dark mode follows the visitor's OS setting via
  `@media (prefers-color-scheme: dark)`. There's no manual toggle.
- **Typography follows the brand guide's tiers**, in the order it specifies:

  | Tier | Serif | Sans |
  |---|---|---|
  | Print (primary) | Adobe Caslon Pro | Freight Sans Pro |
  | Desktop fallback | Baskerville | Source Code Variable |
  | Web safe | Georgia | Verdana |

  The two print fonts are named first in `--font-serif` / `--font-sans`, so
  anyone who has them installed sees the real thing. They are **not**
  self-hosted: their licence doesn't permit serving them from a public
  site, so there are no `@font-face` rules and still no external requests.
  Everyone else falls through to the brand's own substitutes — in practice
  **Georgia headings and Verdana body text** for most visitors.

  Source Code Variable is a monospace face, so it's in `--font-mono`
  (used by `<code>`) rather than the body stack, where it would be wrong
  for running text.

  If Tulane holds an Adobe Fonts web licence, you can serve the real
  Caslon and Freight Sans by adding a Typekit `<link>` to each page's
  `<head>`. That would be the one external request on the site.
- The layout is a sticky sidebar next to a single content column, collapsing to
  a stacked header under 56rem.
- No web fonts, no JavaScript, no external requests — the site works offline and
  loads instantly.
- `cv.html` has a print stylesheet: the browser's Print to PDF gives a usable
  CV without the sidebar or footer.

## Enabling GitHub Pages

If the site isn't live yet: repository **Settings → Pages → Build and
deployment**, set Source to *Deploy from a branch*, branch `main`, folder
`/ (root)`.
