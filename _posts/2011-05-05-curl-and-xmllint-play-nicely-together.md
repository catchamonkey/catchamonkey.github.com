---
layout: post
title: Curl and xmllint play nicely together
date: 5th May 2011
excerpt: Fetching XML via curl is useful for debugging API responses, especially if they are locked down to certain IP addresses, take a look to see how to have the ouput nicely formatted rather than a single long string.
---
If you work a lot with XML API's (writing or consuming), and have ever called one directly using curl to test/debug some output, you may find that the output isn't very friendly, and for larger responses it's almost unreadable.

By using curl and xmllint together you can have your response nicely formatted.

To do so, invoke curl, and parse the response through xmllint, giving xmllint the --format option and the - option which tells it to read from stdin

{% highlight sh %}
curl -sL "http://api.example.com/assets.xml" | xmllint --format -
{% endhighlight %}

The curl options used here are -s to invoke silent mode, and -L so it follows redirects.

[Read more about xmllint here](http://xmlsoft.org/xmllint.html "Read more about xmllint here") or view the man pages directly if you have it installed.