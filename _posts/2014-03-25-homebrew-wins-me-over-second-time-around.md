---
layout: post
title: Homebrew wins me over second time around
date: 25th March 2014
excerpt: Second times a charm (or something like that)
---

tl;dr. I'm a [homebrew](http://brew.sh/ "check out homebrew") convert.  

I've been a Mac user for personal projects for about 5/6 years, but my work environments have generally enforced Windows.  
I used FreeBSD VMs (that I set up) in a previous company, and currently we use CentOS in OpenVZ.

I gave homebrew a try when I first got my Macbook Pro, but had mixed results, the version of PHP wasn't available with the extensions I needed, and other things.  
I ended up building all my requirements from source. Apache, PHP, libxml2 etc, and it was fine, all worked well, but maintaining updates for one machine always felt like overkill.

Last year, I won over the company/boss, with the idea of having the machine to develop on that you wanted, (given the choice between Windows, Fedora or OSX). So I now use a Macbook Air and Thunderbolt display, yay!

Despite us still using VMs of CentOS (which of course makes sense, so it matches our live environment, same packages etc), I still wanted to have the Mac setup so I could develop when offline, and play around without the overhead of a remote connection.

The maintainers there have done a great job in the past few years, From my POV, it's much more robust now, and handles many more of the cases a PHP dev needs, even when living on the cutting edge of versions.


I would have no hesitation in recommending it to anyone looking for a decent package manager for OSX, in lieu of the ports tree!

{% highlight bash %}
brew install wget
{% endhighlight %}
