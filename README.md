# diphyx.github.io

The DiPhyx blog at <https://diphyx.github.io>, served by GitHub Pages straight from this
branch. Plain HTML, no build step. The layout, type and spacing follow stripe.com/blog;
the colours are DiPhyx's. One token in `assets/style.css`, `--accent`, sets the link and
label colour.

```
index.html                       the blog index: featured story, then one row per story
stories/<name>/index.html        one folder per story, e.g. stories/a-gpu-in-a-minute/
_template/index.html             skeleton for a new story
assets/style.css                 the one stylesheet
assets/covers/*.svg              cover illustration per story, square
assets/mark.svg, favicon.png     brand
feed.xml, sitemap.xml            hand-maintained lists of stories
.nojekyll                        tells Pages to serve the files as they are
```

## Adding a story

1. Copy `_template/index.html` into a new folder under `stories/` named after the story's
   URL, for example `stories/my-story/index.html`, published at `/stories/my-story/`.
2. Fill in the placeholders in the `<head>` (title, description, canonical URL, cover) and
   in the article header (breadcrumb category, title, date, lead sentence). Write the body
   as plain HTML inside `<section class="post-body">`. Paragraphs, headings, lists, quotes,
   code blocks and tables are all styled.
3. Add a cover to `assets/covers/`. The existing ones are square SVGs on a light grid with
   the brand gradient; copy one and change the drawing.
4. Put the story on `index.html`. The newest story is the `<article class="featured">`
   card at the top; move the current featured story down into the list as an
   `<article class="post-row">` (copy an existing row) and put the new story in the card.
5. Add an `<item>` to `feed.xml` and a `<url>` to `sitemap.xml`.
6. Optionally add the new story to the "More from the blog" section of the others.

Categories are Product, Engineering and Guides. They are labels only; there is no filter.

## Checking locally

```bash
python3 -m http.server 4321
```

Then open <http://localhost:4321>. Links are root-relative (`/assets/…`, `/stories/my-story/`),
so the site has to be served from the repository root rather than opened as a file.

## Publishing

Push to `main`. In the repository settings, under Pages, the source is the `main`
branch at `/ (root)`.
