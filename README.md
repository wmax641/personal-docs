# personal-docs
Personal documentation

Hosted in github pages at `/docs`

## DevOps
Testing/Dev - run `mkdocs serve` from root directory to serve docs locally

Deploy - Push to master branch, Github Action will deploy (via `mkdocs gh-deploy`, generating static files in `gh-deploy` branch. This gets served as Github Pages)
