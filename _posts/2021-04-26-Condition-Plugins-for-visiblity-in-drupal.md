---
layout: post
title: Condition Plugins for Visibility in Drupal  
permalink: /blog/condition-plugins-for-visibility-in-drupal
published: false
date: 2021-04-26
author: davidjguru
categories: [Drupal & Coding]
sitemap: true
youtubeId: 6O192OAzMH8
---

| ![Picture from Unsplash, by @lazycreekimages]({{ site.baseurl }}/images/davidjguru_drupal_8_9_condition_plugins_for_visibility_main.png) |
|:--:|
| *Picture from Unsplash, user Michael Dziedzic, [@lazycreekimages](https://unsplash.com/@lazycreekimages)* |  

Hello there! In this new post I want to focus on a very interesting topic of Drupal, which pertains to its extension capabilities through its Plugins system. It is not a very extensive topic, but it is also true that there is not much documentation about it. This is a very common case when you're building some kinds of Drupal Blocks in order to render specific data and you need that its visualization and its behaviour responds to special conditions.  
<!--more-->

We have all had at one time or another, to work with visibility conditions FOR a Block. It happens when we're using the config page of a Block in Drupal, seeing something like this:  

![URL aliases section in Drupal 8 or 9]({{ site.baseurl }}/images/davidjguru_drupal_8_9_condition_plugins_for_visibility_1.png)

Well, for this post I was thinking on writing about how to expand these visibility conditions for our own custom needs in a Drupal Project. We're going to talk about the Condition Plugins in Drupal.  


## What are Condition Plugins? 

From the [Condition Plugin System](https://www.drupal.org/node/1961370), as was described in 2013: 

> _"To implement a condition in a module create a class in {module}/src/Plugin/Condition/{ConditionName}.php and extend ConditionPluginBase (which implements ConditionInterface). The class must also declare a plugin annotation in its docblock comment."_  

From the [Plugin Contexts Definition](https://www.drupal.org/docs/drupal-apis/plugin-api/plugin-contexts):  

> _"Sometimes plugins require another object in order to perform their primary operation. This is known as plugin context. Using a practical example, almost all condition plugins require a context. Let's look at the NodeType condition's plugin definition [...] The context_definitions key stores an array of named context definitions the condition requires in order to perform its "evaluate()" method."_  

 

## Existing Condition Plugins in your Drupal Installation  



## Available Condition Plugins in Drupal Contrib Modules  

[](https://www.drupal.org/project/condition_plugins)  

[](https://www.drupal.org/project/condition_plugins_commerce) 

[]()  

## Building your own Condition Plugin 




## :wq!

### Recommended song: Catalina - Rosalía & Refree

{% include youtubePlayer.html id=page.youtubeId %}
