# Atalaya docs

Documentation for [Atalaya](https://github.com/dcarrillo/atalaya), a self-hosted uptime monitoring tool that runs on Cloudflare Workers.

The published site is at https://atalaya-docs.dcarrillo.es

Built with [MkDocs](https://www.mkdocs.org/) and the [Material](https://squidfunk.github.io/mkdocs-material/) theme. Deployed to GitHub Pages through the workflow in `.github/workflows/deploy.yml` on every push to `main`.

## Local development

You need [uv](https://docs.astral.sh/uv/) and Python 3.x

```bash
uv sync
uv run mkdocs serve
```

That starts a dev server at http://127.0.0.1:8000 with live reload, so edits under `docs/` show up as soon as you save.

To build the static site into `site/`:

```bash
uv run mkdocs build
```

## Layout

- `docs/` markdown pages, one file per section
- `mkdocs.yml` site config and navigation
- `overrides/` theme customizations (custom logo icon)
- `docs/stylesheets/extra.css` color palette overrides

Adding a page means dropping a markdown file in `docs/` and listing it under `nav` in `mkdocs.yml`.
