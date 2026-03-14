## Repo Instructions

- This repository contains local agent skills under `skills/`
- When a task matches a listed skill, open that skill's `SKILL.md` and follow it
- Prefer repo-local skills before falling back to generic approaches

## Local Skills

### Available skills

- mkdocs-page-boilerplate: Create and wire up new MkDocs markdown pages in this repository
  and update `mkdocs.yml` nav entries. Use when the user wants a new docs page, markdown
  boilerplate, or a new nav item. Ask for the page title, filename, and target nav section if
  any are missing. File:
  `/home/wmax641/work/personal-docs/skills/mkdocs-page-boilerplate/SKILL.md`

## How To Use Skills

- Discovery: Check the `Local Skills` list above for repo-local skills
- Trigger rules: Use a skill when the user names it or when the request clearly matches its
  description
- Loading: Read only the skill's `SKILL.md` first, then open referenced files only as needed
- Context hygiene: Keep context small and avoid opening unrelated files in the skill folder
- Fallback: If a skill file is missing or incomplete, continue with the best direct approach
