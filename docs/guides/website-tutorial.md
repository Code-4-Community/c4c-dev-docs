!!! important "By C4C and MULTI"

For people unfamiliar with mkdocs and it's powers, [this](https://squidfunk.github.io/mkdocs-material/reference/abbreviations/) is pretty extensive 

## Git Overview

### GitHub Pages
-are you doing this too @Floris

### Clone 
You can clone a repository by going to the repo's page;

> What's a repository? It's a directory or a storage space where your projects can live. Every time you create a Git project, there will be a `./git` folder which keeps track of all your changes and builds your work history over time. If you delete the `./git` folder, your project's history will also be deleted.

There, you will see different buttons, including a green button labeled "Code." If you click on it, you are able to access the repository's link and can copy it by clicking on the button right next to the link, or selecting and copying the link manually. 

![clone Picture](..\img\clonepic.png)

Once you are at your dev environment (for today we're using Visual Studio Code), we can open a terminal (from the top menu) and type in `git clone [repo address]` like the picture below and hit enter.

![Terminal Clone](..\img\terminalclone.png)

### Pull Requests

At it's core, a pull request answers the question: "What did you change?".

A pull request is an event where you submit code contributions in order to merge them into an ongoing project. Pull requests allow you to request feedback on your changes, contribute to source code that you don't own, and make it clear what you changed. Pull requests create a formal process of reviewing code before we submit to prevent any breakage. 

Before creating a pull request, the first thing you have to do is create a `branch`. A branch is a copy of the original source code that you can change completely without affecting the `master`. The default branch name in Git is master. You can create a new branch by typing:

`git checkout -b nameofbranch`

This will switch you off of the master branch and also create a new branch named "nameofbranch". After, there will be a message confirming that you have switched out. Now you can start making edits on the new branch that you have created.

Once you have finished making changes, you create a pull request to ask for reviewers (you can review your own changes if working independently!) to look at the changes to request them or approve them. Pull requests allow us to look at the source code side-by-side from what it was to what it is proposed to be, so we can understand the implications of what we are trying to change. If a pull request is approved, the branch you created is merged into the master and your changes become part of the new source code.

### Commiting Changes
-sadaf

### Pushing Changes

After we have commited the files that you have staged in your local repository, we want to specify the remote repository URL where your local repository will be pushed. We can type:

`git remote add origin "url"`

Now we can push the code in your local repository to GitHub using:

`git push -u origin master`

You will be asked to enter your username and password. After, you can go to GitHub to view the files in your repository that you successfully pushed!

### Setting Up Your Profile
And I'll do this section - Floris

## HTML + CSS

### TODO for RYAN - include how you can double click on an html file to open it on the browser

## Setting Up the Dev Environment

## Additional Resources

#### Github Guides - https://guides.github.com/
Short and easy articles on using Git professionally
#### Git-it - http://jlord.us/git-it/
Step-by-step guide on Git
#### Git & Github in a Nutshell - https://codeburst.io/git-and-github-in-a-nutshell-b0a3cc06458f
Quick review of terminology & commands
#### MULTI Git/Github Workshop Slides - https://docs.google.com/presentation/d/1OuXPJwNGcRUVbKoklV83o_knSMZRflJbCGsbM81uzDA/edit?usp=sharing
