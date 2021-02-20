# Version Control and Git Workshop (Week 1.5)

Hey everyone! In this workshop, we'll be walking through 
[Git](https://git-scm.com/). Git is a tool which allows you to create a history
for the project you're working on. If this isn't something you're interested in,
then you don't have to go through this, but it's definitely one of the most useful
tools used in development.

Git enables you to create a snapshot of the state of your code and save it
whenever you want during your development process. It also provides
a bunch of other really useful features, like the ability to work on multiple features 
at the same time independently of each other, collaboration with other people on the same
work, and a way to share these snapshots.

[Check out the presentation slides](https://drive.google.com/file/d/1rHqLLYtKJYRpoSqVYdYDsBgHaZwSIAnc/view?usp=sharing)

## 1\. Create a file
``` shell
$ mkdir practice-git
$ cd practice-git
$ vim index.html
```

>Note: to start writing in vim, type `i`. Once you're done writing, type `ESC` to get out
>of the 'writing' mode (called insertion mode), then type `:w` (with the `:`) to save your file,
>and `:q` to exit vim.

index.html:
``` html
<head>
  <title> Home Page </title>
</head>
<body>
  <h1> Welcome to our Page! </h1>
  <p> We're learning Git </p>
  <p> It's fun </p>
</body>
```

Right now we just have a file with no source control.

## 2\. Add git

``` shell 
$ git init
$ ls -a
```

All git init does is add the .git/ directory to your project. This is
where git will be keeping track of all the different things it needs to 
to track your changes.

If this is your first time using git on your computer you will have to
tell Git your name and email so that it can credit you as the author
for any of the changes you make.

``` shell 
$ git config --global user.email "liam@gmail.com"
$ git config --global user.name "Liam Moynihan"
```

``` shell 
$ git status
```

Right now our one file is untracked by Git. Git makes you specify which
files you want to track because there are going to be a lot of files you
won't want in your source control (generate files, personal config files,
ect... ).

## 3\. Track our file

``` shell 
$ git add index.html
$ git status
```

At this point, our file is now in _Staging_. Staging has all the files
that have changed and we want to be committed.

``` shell 
$ git commit -m "Making our first commit"
```

When we make a commit we are basically taking a snapshot of what the
project looks like right now and adding it to the history of our project.


``` shell 
$ git log
$ git log --oneline
```

>Note: to quit `git log` (or any of its variations), you'll just need to type `q`.
>It uses a program very similar to vim called less (less is for reading files _only_ though).
>To scroll up and down (if you have a big enough history), you can either use arrow keys or
>`j` and `k`.

At this point we have a single blob with three tags pointing at it. 
- The commit tag, every commit we make will automatically have their own
commit tag pointing to it.
- The head tag because this is where we're working right now.
- The 'master' branch tag. Every git project has one default branch that
will always be called 'master'.

## 4\. Make our history more interesting

about.html
``` html
<head>
  <title> About </title>
</head>
<body>
  <h1> About Me </h1>
  <p> I am cool. </p>
</body>
```

skills.html
``` html
<head>
  <title> Skills </title>
</head>
<body>
  <h1> My Skills </h1>
  <p> Not typing </p>
  <p> Ripsticking </p>
</body>
```

Right now our two new files are still untracked as we would expect.

``` shell 
$ git add *.html
```

Now our files are in staging.

``` shell 
$ git commit -m "Added some pages"
$ git log --oneline
```

We made a commit we can see our master tag advanced along with our
head tag. We can also see each commit has their own commit tag.

Notice the direction of the arrows. When git is keeping track of
our history it's not making any copies of our project (like it would
if git worked like our bad VC system). Instead git just keeps track
of what's _different_ between two commits. This drastically cuts down
the amount of actual data git has to keep track of, it's not duplicating
any unnecessary information.

Now lets just make some quick changes so that we can navigate between
our pages.

