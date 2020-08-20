# All About MkDocs, Markdown, and Contributing to This Resource

I don't know how often it'll be needed, but all you need to know about MkDocs can be found on their amazing website. 
  
[mkdocs.org](https://www.mkdocs.org).

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

# Extensions


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