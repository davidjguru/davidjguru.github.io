---
layout: post
title: Drupal Fast Tips (I) - Using links in Drupal 8
permalink: /blog/drupal-fast-tips-using-links-in-drupal-8
published: true
date: 2019-08-15
categories: [Drupal]
sitemap: true
---
| ![Picture from Unsplash, by @osmanrana]({{ site.baseurl }}/images/davidjguru_drupal_8_using_links_from_unsplash_1.jpeg) |
|:--:|
| *Picture from Unsplash, user Osman Rana @osmanrana* |


The first thing I need to clarify is that we are not going to talk about
***"using links"*** in Drupal 8. We're actually going to talk about how to
 build 
custom links in Drupal 8.  Well, I wanted to write about this but because of
the character limitation of the title string I used that form. 
So keep reading if you want to know how to build links programmatically in Drupal 8. 
Do you follow me?...
<!--more-->

## Introduction
In the context of the Internet, any web application has many, thousands of
 links. Knowing how to operate with links (and above all how they work) within Drupal is a fundamental tool to build projects programmatically. We know how to do it through sitebuilding...but what about code? We can start by imagining it as a small two-step process for which we will need to meet two new friends from the Drupal API. 

## Your new key friends
There are two fundamental classes we need to know for this: First, a PHP
 class that belongs to Drupal's core called "Url" and another class from the
  core called "Link". Very intuitive, isn't it? Well now we'll see why these
   two new friends are so important. 

### The URL Class
The URL class is used to model an object that contains information about a
 URL (its name, whether it is absolute or relative, internal, external, etc.).  URLs in Drupal 8 are represented through this class with namespace Drupal\Core\Url. Ok. This class has a set of static methods indicated for building a URL. Specifically we will stop in two interesting cases that this class provides us: 
  1. The ability to build URLs from paths declared in routing files (my_module
  .routing.yml) through the ::fromRoute() static method. 
  2. The ability to build URLs from a URI address (internal or external
  ), using the ::fromUri() static method. 
  **Examples**
  
   ```php
      use Drupal\Core\Url;

     // Link to the Drupal Home page (declared in the file 'system.routing.yml')
     $url1 = Url::fromRoute('<front>');

     // Link to /admin/people (declared in the file 'user.routing.yml')
     $url2 = Url::fromRoute('entity.user.collection');

     // External Link to Drupal.org.
     $url3 = Url::fromUri('https://www.drupal.org');
   ```
 
* See more at: [The URL Class in the Drupal API Documentation](https://api
.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Url.php/class/Url/8.8.x){:target="_blank"}

### The Link Class

## How it works

## Example: case use