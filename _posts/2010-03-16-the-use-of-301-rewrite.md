---
layout: post
title: The use of 301 rewrite
date: 16th March 2010
---

If you are hosting a domain (or multiple domains) using VirtualHost entries, it is bad practice to use individual entries for the same domain just to cover the inclusion or exclusion of the www in the address, amongst other things it is bad for SEO, but of course you still want visitors to be able to go to your site via the 2 starting addresses http://sedlmayr.co.uk and http://www.sedlmayr.co.uk

For example you should not have an entry for www.sedlmayr.co.uk and another for sedlmayr.co.uk
There are a few ways you can do this but have found this particular one to be effective.
Whatever url you go to; http://sedlmayr.co.uk or http://www.sedlmayr.co.uk a redirect happens on the http://sedlmayr.co.uk that sends you to http://www.sedlmayr.co.uk.

But what about people that go to specific pages such as http://sedlmayr.co.uk/services

We use a RewriteRule so that whatever is after the domain itself (in this case services)(When using http://sedlmayr.co.uk) http://sedlmayr.co.uk/services is shown as http://www.sedlmayr.co.uk/services
It is very simple to achieve this.

Within you VirtualHost entry you need to specify a ServerAlias. So I would have
{% highlight apache %}
ServerName www.sedlmayr.co.uk
ServerAlias sedlmayr.co.uk
{% endhighlight %}
And then within the vhost file for your project you would have

{% highlight apache %}
RewriteCond %{HTTP_HOST} ^sedlmayr\.co.uk
RewriteRule ^(.*)$ http://www.sedlmayr.co.uk/$1 [R=301,L]
{% endhighlight %}
Which needs to go at the top.
That's it!
Hope this is helpful to you at some point.