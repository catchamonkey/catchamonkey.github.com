---
layout: post
title: Git Manuals, Docs and Handy Links
date: 6th April 2010
---

As i am a recent git convert I find myself reading a lot of guides etc on the best way to use git and how my workflow can change now i'm using a DVCS.

This is an attempt to collate my more useful findings, partly for myself, but it may prove useful for you too.

###Git Books
<http://book.git-scm.com/index.html>  
<http://progit.org/book/>

###Git-SVN Resources
<http://orestis.gr/blog/2008/08/23/git-svn-tutorial/>

###Full Git online manual
<http://www.kernel.org/pub/software/scm/git/docs/v1.7.0.4/git.html>

###Combining multiple git commits into 1 SVN commit  
In some cases I use git as a local repo, but the main repo upstream is SVN. This sample enables me to combine multiple local git commits into a single commit back to SVN.
This was taken from [Stack Overflow](http://stackoverflow.com/questions/1408381/combine-local-git-commits-into-one-commit-with-git-svn "Stack Overflow git commit squashing")

{% highlight bash %}
git tag local # create a temporary tag
git reset --hard trunk
git merge --squash local
git commit # write your single commit message here
git svn dcommit
git tag -d local # delete the temporary tag named local</code>
{% endhighlight %}

Delicious
I also tend to bookmark a lot of stuff over at Delicious so you can check out my git stuff there too. Items tagged 'Git' at my Delicious