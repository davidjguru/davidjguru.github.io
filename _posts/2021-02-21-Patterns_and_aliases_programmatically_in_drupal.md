---
layout: post
title: Patterns and Aliases programmatically in Drupal
permalink: /blog/patterns-and-aliases-programmatically-in-drupal
published: true
date: 2021-02-21
author: davidjguru
categories: [Drupal & Coding]
sitemap: true
youtubeId: wqfeeRz_fwE
---

| ![Picture from Unsplash, by @jack_1]({{ site.baseurl }}/images/davidjguru_8_9_patterns_and_ aliases_programmatically_in_drupal_main.jpg) |
|:--:|
| *Picture from Unsplash, user [Rémi Jacquaint](https://unsplash.com/@jack_1)* |

Sometimes in an initial phase of a Drupal project you need prepare some kind of tasks related with routing: creates new routes, defines some Controllers or Forms o maybe just define the so called "aliases". The alias allows us define URLs more friendly than the natural, or even thinking in build some patterns in order to apply to our content or entities (users, taxonomy terms, nodes, etc). I'm talking about pretty and semantic URLs like "mydomain.com/blog/article/special-article". Drupal allows define one by one these kinds of directions, but also we can work with batch processing, organizing the work from the code side. Let's see some ideas and examples and we're going to take a walk around [the pathauto module for Drupal](https://www.drupal.org/project/pathauto). Pay attention.  
<!--more-->

## Introduction 

For some ideas and side-projects, I was thinking in enabling sets of taxonomy terms from an external source, and I could move the data just like a migration and also like part of the install process of a custom module. For this case, I'll use the approach as a simple custom Drupal module created for register some data, creating vocabulary, taxonomy terms and aliases but without using Migrations (I guess that I'll write about migrations of patterns for aliases next months in [www.therussianlullaby.com](https://www.therussianlullaby.com/)). I just wanna play with functions in a simple way in order to show how we can work along with a special kind of Drupal Entity: [The PathautoPattern Entity](https://git.drupalcode.org/project/pathauto/-/blob/8.x-1.x/src/Entity/PathautoPattern.php), provisioned by [the Pathauto module for Drupal](https://www.drupal.org/project/pathauto).  

By default Drupal implements `node/nid` or `taxonomy/term/tid` URL paths for entities and bundles. This is very easy to test, just creating a node in Drupal and seeing its related URL after published: `/node/4`. Ok. 

The Pathauto module offers some interesting options to update URLs related with specific entities in your Drupal installation (content, taxonomy terms, users), giving support for tokens, bulk updates and automatic generation of aliases by creating patterns directly related with entities (patterns for vocabularies but also for certain vocabularies, for example). The module works from a User Interface in your Drupal installation, in path `http://example-drupal.ddev.site/admin/config/search/path/patterns` and its tabs.  In the basement, there's a very interesting concept in order to work with patterns: the PathautoPatter Entity. This post talk about working with this Drupal entity from a programmatic point of view. We're going to do some tasks not from the UI, but from custom code.  


#### Recipe 

Well, for this recipe I'll use some of my usual ingredients:   

* A DDEV-Local based environment (Dockerized et al) for a Drupal 9 installation. Follow this guide from Digital Ocean: [digitalocean.com/deploy-drupal-using-docker-and-ddev](https://www.digitalocean.com/community/tutorials/how-to-develop-a-drupal-9-website-on-your-local-machine-using-docker-and-ddev).  
* A Drupal 9 deployed in your local. Follow the snippet: [Drupal 9 in six steps using DDEV: Quick Deploy](https://gitlab.com/-/snippets/2012512).  
* The Pathauto contrib module installed and enabled with its dependencies. Follow the Snippet: [Drupal 8-9 - Install Pathauto module from a DDEV deploy](https://gitlab.com/-/snippets/2080056).  
* A new custom Drupal module created by using `drush generate`. Follow the Snippet:  [Drupal 8-9 - Creating custom resources using Drush generate](https://gitlab.com/-/snippets/2054593).  

#### Extra 
For this post, I'm using a simple [hook_install()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Extension%21module.api.php/function/hook_install/9.0.x) as a playground. All the code was developed inside this classic hook that executes just in the installing process. You can use the same services, classes and resources from a more OOP context with Dependency Injection in Drupal.  

## Adding new patterns by code 

## Creating an alias for a item 

## Applying alias patterns to a complete vocabulary 




```
$ ddev composer require drupal/pathauto 
$ ddev drush en pathauto
```

{% gist 590bf212c2a31528ea872a27f7bf3443 %} 

```
entity.pathauto_pattern.add_form:
  path: '/admin/config/search/path/patterns/add'
  defaults:
    _entity_form: 'pathauto_pattern.default'
    _title: 'Add Pathauto pattern'
    tempstore_id: 'pathauto.pattern'
  requirements:
    _permission: 'administer pathauto'
```

```
$ ddev drush en testing_pathauto
```



https://www.specbee.com/blogs/drupal-pathauto-module-brief-tutorial-how-automatically-generate-bulk-url-aliases-drupal-8

## :wq!

### Recommended song: Asilos Magdalena - The Mars Volta

{% include youtubePlayer.html id=page.youtubeId %}
