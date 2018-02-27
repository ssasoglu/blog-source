---
title: Basic Git Aliases
date: 2017-06-20 23:38:52
tags: 
- github
- git
- shortcut
- alias
- productivity
---

It has been a while since I created a new blog post. I was super busy with a new project on my new position. I had some time to spare finally, so I took the opportunity to write about some git aliases that I use everyday which make my life easier on git console.

I generally use Windows operating system for development and I gave following examples accordingly, but the aliases I give here should probably work for other operating systems as well.

# Why

I suggest you to learn the git commands in a proper way first before even attempting to use aliases. With proper I mean learning each necessary command with it's most useful switches. [Atlassian](https://confluence.atlassian.com/bitbucketserver/basic-git-commands-776639767.html) and [GitHub](https://education.github.com/git-cheat-sheet-education.pdf) has good basic documentation for starters. Web is [full of tutorials](http://lmgtfy.com/?q=git+tutorial) and cheatsheets to learn. But after you learn your way around repositories, it is good to invest some time and create some aliases that can make your interaction with the repositories faster.

# How

If you have used git before, you probably have a `.gitconfig` file under your `%UserProfile%` folder. But even if you do not have, it is easy to create. Head over to [First time git setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup). I suggest to set at least your user name, e-mail and core editor with the following commands:

``` bash
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
git config --global core.editor code
```

The first two are easy enough. The third command actually sets your core editor. I generally use VS Code, but you can set to notepad if it is not installed on your machine.

After the initial setup, you can enter the following command in your command line. This will popup your core editor with your `.gitconfig` file to edit.

``` bash
git config --global --edit
```

# Aliases

I will go over some aliases I created, ordered by my daily usage of them. Here we go!

## Add All and Commit

I like to commit as frequently as possible so I quickly check my status with `git status` and then just use the following alias to add all my changes and commit them with a message.

### Alias

``` bash
cm = !git add -A && git commit -m
```

### Usage

``` bash
git cm "Changed some stuff."
```

----------------------------

## Checkout

Helpful for switching between branches.

### Alias

``` bash
co = checkout
```

### Usage

This command switches your current branch to `master` or `MyFeatureBranch`

``` bash
git co master
git co MyFeatureBranch
```

----------------------------

## Checkout Branch

Alias to create a new branch.

### Alias

``` bash
cob = checkout
```

### Usage

This command creates a new branch named as `MyNewBranchName`.

``` bash
git cob MyNewBranchName
```

----------------------------

## Checkout and Track Branch

Helpful during code reviews. Sometimes when I review code of colleagues, I prefer to clone their branch and have a closer look to their changes.

### Alias

``` bash
track = checkout --track
```

### Usage

This command creates a local branch `NewFeatureBranch` and sets an upstream to the branch on remote repository. (`origin/NewFeatureBranch`)

``` bash
git track origin/NewFeatureBranch
```

----------------------------

## Log with Format

Handy to quickly see what has changed recently.

### Alias

``` bash
[log]
   date = relative
[format]
   pretty = format:%h   %Cblue%ad%Creset   %ae    %Cgreen%s%Creset
```

### Usage

This command shows the last 3 commits to the repository.

``` bash
git log -3
```

results with a screen like this:

![git-log](/images/posts/2017/git-log.png)

----------------------------

## History

This is nowhere near as what you can see from GitHub interface or any GUI, but it is sometimes useful to quickly see what is going on the repository.

### Alias

``` bash
hist = log --oneline --abbrev-commit --all --graph --decorate --color
```

### Usage

``` bash
git hist
```

Results with a screen like this:

![git-history](/images/posts/2017/git-history.png)

# Final Configuration

The `.gitconfig` file looks like this when all these aliases are added:

``` bash
[user]
   name = Selcuk Sasoglu
[user]
   email = selcuk@sasoglu.com
[alias]
   # Get the current branch name (not so useful in itself, but used in other aliases)
   branch-name = "!git rev-parse --abbrev-ref HEAD"
   # Push the current branch to the remote "origin", and set it to track
   # the upstream branch
   publish = "!git push -u origin $(git branch-name)"
   # Delete the remote version of the current branch
   unpublish = "!git push origin :$(git branch-name)"
   hist = log --oneline --abbrev-commit --all --graph --decorate --color
   track = checkout --track
   cl = clone
   cm = !git add -A && git commit -m
   co = checkout
   cob = checkout -b
   br = branch
   wip = commit -am "WIP"
[log]
   date = relative
[format]
   pretty = format:%h   %Cblue%ad%Creset   %ae    %Cgreen%s%Creset
[filter "lfs"]
   clean = git-lfs clean -- %f
   smudge = git-lfs smudge -- %f
   process = git-lfs filter-process
   required = true
```

Enjoy these aliases and happy coding!

----------------------------