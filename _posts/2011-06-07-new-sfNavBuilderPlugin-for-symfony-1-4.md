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

### Basic usage example

One main menu item, with 2 child items  

```php
    <?php
    // create a menu item for the main about page
    $homepage = new sfNavBuilderItem();
    $homepage
        ->setDisplayName('About Us')
        ->setUrl(url_for('about'))
        ->addActivateWhen(array(
            'module' => array('about'),
            'action' => array('index')
        ));
    // create a menu item for the about the team page and define the parent
    $team = new sfNavBuilderItem();
    $team
        ->setDisplayName('About the Team')
        ->setUrl(url_for('aboutTeam'))
        ->addActivateWhen(array(
            'module' => array('about'),
            'action' => array('team')
        ))
        ->setParent($homepage);
    // create a menu item for the about the company page and define the parent
    $company = new sfNavBuilderItem();
    $company
        ->setDisplayName('About the Company')
        ->setUrl(url_for('aboutCompany'))
        ->addActivateWhen(array(
            'module' => array('about'),
            'action' => array('company')
        ))
        ->setParent($homepage);

    // put the items into a menu instance and provide the required info
    $this->menu = new sfNavBuilder();
    $this->menu
        ->setRequest($request)
        ->setModule($this->getContext()->getModuleName())
        ->setAction($this->getContext()->getActionName())
        ->addItem($homepage);

    // in your template you can then call the renderer
    <?php echo $menu->render(); ?>
    // check out the sfNavBuilderRenderer class to see what this does
```


I have included some fuller examples of use within the readme of the project, but am happy to answer question you have when using it.

[Project page on github](https://github.com/catchamonkey/sfNavBuilderPlugin "Project page on github")  
[sfNavBuilderPlugin Project site](http://catchamonkey.github.com/sfNavBuilderPlugin "sfNavBuilderPlugin Project site")

Hope you find it useful!
