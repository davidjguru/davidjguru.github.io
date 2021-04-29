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

Every Drupal Site Builder works with blocks, blocks are a basic and essential piece of functionality in Drupal and a very important building element in Drupal-based projects. And one of the most important phases of working with blocks in Drupal is managing the rules for the visibility of this important resource: We need determine when show it and when hide it.  

We have all had at one time or another, to work with visibility conditions for a Block. It happens when we're using the config page of a Block in Drupal, seeing something like this:  

![Basic visibility conditions for Blocks in Drupal]({{ site.baseurl }}/images/davidjguru_drupal_8_9_condition_plugins_for_visibility_1.png)

Well, for this post I was thinking on writing about how to expand these visibility conditions for our own custom needs in a Drupal Project. We're going to talk about the Condition Plugins in Drupal.  

## What are Condition Plugins? 

Although there is not a lot of information on this subject, nor does it form part of the documented APIs of Drupal, We can assemble interpretative pieces about how this small extensible sub-system based on Drupal Plugins works.  
Basically, we can say that Condition Plugins are an extensible way to generate new visibility conditions for Blocks in Drupal, based in the format of Plugins that you can implement following the Drupal rules for Plugins.  

The Condition Plugins are context-aware (many of them requires explicit context in its annotations blocks), but let's see some information located within the Drupal documentation:  

From the [Condition Plugin System](https://www.drupal.org/node/1961370), as was initially described in 2013: 

> _"To implement a condition in a module create a class in {module}/src/Plugin/Condition/{ConditionName}.php and extend ConditionPluginBase (which implements ConditionInterface). The class must also declare a plugin annotation in its docblock comment."_  
> 

And about the Contexts, from the [Plugin Contexts Definition](https://www.drupal.org/docs/drupal-apis/plugin-api/plugin-contexts):  

> _"Sometimes plugins require another object in order to perform their primary operation. This is known as plugin context. Using a practical example, almost all condition plugins require a context. Let's look at the NodeType condition's plugin definition [...] The context_definitions key stores an array of named context definitions the condition requires in order to perform its "evaluate()" method."_  

Actually, the Condition Plugin it's not a complicated concept, but it's true that you need to know quite well some basic previous steps in order to reach here the [Satori](https://en.wikipedia.org/wiki/Satori). These are key concepts for Drupal Development and maybe you'll need understand in deep the next topics:  
* [Creating Custom Modules in Drupal](https://www.drupal.org/docs/creating-custom-modules).  
* [The Plugin API in Drupal](https://www.drupal.org/docs/drupal-apis/plugin-api).  
* [Annotations Based Plugins in Drupal](https://www.drupal.org/docs/drupal-apis/plugin-api/annotations-based-plugins).  
* [Guide: How to integrate JavaScript in Drupal 8-9](https://www.therussianlullaby.com/blog/guide-how-to-integrate-javascript-in-drupal-8-9/)  

## Existing Condition Plugins in your Drupal Installation  

You can discover some existing Condition Plugins available in your Drupal installation from different core modules:   

**Current Condition Plugins in Core:**

* [NodeType Condition Class](https://api.drupal.org/api/drupal/core%21modules%21node%21src%21Plugin%21Condition%21NodeType.php/class/NodeType/9.2.x): namespace Drupal\node\Plugin\Condition, in core/modules/node  
* [RequestPath Condition Class](https://api.drupal.org/api/drupal/core%21modules%21system%21src%21Plugin%21Condition%21RequestPath.php/class/RequestPath/9.2.x): namespace Drupal\system\Plugin\Condition, in core/modules/system  
* [UserRole Condition Class](https://api.drupal.org/api/drupal/core%21modules%21user%21src%21Plugin%21Condition%21UserRole.php/class/UserRole/9.2.x): namespace Drupal\user\Plugin\Condition, in core/modules/user  

These previous Plugins are the three basic items availables by default in a Block Visibility Configuration, I mean:  

![Condition Classes related to basic visibility options]({{ site.baseurl }}/images/davidjguru_drupal_8_9_condition_plugins_for_visibility_2.png)  

But there are a few more in other locations (in addition to some used in test classes):  

* [CurrentTheme Condition Class](https://api.drupal.org/api/drupal/core%21modules%21system%21src%21Plugin%21Condition%21CurrentThemeCondition.php/class/CurrentThemeCondition/9.2.x): namespace Drupal\system\Plugin\Condition, in core/modules/system  
* [Language Condition Class](https://api.drupal.org/api/drupal/core%21modules%21language%21src%21Plugin%21Condition%21Language.php/class/Language/9.2.x): namespace Drupal\language\Plugin\Condition, in core/modules/language  
  
**And the central Interface for Conditions:**  
* [ConditionInterface](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Condition%21ConditionInterface.php/interface/ConditionInterface/9.2.x): namespace Drupal\Core\Condition, in core/lib/Drupal/Core/Condition  

## Available Condition Plugins in Drupal Contrib Modules  

As in Drupal Core you can find Condition Plugins in Contrib modules too. For instance, there are some modules developer by [Cambrico](https://www.drupal.org/cambrico) with many resources within:   

* [Drupal Module Condition Plugins](https://www.drupal.org/project/condition_plugins)  

* [Drupal Module Condition Plugins Commerce](https://www.drupal.org/project/condition_plugins_commerce)  

And one of the biggest contrib modules of Drupal -[Webform](https://www.drupal.org/project/webform)- also provides its own Plugin for Conditions:  
* [Webform Condition](https://git.drupalcode.org/project/webform/-/blob/6.x/src/Plugin/Condition/Webform.php)  

## Building your own Condition Plugin 

Ok, now let's go to build something useful. We already know conceptually what they are, what they do and where cand find them. So is the time to implement Condition Plugins!.  

### Our Goals
For this case, I have come up with a very simple and straightforward idea:  
We want to show a block only for nodes of type "Article" when they have checked a field. 

In order to prepare this, I made the quickest way:  
* First I added a new field for the content type "Article", the new "field_selected_article_check".  
* Then I created a new custom block "My Block" in the most easy way, just clicking in /block/add.  
* Finally I implemented scaffolding for a new custom module, called "visibility_conditions"  


![Placing Block and new field for the example]({{ site.baseurl }}/images/davidjguru_drupal_8_9_condition_plugins_for_visibility_3.png)  


### Annotations 


### Configuration 


### Evaluate 


### Summary

### Some Futher Details  

In the previous section, we took care of preparing a Summary, but this is only available at code level, it's not visible in GUI. Could we improve the usability of our new element by adding this summary? Yes, it's possible and for this we can rely on a jQuery function in Drupal (sorry).  

I introduce you the function "drupalsetSummary", I discovered it consulting problems [in StackExchange](https://drupal.stackexchange.com/a/252723/94320) and then I identified it in real cases like [the JavaScript added for Nodes in the Drupal core](https://git.drupalcode.org/project/drupal/blob/HEAD/core/modules/node/node.js#L12). I haven't found much information about it, so I can't explain much more than this: "it serves to place summaries" ¯\_(ツ)_/¯ . Well, ok.  

The important thing is that I was playing with this function and it seems to work well for inserting elements. The pre-conditions are that JavaScript must be added to our custom module and that we must use the Drupal Behaviors format. Please review [this Drupal - JavaScript integration guide: Drupal Behaviors](https://www.therussianlullaby.com/blog/guide-how-to-integrate-javascript-in-drupal-8-9/#6--drupal-behaviors) in order to get more info about the Drupal Behaviors format and how implementing it.  


 

## :wq!

### Recommended song: Catalina - Rosalía & Refree

{% include youtubePlayer.html id=page.youtubeId %}