index.html:
``` html 
<ul>
  <li>
    <a href="about.html">About Me</a>
  </li>
  <li>
    <a href="skills.html">My Skills</a>
  </li>
</ul>
```

skills.html & about.html:
``` html 
<p><a href="index.html">Go Home</a></p>
```

``` shell 
$ git status
$ git add *
$ git commit -m "Added navigation between pages"
$ git status
$ git log --oneline
```

As we can see, we've added another block to our tree and advanced our
head. Lets do this once more and add some color to our homepage.

```html
<h1 style="color: red">Welcome to our Page!</h1>
```

``` shell 
$ git status
$ git add index.html
$ git commit -m "Added color to our homepage"
$ git status
$ git log --oneline
```

At this point we're already getting a lot of benefit out of git. We
are keeping track of the progress of our project and have a record
of everything we've done.

## 5\. Go back in time

Having our history is cool but it only really helpful if we can go
back and use our history.

To change how our project looks we have to move our HEAD tag somewhere else
which we can do with the `git checkout` command. Remember our HEAD
tag is just specifying where we're working right now.

``` shell 
$ git checkout 4ca0fa
$ git log --oneline
$ git checkout 2f797e6
$ ls
$ git log --oneline
```

>Note: 4ca0fa is a commit hash __we__ have. You won't have one that looks like that in
>your git repository, so you'll have to use one that you find when you run `git log`.

Really when we're checking out these commits all we do is move our
head to a previous commit.

Now we see that when we're typing `git log` we're just getting the
history going back from our head. The other commits are still there,
they're just hidden right now because `git log` is only looking at the
history that our HEAD tag can see. (notice the direction of our commit
arrows)

Whenever we want to go back to our most recent snapshot we just checkout
master.

``` shell 
$ git checkout master
```

## 6\. Branches and Tags

So this is all pretty good but the thing that really makes git so
powerful is the idea of branches, and really a branch is just a line
of commits with a __branch tag__ pointing to the end of it.

All branches are going to be defined with a tag at the very tip of
its line of commits. Currently, 'master' points to our last commit because
it is the last commit that we've made in this line of work.

We can look at the branches we currently have with:
``` shell 
$ git branch
```

Right now, as we expect, we just have the one master branch. The asterisk ('*')
on the left is just going to denote what branch we're currently working
on (which branch our HEAD points to).

Notice how when we move our HEAD tag to a commit this changes how
it shows our master branch:
``` shell 
$ git checkout 4fca0ca
$ git branch
```

>Note: remember, you won't have a 4fca0ca commit hash unless you're working using our repository.

So git doesn't really like when our HEAD is not at the same place as a
branch tag, this is because if we were to add some commits from here
and then switch back to our master branch, we would lose any reference
we had to all our work. <DRAW EXAMPLE>

We can fix this by simply creating a new branch. Notice that just creating
our branch still leaves our head detached. We need to checkout the
new branch to tell HEAD it should be pointing to the feature tag.

``` shell 
$ git branch feature
$ git branch
$ git checkout feature
$ git branch
```

Now that we have our tags all set up for the commit before we added
color, lets try adding a new page.

quotes.html:
```html
<head>
  <title>Quotes</title>
</head>
<body>
  <h1>Things I've Said</h1>
  <p>"Git is fun"</p>
  <p>"Ripsticking is really cool"</p>
</body>
```

``` shell 
$ git status
$ git add quotes.html
$ git commit -m "Added a quotes page"
```

And we'll add another commit to add navigation

index.html:
```html
<li><a href="quotes.html">Quotes</a></li>
```

quotes.html:
```html
<p><a href="index.html">Go Home</a></p>
```

``` shell 
$ git status
$ git add *
$ git commit -m "Added navigation to the quotes page"
```

## 7\. Merging and Workflow

Let's draw out our tree so far.

