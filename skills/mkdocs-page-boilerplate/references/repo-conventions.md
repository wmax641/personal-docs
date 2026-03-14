# Repo Conventions

## Docs Layout

- Section pages live under `docs/sysadmin/`, `docs/syseng/`, `docs/patterns/`, and `docs/wap/`
- The site landing page is `docs/index.md`
- Images live under `docs/img/`

## MkDocs Navigation

- `mkdocs.yml` uses a manual `nav:` block
- Top-level sections currently include:
  - `Systems Administration`
  - `Systems Engineering`
  - `Patterns`
  - `wmax641 App Platform (WAP)`
- Nav items are written as `"Label" : "path/to/file.md"`

## Page Creation Rules

- Put new markdown pages in the section folder that matches the selected nav group
- Use lowercase kebab-case filenames ending in `.md`
- Match nearby page naming when the section already has a visible convention

## Editing Rules

- Insert the new nav item inside the existing section block
- Preserve indentation and quote style
- Avoid changing unrelated sections while adding the new page
