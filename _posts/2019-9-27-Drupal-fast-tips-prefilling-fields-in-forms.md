---
layout: post  
title: Drupal Fast Tips (II) - Prefilling fields in forms  
permalink: /blog/drupal-fast-tips-prefilling-fields-in-forms  
published: true  
date: 2019-09-27  
categories: [Drupal]  
sitemap: true
---
| ![Picture from Unsplash, by @chadwiq]({{ site.baseurl }}/images/davidjguru_drupal_8_prefilling_fields_in_forms.jpeg) |
|:--:|
| *Picture from Unsplash, user Chad Walton @chadwiq* |


Today I would like to play a little with the forms in Drupal 8
, taking advantage of them and walking around interesting concepts such as
 the injection of services (and services by themselves) or dynamic queries to
  database. That's why today I'm thinking about a short and didactic
   exercise that integrates everything. If you are interested in Drupal
    8 forms, the services and the Database API, read on...
<!--more-->

## Introduction 
The first step is to decide what kind of forms we want to build. In Drupal 8, there are -basically- three types of forms that, in turn, are constructed from three specific classes: 

1- **Basic Form**: a normal form of general purpose, adaptable. Created from
 the FormBase Class in Drupal API.  
 [https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21FormBase.php/class/FormBase/8.7.x](https://api.drupal.org/api/drupal/core
 %21lib%21Drupal%21Core%21Form%21FormBase.php/class/FormBase/8.7.x)  
 
2- **Config Form**: a form of specific use to establish an object and
 configuration values.  Created from the ConfigFormBase in Drupal API.
 [https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfigFormBase.php/class/ConfigFormBase/8.7.x](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfigFormBase.php/class/ConfigFormBase/8.7.x)  
 
3- **Confirm Form**: a form to request confirmation from the user before
 executing an irreversible action. Created from the ConfigFormBase in Drupal
  API. [https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfigFormBase.php/class/ConfigFormBase/8.7.x](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfigFormBase.php/class/ConfigFormBase/8.7.x)

In this case, we will opt for a form created as Basic Form, more adaptable and elastic for general purposes. We will create a new custom module for Drupal 8, and in its /src/Form route we will include our test form. We will see the code in the next section. 

## Build our Form

## Filling fields in our Form

### Inyecting services in Drupal 8

### Dynamyc Queries with Database API

###  The final version of the class