So different companies will have different standards for how they're
using git in their workflow, but pretty much anywhere you go the team
will likely reserve the master branch for stable releases of their
software. That means that if they're working on a new feature
(like our new quotes page) then instead of developing right on the
master branch, they'll first create a new branch to work on and then
merge their work back into master when they're sure it works. 

So to show merging works let's first leave our feature branch for a
minute and go back to our master branch.

``` shell 
$ git checkout master
$ git log --oneline
```

Let's pretend we're working on this website for our employer and our
boss comes and asks us to add new colors to our other pages. We don't
want to work right off of master so we'll create a new branch before
we do any development.

Notice how, because we're using git, we don't have to worry about losing
the work we've already done on our quotes page just because we need to
go work on some other feature here.

Also notice how when we're doing `git log` we can tell where our head
is pointing

``` shell 
$ git branch hotfix
$ git log --oneline
$ git checkout hotfix
$ git log --oneline
```

Let's make the changes the boss wanted

about.html, skills.html:
``` html
<h1 style="color: blue"></h1>
```

``` shell 
$ git status
$ git add *
$ git commit -m "Added color to our about and skills pages"
```

Now once we're sure our hotfix branch is working exactly how we want
it to we'll want to merge in our master branch to have our changes.
If you notice, our N'th commit is going in a direct line from our
master tag. To update our master branch, all we really need to do
is change where the master tag is referring to.

``` shell 
$ git log --oneline
$ git checkout master
$ git merge hotfix
$git log --oneline
```

When we just update where the tag is pointing to, this is called a 
fast-forward merge. Now that our master is up to date with our hotfix,
we can safely delete that branch with:

``` shell 
$ git branch hotfix -d
$ git branch
```

Now lets try merging our feature branch back into master as well. Notice
how the two branches have diverged, we can't just move our master tag
like last time.

To actually merge these two branches we're going to do what's called a
3-way merge. This merge uses the last commit the two branches share
and each of the lasts commits from each branch.

``` shell 
$ git merge feature
$ git log --oneline
```

<DRAW TREE>

Notice that to merge these two branches we needed to add a 'merge commit'.
This commit is different than our other ones because it's actually
dependent on two different commits rather than just one.

We luckily didn't have to do anything because our two branches were working
on different parts of the code. If instead our two branches both made
changes to the same piece of code we would have whats called a __merge conflict__.
When this happens we'd just have to manually decide which parts of each
branch's changes that we want to keep.

This is where we're going to end this workshop. There's much more to learn about
though, so try using it and checking it out yourself!

This is basically all git is about. There are some fancy things you can 
do with rebasing, and changing your log history. When you're working at
a company you're also going to be dealing with remote branches that
other developers are working on but everything is built out from the
basic concepts of commits, branching, and merging.

Down below in the __Extra Materials__ section, you can see a few other resources you can use for
learning git that we recommend

## Extra Materials:

- [Git documentation](https://git-scm.com/doc)
    - Here's where you can go to get official documentation for Git.
- [Ry's Git Tutorial (archive)](https://web.archive.org/web/20161121145226/http://rypress.com/tutorials/git/index)
    - A simple tutorial for learning the basics of Git.
- [Atlassian Git Tutorial](https://www.atlassian.com/git/tutorials)
    - A tutorial by a company which makes a lot of enterprise-level stuff for Git. 
    This one is really useful for learning about new (and sometimes advanced) topics.
- [Githug](https://github.com/Gazler/githug)
    - A more in-depth tutorial which teaches more complex Git concepts.
- [Visual Git Guide](http://marklodato.github.io/visual-git-guide/index-en.html)
    - A guide to Git done using visual representations of everything.
- [Git for Computer Scientists](https://eagain.net/articles/git-for-computer-scientists/)
    - An abstract description of Git.
- [Hubspot Git Tutorial](https://product.hubspot.com/blog/git-and-github-tutorial-for-beginners)
    - A Git and GitHub tutorial by Hubspot.
