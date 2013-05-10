---
layout: post
title: Functional testing in Symfony with a different user provider
date: 7th December 2012
excerpt: How to use a different user provider (and authentication method) in your functional tests, than is used in your main app
---

The main security configuration for the application is form_login, with a custom authentication
 provider and user provider (these parts are farmed out to an API).

In functional tests I of course don't want to have the Auth API hit, as that
 isn't part of the application, it's separate and already tested.


{% highlight yaml %}
security:
    encoders:
        Symfony\Component\Security\Core\User\User:
            algorithm: plaintext
            encode_as_base64: false

    providers:
        in_memory:
            memory:
                users:
                    user: { password: somepassword, roles: ROLE_USER }

    firewalls:
        secure_area:
            http_basic:
{% endhighlight %}

{% highlight yaml %}
security:
    providers:
        in_memory:
            memory:
                users: ~
{% endhighlight %}
