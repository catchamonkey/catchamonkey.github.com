---
layout: post
title: Auto persistent url params in symfony 1.4
date: 18th July 2011
excerpt: How to persist URL parameters during URL generation in symfony 1.4 with very little work
---

Imagine you have various pages and routes within your application, maybe you are displaying
data based on some URL parameters (filters for example).  

If you want to persist your filters between pages then you have a few choices.  

You could store the filters in the user's session making it easy, but they are not visible as a URL parameter.  
You could modify or create a new link_to/url_for function then modify your templates to use the new function.  

Neither of these appealed to me, I wanted the filters to be in the URL, and I didn't want to modify each link_to usage.

What I have done is created a new custom routing class.  
The class is invoked by configuring a route to use it, and you also tell it which URL params you want to persist.

```yaml
dashboard:
  url:   /dashboard
  class: ictPersistentParamRoute
  options:
    persist: [param1, param2]
  param: { module: dashboard, action: show }
```

The class extends sfRoute and overrides the generate() method.  
At URL generation time (usage of url_for/link_to etc) the class checks if the route
has persist options defined, and looks at sfWebRequest to see if these parameters are present.
If they are it will retrieve their values and merge the values to those defined in the (link_to) params.

This makes it very easy to add persistent params to your application without much effort!

```php
<?php
class ictPersistentParamRoute extends sfRoute
{
    public function generate($params, $context = array(), $absolute = false)
    {
        // our work happens here
    }
}
```
