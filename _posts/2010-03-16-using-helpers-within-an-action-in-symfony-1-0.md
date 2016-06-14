---
layout: post
title: Using helpers within an action in symfony 1.0
date: 16th March 2010
---

If you ever need to use a helper outside a template, you can still load a helper group from anywhere by calling

```php
<?php
sfLoader::loadHelpers($helpers)
```

where $helpers is a helper group name or an array of helper group names.
For instance, if you want to use simple_format_text() in an action, you need to call

```php
<?php
sfLoader::loadHelpers('Text');
```

I know this is covered in the symfony docs but it's a fairly handy thing to know so thought I would make it easier to find.
