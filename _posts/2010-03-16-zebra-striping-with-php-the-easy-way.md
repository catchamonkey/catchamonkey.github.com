---
layout: post
title: Zebra Striping with PHP the Easy Way
date: 16th March 2010
---

Using one of the built in Maths functions of PHP we can easily create zebra striped lists.  
Let's say we have an array of entries stored within the $comments variable.  
All we have to do with this is to use the fmod function and assign a css class that alternates per entry.

```php
<ul>
    <?php foreach ($comments as $int => $comment): ?>
        <li class="<?php echo fmod($int, 2) ? 'even' : 'odd' ?>">
    <?php endforeach; ?>
</ul>
```

Then just give the even and odd classes different colours to suit your layout.

```css
li.even {
  background-color:#FFFFFF;
  color:#000000;
}

li.odd {
  background-color:#000000;
  color:#FFFFFF;
}
```

And you're done!
