---
layout: post
title: Condition Plugins for Visibility in Drupal  
permalink: /blog/condition-plugins-for-visibility-in-drupal
published: false
date: 2021-04-24
author: davidjguru
categories: [Drupal & Coding]
sitemap: true
youtubeId: 6O192OAzMH8
---

| ![Picture from Unsplash, by @lazycreekimages]({{ site.baseurl }}/images/davidjguru_drupal_8_9_condition_plugins_for_visibility_main.png) |
|:--:|
| *Picture from Unsplash, user Michael Dziedzic, [@lazycreekimages](https://unsplash.com/@lazycreekimages)* |  

Following on from my previous post about "200 Linux commands for everyday life" that I published a few months ago -September 2020, [here](https://davidjguru.github.io/blog/200-linux-commands-for-everyday-life)-, so today I want to share another small article about little everyday utilities. The previous post had a more extensive (and intense) feedback than when I'm writing about Drupal, so this has invited me to think that maybe (just maybe) it could be useful content for someone.  
<!--more-->

From the [Condition Plugin System](https://www.drupal.org/node/1961370), as was described in 2013: 

> _"To implement a condition in a module create a class in {module}/src/Plugin/Condition/{ConditionName}.php and extend ConditionPluginBase (which implements ConditionInterface). The class must also declare a plugin annotation in its docblock comment."_  

From the [Plugin Contexts Definition](https://www.drupal.org/docs/drupal-apis/plugin-api/plugin-contexts):  

> _"Sometimes plugins require another object in order to perform their primary operation. This is known as plugin context. Using a practical example, almost all condition plugins require a context. Let's look at the NodeType condition's plugin definition [...] The context_definitions key stores an array of named context definitions the condition requires in order to perform its "evaluate()" method."_  

[](https://www.drupal.org/project/condition_plugins)  

[](https://www.drupal.org/project/condition_plugins_commerce)  

## :wq!

### Recommended song: Catalina - Rosalía & Refree

{% include youtubePlayer.html id=page.youtubeId %}
