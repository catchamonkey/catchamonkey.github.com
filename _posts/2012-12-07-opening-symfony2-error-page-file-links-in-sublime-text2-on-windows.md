---
layout: post
title: Opening Symfony2 Error page file links in Sublime Text 2 on Windows
date: 7th December 2012
excerpt: Have always loved this when using Textmate on OSX, now I can use it on Windows too!
---

During my day job, I work on a Windows 7 Machine, with mapped drives to my
 CentOS development environment.

Sublime Text 2 and Putty give me the file and shell access to work as if I were
 not using a desktop OS that sucks (albeit one that still has some annoying missing features).

I've wanted to map the framework: ide: setting in Symfony2 (also works in Symfony 1)
 so I could open the file links in error pages straight to Sublime at the right file and line,
 but I've never quite pursued it... until now!

There is a Sublime package that adds the protocol handler, and from there
 it's actually very easy, once I worked out the file path format required.

## Step 1)

Add the bitbucket URL as a repo in Sublime Text 2

    ctrl+shift+p
    https://bitbucket.org/sublimator/sublimeprotocol

## Step 2)

Then install the package

    ctrl+shift+p

Choose SublimeProtocol and press enter.

Note, for this step I had to launch Sublime Text 2 as an administrator due
 to the windows domain rules in the office.

## Step 3)

Configure the ide: setting in Symfony2

{% highlight yaml %}
#app/config/config.yml
framework:
    ide:    "sblm:///R/%%f:%%1"
{% endhighlight %}

In my case, the R drive is mapped to the root of my Dev environment.

%%f is the filepath and %%1 is the line number

So when you see a Symfony2 error page, the links will be mapped using this protocol

e.g.

    sblm:///R//home/sites/SymfonySite/vendor/symfony/etc.php:139

Just make sure your mapping of drive or folder location is correct.
