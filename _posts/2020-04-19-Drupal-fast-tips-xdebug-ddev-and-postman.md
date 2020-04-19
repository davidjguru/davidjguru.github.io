---
layout: post
title: Drupal Fast Tips (IV) - Xdebug, DDEV and Postman
permalink: /blog/drupal-fast-tips-xdebug-ddev-and-postman
published: true
date: 2020-04-19
author: davidjguru
categories: [Tools]
sitemap: true
---

| ![Picture from Unsplash, by @timothycdykes]({{ site.baseurl }}/images/davidjguru_drupal_fast_tips_IV_main.jpg) |
|:--:|
| *Picture from Unsplash, user [Timothy Dykes, @timothycdykes](https://unsplash.com/@timothycdykes)* |

Last Friday I had to perform several code debugging to review several use cases based on the interconnections between a data bus and a Drupal installation. It has been a long time since I debugged REST and in this time, I have gone from using Drupal in a custom LAMP / Docker environment to working only and exclusively with DDEV based Drupal environments.  
<!--more-->
So "remodeling" at that level the work stack has made me want to take some notes, for the joy of using something as easy as DDEV for the development in a local environment.  

This will be an article about configurations for testing REST queries from Postman while debugging requests from the Drupal side connecting Xdebug between PHPStorm and DDEV.  

---------------------------------------------------------------------------------------
<!-- /TOC -->
**This article is part of a series of posts about quick Drupal Tips.
(Random, partial and brief -or almost-):**  

[1- Drupal Fast Tips (I) - Using links in Drupal 8](https://davidjguru.github.io/blog/drupal-fast-tips-using-links-in-drupal-8)  
[2- Drupal Fast Tips (II) - Prefilling fields in forms](https://davidjguru.github.io/blog/drupal-fast-tips-prefilling-fields-in-forms)  
[3- Drupal Fast Tips (III) - The Magic of '#attached'](https://davidjguru.github.io/blog/drupal-fast-tips-the-magic-of-attached)  
[4- Drupal Fast Tips (IV) - Xdebug, DDEV and Postman](https://davidjguru.github.io/blog/xdebug-ddev-and-postman)  
<!-- /TOC -->

------------------------------------------------------------------------------------------------

## Introduction 

In this article I would like to offer a simple scheme to prepare the configuration of a sufficient test environment to test REST to Drupal connections. Just a quick recipe for the tools needed to build the test environment. 

**Ingredients:**

1. Xdebug (the classical extension for PHP for debugging).  
2. PHPStorm (Well, or you opensourcered IDE).  
3. DDEV (The containerising system Docker-based for PHP projects).  
4. Drupal and some contrib modules (but not many, actually)  
5. Postman or Postwoman, as you wish (for API REST testing).  


**Recipe:**
[Xdebug](https://xdebug.org/)

[PHPSTORM]()

[DDEV](https://ddev.readthedocs.io)
**Did I already say that working with DDEV means reaching the Satori?** If you haven't read me about this topic, I recommend you get to [know the tool](https://ddev.readthedocs.io/en/stable/) or visit some of the articles I have already published, here or at my other website [https://www.therussianlullaby.com](https://www.therussianlullaby.com):

* [Creating development environments for Drupal with DDEV](https://www.therussianlullaby.com/blog/creating-development-environments-for-drupal-with-ddev/).  
* [Docker, Docker-Compose and DDEV - Cheatsheet](https://www.therussianlullaby.com/blog/docker-docker-compose-and-ddev-cheatsheet/).  
* [Books/ Local Web development with DDEV](https://www.therussianlullaby.com/blog/books-local-web-development-with-ddev-explained/).  

You need to install the Docker Engine for DDEV, so [review the installing process for DDEV](https://ddev.readthedocs.io/en/stable/#installation) and put it running in your machine. 

[Drupal Modules]()

[Postman](https://www.postman.com/)


## Xdebug on DDEV and true happiness
With the DDEV-based Drupal installation, you will have Xdebug installed by default, just make sure you have the extension enabled in your DDEV installation's own configuration for the project, through the config.yaml file, by setting the ``xdebug_enabled`` configuration variable to true:

![Enabling Xdebug in a DDEV installation for Drupal]({{ site.baseurl }}/images/davidjguru_drupal_fast_tips_IV_xdebug_config.png)

After changing this, you will only have to restart the web container to take the new value and have Xdebug available. 


## Config PHPStorm



## From Postman to your Drupal installation 


## :wq!