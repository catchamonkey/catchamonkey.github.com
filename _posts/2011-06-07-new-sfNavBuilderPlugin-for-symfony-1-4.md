---
layout: post
title: New sfNavBuilderPlugin for symfony 1.4
date: 7th June 2011
excerpt: Create your symfony menu's using a simple interface, with support for active state definitions
---

I released this plugin yesterday, which offers a simple interface with which to define and render your symfony 1.4 menu's  

The plugin allows you to create a hierarchical menu, with each item having it's active state defined by;  

* Current module
* Current action
* Parameter=value

This therefore allows you to say you want an item to be active if;

    module = blogPost
    action = show
    a paramName of post_id = a paramValue of 1
    
I have included some examples of use within the readme of the project, but am happy to answer question you have when using it.

[Project page on github](https://github.com/catchamonkey/sfNavBuilderPlugin "Project page on github")  
[sfNavBuilderPlugin Project site](http://catchamonkey.github.com/sfNavBuilderPlugin "sfNavBuilderPlugin Project site")

Hope you find it useful!