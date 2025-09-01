# zkSNARK Wiki: Contribution Guide

## Getting Started

1.  Clone the repository: `git clone https://github.com/zksnark-wiki/zksnark-wiki.github.io.git`
2.  Install MkDocs: `pip install mkdocs`
3.  Install the Material for MkDocs theme: `pip install mkdocs-material`

## Workflow

1.  Remember to pull and merge.
2.  Create a new Markdown (`.md`) file in the **`docs`** directory and write your content.
3.  Integrate your new page into the navigation by editing `mkdocs.yml`
4.  Preview your changes locally with `mkdocs serve`.
5.  Commit your updates to Git:
    -   `git add .`
    -   `git commit -m "Add new post about X"`
    -   `git push`
6.  Deploy the site to GitHub Pages: `mkdocs gh-deploy --force`