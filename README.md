# Backend Wiki

A practical, backend-focused learning wiki for Ruby, Rails, and production web
engineering.

## Local development

```sh
python3 -m venv .venv
. .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

Open <http://127.0.0.1:8000>. GitHub Pages deploys automatically when `main`
changes. Replace the placeholder `site_url` and repository values in
`mkdocs.yml` before publishing.
