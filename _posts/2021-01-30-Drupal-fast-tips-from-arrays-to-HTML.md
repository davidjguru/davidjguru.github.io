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

**I will use a single Drupal local installation** from myself. In my local environtment I have a Drupal 9 deploy for testing, using DDEV-local as deploying tool. See more about [DDEV-Local](https://ddev.readthedocs.io/en/stable/) and know how deploy Drupal installations in some steps from a single snippet in my gitlab profile: [Drupal 9 in six steps using DDEV: Quick Deploy](https://gitlab.com/-/snippets/2012512).   

**I will use a single custom module available in my gitlab profile**: 'Managing Activities', in order to get some structured arrays and testing the main idea. Use, downloading or cloning the code from here: [gitlab.com/davidjguru/custom-modules/managing_activities)](https://gitlab.com/davidjguru/drupal-custom-modules-examples/-/tree/master/managing_activities) or from the main folder here: [gitlab.com/davidjguru/custom-modules](https://gitlab.com/davidjguru/drupal-custom-modules-examples).   

**Please:** These modules aren't for Stage or Live / Prod. Modules only for dev, testing and fun.  

**I will use Xdebug in order to get internal info from some variables** and follow the processing of the original Render Array, step by step. Cause I'm using DDEV-Local in all my local deploys, for me is very easy using and enabling Xdebug in Drupal projects. It's quite fast, only go to your Drupal installation by prompt and ask:  

```txt
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

The so called "Render Arrays" or "Rendered Arrays" in Drupal are like blocks in order to build Drupal sites. Are only associative arrays with structured data based in relationships key/value, from its properties defined in the Drupal API specifications.

[**Read more about the Render Arrays properties** in Drupal, here.](https://www.drupal.org/docs/drupal-apis/render-api/render-arrays#properties) 

For this case, I'll use a render array from my custom module. I'm gonna take as base file [this one](https://gitlab.com/davidjguru/drupal-custom-modules-examples/-/blob/master/managing_activities/src/Form/ManagingActivitiesRegisterForm.php), the ManagingActivitiesRegisterForm class, that extends FormBase in order to create a Drupal form using the rules of the Form API, building and delivering a render array returned by its method [buildForm()]().  

In this class, we're building a render array just like this: [see link to code lines](https://gitlab.com/davidjguru/drupal-custom-modules-examples/-/blob/master/managing_activities/src/Form/ManagingActivitiesRegisterForm.php#L135).  Some steps are:  

I'm adding a div wrapper to the form:  

```php
   // Building the form.
    $form['#prefix'] = '<div id="register_form_wrapper">';
    $form['#suffix'] = '</div>';
```

Putting some elements:  

```php
$form['managing_activities_register_identification'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Indentification Number'),
      '#attributes' => [
        'placeholder' => t('12345678A'),
      ],
      '#description' => $this->t('Set your Identification Number or DNI for the Spanish State.'),
      '#maxlength' => 30,
      '#size' => 30,
      '#weight' => '4',
      '#required' => TRUE,
      '#prefix' => '<div id="register_form_identification">',
      '#suffix' => '</div>',
    ];

$form['managing_activities_register_date'] = [
      '#type' => 'date',
      '#title' => $this->t('Date of birth'),
      '#description' => $this->t('Set your date of birth.'),
      '#weight' => '5',
      '#required' => TRUE,
      '#prefix' => '<div id="register_form_date">',
      '#suffix' => '</div>',
    ];
```
And at last, some actions and a custom JavaScript library using the property "attached".  
**See the post:** [Drupal Fast Tips (III) - The Magic of '#attached'](https://davidjguru.github.io/blog/drupal-fast-tips-the-magic-of-attached)   

```php

$form['actions']['submit'] = [
      '#type' => 'submit',
      '#value' => $this->t('Request'),
      '#button_type' => 'primary',
      '#prefix' => '<div id="register_form_submit">',
      '#suffix' => '</div>',
    ];

// We're going to add the custom JavaScript related library.
$form['#attached']['library'][] = 'managing_activities/getting_feedback';
```

As we can see it's a very normal render array, with a classical definition for a custom form. Almost a standar in the daily work, isn't it?  

Now we're going to talking'bout the next resource: the renderer service.  


## The Renderer service 

In the Drupal world, "rendering" means something like this: take an render array and turning it to HTML code in a single piece of code, ready to sent to the client browser in a Drupal response context.  



## Testing the array 



## Get more info

* [Renderer Service in core.services.yml for Drupal 9](https://api.drupal.org/api/drupal/core%21core.services.yml/service/renderer/9.2.x)

* [Drupal's render() replaced with Renderer service in Drupal 8 || 9](https://www.drupal.org/node/2939099)

* [The Render Plain function for Drupal 8.x](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21Renderer.php/function/Renderer%3A%3ArenderPlain/8.2.x)

* [The Drupal 8 || 9 Render pipeline](https://www.drupal.org/docs/8/api/render-api/the-drupal-8-render-pipeline) 

* [The Drupal 8 || 9 Render API](https://www.drupal.org/docs/drupal-apis/render-api) 

## 6- :wq!

### Recommended song

{% include youtubePlayer.html id=page.youtubeId %}