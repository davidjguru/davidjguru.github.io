---
layout: post
title: Drupal Fast Tips (VII) - Link fields from Twig  
permalink: /blog/drupal-fast-tips-link-fields-from-twig
published: true
date: 2021-04-30
author: davidjguru
categories: [Drupal & Coding]
sitemap: true
youtubeId: 6O192OAzMH8
---

| ![Picture from Unsplash, by @lazycreekimages]({{ site.baseurl }}/images/davidjguru_drupal_8_9_link_fields_from_twig_main.png) |
|:--:|
| *Picture from Unsplash, user [Michael Dziedzic, @lazycreekimages](https://unsplash.com/@lazycreekimages)* |  

In this new issue of the Drupal Fast Tips I would like to share some basic tips and examples related with the Drupal Link field and how to get the data from its sub-fields (title and link) to rendering in a Twig template. Sometimes we need render values from a link field in a structured way through a Twig template, and depending on the location of the Link field, this may have a different articulation.  
<!--more-->

Because of this, I wanted to show some small examples of extracting and handling the values of this particular type of fields. This will be a post for site-builders or developers with basic knowledge on the Drupal backend (yes, Twig and the rendering are in the backend, sorry.).  

---------------------------------------------------------------------------------------
<!-- /TOC -->
**This article is part of a series of posts about Drupal Tips.**

[1- Drupal Fast Tips (I) - Using links in Drupal 8](https://davidjguru.github.io/blog/drupal-fast-tips-using-links-in-drupal-8)  
[2- Drupal Fast Tips (II) - Prefilling fields in forms](https://davidjguru.github.io/blog/drupal-fast-tips-prefilling-fields-in-forms)  
[3- Drupal Fast Tips (III) - The Magic of '#attached'](https://davidjguru.github.io/blog/drupal-fast-tips-the-magic-of-attached)  
[4- Drupal Fast Tips (IV) - Xdebug, DDEV and Postman](https://davidjguru.github.io/blog/xdebug-ddev-and-postman)  
[5- Drupal Fast Tips (V) - Placing a block by code](https://davidjguru.github.io/blog/drupal-fast-tips-placing-a-block-by-code)  
[6- Drupal Fast Tips (VI) - From Arrays to HTML](https://davidjguru.github.io/blog/drupal-fast-tips-from-array-to-html)  
[7- Drupal Fast Tips (VII) - Link Fields from Twig](https://davidjguru.github.io/blog/drupal-fast-tips-link-fields-from-twig)  
<!-- /TOC -->

------------------------------------------------------------------------------------------------

## Building the link with separate items  

When you're getting the link field values from a viewand you've selected as formatter: `separate link element and text` in config.  

```
{% spaceless %}
  <div class="link-item">
    {% if title %}
      <div class="link-title">{{ title }}</div>
    {% endif %}
    <div class="link-url">{{ link }}</div>
  </div>
{% endspaceless %}
```

## :wq!

### Recommended song: Catalina - Rosalía & Refree

{% include youtubePlayer.html id=page.youtubeId %}
