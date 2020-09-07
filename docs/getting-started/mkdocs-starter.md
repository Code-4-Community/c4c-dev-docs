# All About MkDocs, Markdown, and Contributing to This Resource

I don't know how often it'll be needed, but all you need to know about MkDocs can be found on their amazing website. 
  
[mkdocs.org](https://www.mkdocs.org).

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



## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server (you likely only need this one)
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml                # The configuration file. Super important. 
    docs/
        index.md              # The documentation homepage.
        mkdocs-starter.md     # This page
        llb/
            index.md          # Not built yet
            llb-api-spec.md   # Needs to be updated
            llb-auth-api.md   # Should be abstracted away
        sftt/
            index.md          # Not built yet
            sftt-api-spec.md  # Needs to be updated
            sftt-auth-api.md  # Should be abstracted away
    img/
        favicon.ico           # Icon for browser tab
---

## Material-MkDocs

What's much more important than how MkDocs internally works is how to use our theme, [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/). 

The [Setup Documentation](https://squidfunk.github.io/mkdocs-material/setup/changing-the-colors/) is an excellent resource for site customization + configuration needs - changing colors, fonts, icons, navigation... they even support a [comment system](https://squidfunk.github.io/mkdocs-material/setup/adding-a-comment-system/)!!

The [Reference Documentation](https://squidfunk.github.io/mkdocs-material/reference/admonitions/) offers a lot of **awesome** features to make the site much more approachable. 

- [code blocks](https://squidfunk.github.io/mkdocs-material/reference/code-blocks/) - obviously we'll need these 
- [data tables](https://squidfunk.github.io/mkdocs-material/reference/data-tables/)
- [lists](https://squidfunk.github.io/mkdocs-material/reference/lists/) - could be good for tracking live course progress in jumpstart

Many require extensions, which are no issue to add. They live in `mkdocs.yml`. All extensions we've installed are listed below. Please add an example of any extension you add here. 

## Admonitions

Admonitions, also known as call-outs, are an excellent choice for including side content without significantly interrupting the document flow. Material for MkDocs provides several different types of admonitions and allows for the inclusion and nesting of arbitrary content.

[Docs](https://squidfunk.github.io/mkdocs-material/reference/admonitions/)

---

### [admonition](https://python-markdown.github.io/extensions/admonition/)

!!! info "Important!"
Here's a very important reason or explanation I don't want you to miss

Source: 
```
!!! info "Important!"
Here's a very important reason or explanation I don't want you to miss
```
---

### [pymdownx.details](https://facelessuser.github.io/pymdown-extensions/extensions/details/)

???+ example
    ???+ success "Prefer"
        Here is a preferred method of doing some development task. Follow this best practice!
        
    ???+ fail "Avoid"
        Here's a less good way to do it. Don't do it this way. 
        
Source
```
???+ example
   ???+ success "Prefer"
       Here is a preferred method of doing some development task. Follow this best practice!
       
   ???+ fail "Avoid"
       Here's a less good way to do it. Don't do it this way. 
```
        
---

### More examples

[Docs - Material](https://squidfunk.github.io/mkdocs-material/reference/admonitions/)