---
name: mkdocs-page-boilerplate
description: Create and wire up new MkDocs markdown pages in this repository. Use when the user wants a new docs page, a markdown boilerplate page, or an updated `mkdocs.yml` nav entry. Ask for the page title, filename, and target nav section if any of them are missing before making edits.
---

# Mkdocs Page Boilerplate

## Overview

Create a new markdown page under `docs/` and add a matching entry under `nav:` in
`mkdocs.yml`.

Use this skill for repo-local documentation pages, not for standalone HTML pages or app scaffolds.

## Workflow

1. Confirm the required inputs
2. Pick the target docs folder and nav section
3. Create the markdown file from the template in `assets/page-template.md`
4. Add the nav entry in `mkdocs.yml`
5. Keep formatting and ordering consistent with nearby entries

## Required Inputs

Ask the user for these values if they are not already explicit:

- File name
- Target nav section

If the user gives only a title, propose a slugified file name and ask for confirmation when the
mapping is not obvious.

If the user gives only a filename, ask for the display title that should appear in `mkdocs.yml`.

If the nav section is missing, show the current top-level sections from `mkdocs.yml` and ask which
one should contain the new page.

## Repo Conventions

Read `references/repo-conventions.md` before editing.

For this repo:

- Pages live under `docs/<section>/` for section content
- The root landing page is `docs/index.md`
- Nav entries are declared manually in `mkdocs.yml`
- New entries should be inserted into the matching existing section instead of appended at random

## Page Template

Use `assets/page-template.md` as the default starting point.

Replace the placeholders with the requested page content. Keep the first heading as the page title.

If the user asks for a very minimal page, it is acceptable to trim optional sections while keeping
the heading and a short intro.

## Nav Rules

- Preserve existing YAML style and quoting patterns
- Use the user-facing page title as the nav label unless the user asks for a shorter label
- Keep entries grouped with similar pages in the same section
- Do not rename unrelated nav entries

## Output Expectations

When using this skill, complete the task end to end:

- Create or update the markdown page
- Update `mkdocs.yml`
- Mention the final file path and nav label in the response

If the request is ambiguous, ask the user before editing files.
