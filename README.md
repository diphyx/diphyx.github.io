# diphyx.github.io

The DiPhyx blog at <https://diphyx.github.io>, served by GitHub Pages straight from this
branch. Plain HTML, no build step. The layout follows stripe.com/blog with DiPhyx colours.

```
index.html                       the blog index: category tabs and one card per story
stories/<name>/index.html        one folder per story, e.g. stories/a-gpu-in-a-minute/
_template/index.html             skeleton for a new story
assets/style.css                 the one stylesheet
assets/covers/*.svg              cover illustration per story
assets/mark.svg, favicon.png     brand
feed.xml, sitemap.xml            hand-maintained lists of stories
.nojekyll                        tells Pages to serve the files as they are
```

## Adding a story

1. Copy `_template/index.html` into a new folder under `stories/` named after the story's URL, for
   example `stories/my-story/index.html`, which is published at `/stories/my-story/`.
2. Fill in the placeholders in the `<head>` (title, description, canonical URL, cover) and
   in the header of the article (category, title, subtitle, date, reading time). Write the
   body as plain HTML inside `<div class="wrap narrow prose">`.
3. Add a cover to `assets/covers/`. The existing ones are 1200×750 SVGs on a light grid
   with the brand gradient; copy one and change the drawing.
4. Add the story to `index.html`: copy an `<article class="post-row">` block and put it
   at the top of the list. The first block in the list is the featured story and carries
   the `featured` class and the subtitle; move that class to the new block.
5. Add an `<item>` to `feed.xml` and a `<url>` to `sitemap.xml`.
6. Optionally add the new story to the "More from the blog" section of the others.

Categories are Product, Engineering and Guides. The index tabs filter on the
`data-category` attribute of each card, so the value must match a tab exactly.

## Checking locally

```bash
python3 -m http.server 4321
```

Then open <http://localhost:4321>. Links are root-relative (`/assets/…`, `/stories/my-story/`),
so the site has to be served from the repository root rather than opened as a file.

## Publishing

Push to `main`. In the repository settings, under Pages, the source is the `main`
branch at `/ (root)`.
