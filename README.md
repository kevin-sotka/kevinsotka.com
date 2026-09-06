# kevinsotka.com

The source for my personal site. One static HTML page, no build step, no dependencies.
Published with GitHub Pages.

## What is in here

| File | What it is |
|------|------------|
| `index.html` | The whole site. All CSS is inline. The only external request is the Inter webfont from Google Fonts. |
| `404.html` | A styled not-found page, served automatically by GitHub Pages. |
| `CNAME` | Tells GitHub Pages this site answers at `kevinsotka.com`. Do not delete it. |
| `.nojekyll` | Skips Jekyll processing. The site is plain HTML and does not need it. |

## Editing

Open `index.html` in an editor, change it, commit, and push. GitHub Pages rebuilds within a
minute or two. There is nothing to compile and nothing to install.

To preview locally before pushing:

```bash
python3 -m http.server 8000
```

Then open http://localhost:8000 in a browser.

## Deployment

Pushing to the `main` branch publishes the site. That is the entire pipeline.

See `DEPLOY.md` for first-time setup, including pointing the domain at GitHub Pages
through Namecheap.

## House rules for edits

These come from the project's `CLAUDE.md`, which lives one folder up and is not published here.

- No em dashes anywhere, including in code comments.
- Full sentences in body copy. No punchy sales fragments.
- Do not add a claim that is not verified. Every number on this page is confirmed.
- Do not self-deprecate. Understatement is fine, diminishment is not.
- Do not leave internal notes in the published HTML. Anyone can read the page source.
