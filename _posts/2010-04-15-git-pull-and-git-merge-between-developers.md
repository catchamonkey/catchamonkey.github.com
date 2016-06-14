---
layout: post
title: Git pull and git merge between developers
date: 15th April 2010
excerpt: A short article outlining how you and your fellow developers can use git between you, without ditching svn as your upstream master repository (as may be required in your place of work)
---

I use git locally, but the main upstream repo is an SVN one, there are also a couple of other devs that also use git locally.  
We are using git svn as the bridge between these 2.

If we want to share code or update each other it was generally the case that we would have to commit everything back to svn (git svn dcommit), then the others would have to rebase.  
It felt like I was abusing git and not making the most of it.

### Enter git pull
Git pull allows you to grab code from another git repo and store it in your local repo.

### Git pull
I'll presume you already have a git repo locally, and that there is another git repo somewhere you have access to.

First of all we add the remote location.
```bash
git remote add mp git.user@mpdevbox:/www/cm/
```
Let me break that command down

```bash
git remote add = The git command
mp = A friendly name you use for this remote location
git.user@mpdevbox = the remote user and the remote location
:/www/cm = The path to the remote git repo, (note the colon)
```

Having completed that command you now have a reference to a remote git repo, If you want to pull the code down from it, it's simple (you may want to create and checkout a branch before you do so, depending on your needs. I won't so will just pull)

```bash
git pull mp master
```

Where master is the name of your local branch you want to pull into.

This will pull down the code from the remote branch and merge it into your branch, if that all goes OK it will then perform a commit, if you don't wan the commit then just add --no-commit to the end of the pull command

If there are conflicts that git fails to resolve automatically then you will need to resolve them manually.  
This can be easily done using mergetool  

```bash
git mergetool
```

and follow the instructions.  

On Mac this will open a merge/diff client which lets you easily choose from your local and the pulled down repo. Once done you can commit if you want, there is even a pre-filled commit message for you about the merge.

It's as simple as that really, this approach also makes it much easier to develop on multiple machines, without having to get rid of SVN as your main repo (which depending on circumstances, requirements, or the people that make those decisions, may not be an easy sell).
