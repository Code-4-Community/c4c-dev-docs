# C4C Dev Docs

C4C Dev Docs holds all technical documentation for our applications and projects. The goal of this site is to bring existing information together to a single, searchable, and pleasant UI. 

As of now, we've copied over the LLB and SFTT API specs. There is some work to update those to be more user-friendly for this site, but I'm very happy with how MkDocs took our existing Markdown files and created this site so effortlessly. 

Other things to expect to see here: general repo setup guide for our major projects, DevOps/Deployment overview for LLB/SFTT cloud pipelines, Jumpstart course material...


## Local Installation

This repo uses two Python packages, [MkDocs](https://www.mkdocs.org/) and [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started/), to generate a static documentation site for C4C repos. Pages are written in Markdown.

#### Pre-requisite: python3 [(installation guide)](https://realpython.com/installing-python/)

Install the required Python packages: 

```
pip install mkdocs

pip install mkdocs-material
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

### Adding a Page (local copy of repo)

Add a Markdown file to the `docs/` directory. 

Edit the navigation in `mkdocs.yml`. The structure is well documented [here](https://code-4-community.github.io/c4c-dev-docs/). 

To publish your changes, create a PR. 

```
git checkout -b JB_update_llb-docs
git add .
git commit -m 'updating docs'
git push (set upstream if needed)
```

Include a screenshot of what your new changes look like in your PR. When merged, your changes will automatically be deployed to our site. 

## Contributing without Local Installation

You can make edits right on [GitHub](https://code-4-community.github.io/c4c-dev-docs/) easily as well, the downside is that you cannot preview what those changes will look like in the final MkDocs generated site, so this is preferred for small tweaks rather than adding new pages entirely. 

To contribute online, simply click the edit logo (the pen) in the top right corner of this page. This will take you to GitHub, where you can make whatever changes you'd like to the Markdown file which generates the page you were on. 

When you're done with your edits, simply write an update message, and select `create a new branch for this commit and start a pull request`. Submit your PR, and it'll be reviewed the same as any others.  

