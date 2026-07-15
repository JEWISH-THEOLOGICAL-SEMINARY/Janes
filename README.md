# JANES — Journal of the Ancient Near Eastern Society

The [Hugo](https://gohugo.io/) static site for the *Journal of the Ancient Near
Eastern Society*, published by the Jewish Theological Seminary.

**Live site:** https://janes.jtsa.edu/

## How it's hosted

- **GitHub Pages** serves the built HTML. Every push to `main` triggers
  `.github/workflows/hugo.yml`, which builds the site fresh in CI
  (`hugo build --gc --minify`) and deploys via `actions/deploy-pages`. A push to
  `main` is a production deploy.
- The built output lives in `public/`, which is **git-ignored** — it is pure CI
  output and is never committed.
- `CNAME` pins the custom domain (`janes.jtsa.edu`); `baseURL` in `hugo.toml`
  must match it.
- **Article PDFs are not stored in this repo.** They live in an S3 bucket and are
  served through CloudFront. Each article's front matter carries a full
  `pdf_url` pointing at the CloudFront distribution.

## Repository layout

| Path | Purpose |
| --- | --- |
| `content/` | All site content (Markdown). Pages plus `content/volumes/<vol>/<issue>/article-N.md`. |
| `layouts/` | Hugo templates, including the volume/article and author-index layouts. |
| `static/` | Committed static assets (images, CSS). PDFs are **not** here. |
| `data/authors.yaml` | Generated author index (do not hand-edit). |
| `archetypes/` | Hugo content archetypes. |
| `hugo.toml` | Site configuration (`baseURL`, menus, params). |
| `.github/workflows/hugo.yml` | CI build + GitHub Pages deploy. |

## Local preview

Requires [Hugo](https://gohugo.io/) (see `HUGO_VERSION` in the workflow for the
version CI uses):

```bash
hugo server -D
```

Then open http://localhost:1313/. The `-D` flag renders draft content.

## Publishing content

New volumes and the author index are produced by tooling kept **outside this
repo** (in the parent working directory's `scripts/`), not by hand-editing
Markdown here. That workflow uploads each PDF to S3, writes the article/issue/
volume Markdown into `content/volumes/`, and regenerates `data/authors.yaml`.
Committing and pushing the result to `main` deploys it.
