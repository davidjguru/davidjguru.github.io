---
layout: post
title: Patterns and Aliases programmatically in Drupal
permalink: /blog/patterns-and-aliases-programmatically-in-drupal
published: false
date: 2021-02-17
author: davidjguru
categories: [Drupal & Coding]
sitemap: true
youtubeId: wqfeeRz_fwE
---

| ![Picture from Unsplash, by @jack_1]({{ site.baseurl }}/images/davidjguru_8_9_patterns_and_ aliases_programmatically_in_drupal_main.jpg) |
|:--:|
| *Picture from Unsplash, user [Rémi Jacquaint](https://unsplash.com/@jack_1)* |

Hello everybody! for this post I'm going to stop in a very effective utility for observing the direct relationship between the so-called "Render Arrays" of Drupal and the HTML final code itself, that in many occasions we'll generating for our Drupal site. I was working on rendering some elements and thought it might be interesting to show that transition exists between a Drupal array and the HTML that will be inserted in the response from our server response, interpreting what Drupal really does from a Render Array with lot of properties and values inside.  
<!--more-->

## Introduction 

```
$ ddev composer require drupal/pathauto 
$ ddev drush en pathauto
```

{% gist 590bf212c2a31528ea872a27f7bf3443 %} 

```
$ ddev drush en testing_pathauto
``

## :wq!

### Recommended song: Asilos Magdalena - The Mars Volta

{% include youtubePlayer.html id=page.youtubeId %}
