---
layout: post
title: Drupal Fast Tips (VI) - From arrays to HTML
permalink: /blog/drupal-fast-tips-from-array-to-html
published: true
date: 2021-01-30
author: davidjguru
categories: [Drupal & Coding]
sitemap: true
youtubeId: UpEkYeBJqVo
---
| ![Picture from Unsplash, by @auntneecey]({{ site.baseurl }}/images/davidjguru_drupal_8_9_from_arrays_to_HTML_main.png) |
|:--:|
| *Picture from Unsplash, user [Denise Johnson](https://unsplash.com/@auntneecey)* |


Hello everybody! for this post I'm going to stop in a very effective utility for observing the direct relationship between the so-called "Render Arrays" of Drupal and the HTML final code itself, that in many occasions we'll generating for our Drupal site. I was working on rendering some elements and thought it might be interesting to show that transition exists between a Drupal array and the HTML that will be inserted in the response from our server response, interpreting what Drupal really does from a Render Array with lot of properties and values inside.  
<!--more-->

I'm going to play with an array, passing it by a render service and getting the result transformation. This post was thought just like a short article. Let's see.  

---------------------------------------------------------------------------------------
<!-- /TOC -->
**This article is part of a series of posts about Drupal Tips.**

[1- Drupal Fast Tips (I) - Using links in Drupal 8](https://davidjguru.github.io/blog/drupal-fast-tips-using-links-in-drupal-8)  
[2- Drupal Fast Tips (II) - Prefilling fields in forms](https://davidjguru.github.io/blog/drupal-fast-tips-prefilling-fields-in-forms)  
[3- Drupal Fast Tips (III) - The Magic of '#attached'](https://davidjguru.github.io/blog/drupal-fast-tips-the-magic-of-attached)  
[4- Drupal Fast Tips (IV) - Xdebug, DDEV and Postman](https://davidjguru.github.io/blog/xdebug-ddev-and-postman)  
[5- Drupal Fast Tips (V) - Placing a block by code](https://davidjguru.github.io/blog/drupal-fast-tips-placing-a-block-by-code)  
[6- Drupal Fast Tips (VI) - From Arrays to HTML](https://davidjguru.github.io/blog/drupal-fast-tips-from-array-to-html)  
<!-- /TOC -->

------------------------------------------------------------------------------------------------

## Introduction   

I will use a single Drupal local installation from myself. In my local environtment I have a Drupal 9 deploy for testing, using DDEV-local as deploying tool. See more about [DDEV-Local](https://ddev.readthedocs.io/en/stable/) and know how deploy Drupal installations in some steps from a single snippet in my gitlab profile: [Drupal 9 in six steps using DDEV: Quick Deploy](https://gitlab.com/-/snippets/2012512).   

I will use a single custom module available in my gitlab profile: 'Managing Activities', in order to get some structured arrays and testing the main idea. Use, downloading or cloning the code from here: [gitlab.com/davidjguru/custom-modules/managing_activities)](https://gitlab.com/davidjguru/drupal-custom-modules-examples/-/tree/master/managing_activities) or from the main folder here: [gitlab.com/davidjguru/custom-modules](https://gitlab.com/davidjguru/drupal-custom-modules-examples). **Please:** These module aren't for Stage or Live / Prod. Modules only for dev, testing and fun.  

I will use Xdebug in order to get internal info from some variables and follow the processing of the original Render Array, step by step. Cause I'm using DDEV-Local in all my local deploys, for me is very easy using and enabling Xdebug in Drupal projects. It's quite fast, only go to your Drupal installation by prompt and ask:  
```
local-project$ ddev xdebug on
```
And so Xdebug is enabled and ready to use. If you don't know DDEV, please read these related articles and resources from here, my main website [www.therussianlullaby.com](https://www.therussianlullaby.com) and my repositories:  

* [Development environments for Drupal with DDEV](https://www.therussianlullaby.com/blog/creating-development-environments-for-drupal-with-ddev/)  
* [Docker, Docker-Compose and DDEV - Cheatsheet](https://www.therussianlullaby.com/blog/docker-docker-compose-and-ddev-cheatsheet/) 
* [Drupal Fast Tips (IV) - Xdebug, DDEV and Postman](https://davidjguru.github.io/blog/drupal-fast-tips-xdebug-ddev-and-postman)  
* [Books/ Local Web development with DDEV](https://www.therussianlullaby.com/blog/books-local-web-development-with-ddev-explained/)  
* [Installing Docker, Docker Compose and DDEV in Ubuntu - Bash script](https://github.com/davidjguru/scripting_for_drupal/blob/main/installers/tools_and_environment/installing_docker_dockercompose_ddev)  


## Render Arrays 

As we already know, the so called "Render arrays" in Drupal are just multidimensional PHP "arrays", which as we have already seen here: [davidjguru.github.io/php-performance#41--php-under-the-hood](https://davidjguru.github.io/blog/playing-with-php-8-performance#41--php-under-the-hood), are not specifically arrays as we know from another programming languages, but dynamic and relatively complex structures created in C language. These kind of arrays have a very-very extensive use in Drupal, just like an specific Drupalism.  


## The Renderer service 

## Testing the array 

## Get more info

* Renderer Service in core.services.yml for Drupal 9: [api.drupal.org/core.services.yml/renderer](https://api.drupal.org/api/drupal/core%21core.services.yml/service/renderer/9.2.x)

* Drupal's render() replaced with Renderer service in Drupal 8 || 9: [drupal.org/node/2939099](https://www.drupal.org/node/2939099)

* The Render Plain function for Drupal 8.x[api.drupal.org/Renderer::renderPlain](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21Renderer.php/function/Renderer%3A%3ArenderPlain/8.2.x)

* The Drupal 8 || 9 Render pipeline: [drupal.org/the-drupal-8-render-pipeline](https://www.drupal.org/docs/8/api/render-api/the-drupal-8-render-pipeline) 

* The Drupal 8 || 9 Render API: [drupal.org/drupal-apis/render-api](https://www.drupal.org/docs/drupal-apis/render-api) 

## 6- :wq!

### Recommended song

{% include youtubePlayer.html id=page.youtubeId %}