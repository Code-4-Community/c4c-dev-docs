# C4C Dev Docs

This repo uses two Python packages, [MkDocs](https://www.mkdocs.org/) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started/), to generate a static documentation site for C4C repos. Pages are written in Markdown.

## Installation 

#### Pre-requisite: python3 [(installation guide)](https://realpython.com/installing-python/)

Install the required Python packages: 

```
pip install mkdocs

pip install mkdocs-material
```

Or using Anaconda (if you already have Anaconda installed):

```
conda install -c conda-forge mkdocs

conda install -c conda-forge mkdocs-material

conda install -c conda-forge mkdocs-material-extensions
```

### Previewing Locally

MkDocs includes a live preview server, so you can preview your changes as you write your documentation. The server will automatically rebuild the site upon saving. Start it with: 
```
mkdocs serve
```

Point your browser to [localhost:8000](http://localhost:8000/)

### Building your site
When you're finished editing, you can build a static site from your Markdown files with:
```
mkdocs build
```

The contents of this directory make up your project documentation. There's no need for operating a database or server, as it is completely self-contained. The site can be hosted on GitHub Pages, GitLab Pages, a CDN of your choice or your private web space.

## Adding a Page

Add a Markdown file to the `docs/` directory. 

Additionally, you can add a site navigation link to `mkdocs.yml`

```
nav:
    - Page Name: newfilename.md
```