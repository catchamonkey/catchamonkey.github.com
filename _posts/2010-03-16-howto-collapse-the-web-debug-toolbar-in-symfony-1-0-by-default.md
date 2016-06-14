---
layout: post
title: How-to collapse the web debug toolbar in symfony 1.0 by default
date: 16th March 2010
---

To automatically collapse, hide, shrink (or whatever you want to call it) the web debug toolbar to just the SF and close icons; add this line

```js
window.onload=sfWebDebugToggleMenu;
```

to the end of this file

```
...\symfony\web\sf\sf_web_debug\js\main.js
```

This handy little tip courtesy of IsRobot.
