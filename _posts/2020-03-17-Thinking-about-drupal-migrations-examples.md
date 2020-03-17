---
layout: post
title: Thinking about Drupal Migrations(II) - Examples
permalink: /blog/thinking-about-drupal-migrations-examples
published: true
date: 2020-03-17
author: davidjguru
categories: [Migrations]
sitemap: true
---
| ![Picture from Unsplash, by @emileseguin]({{ site.baseurl }}/images/davidjguru_drupal_thinking_about_drupal_migrations_examples_main.jpg) |
|:--:|
| *Picture from Unsplash, user Émile Séguin @emileseguin* |

As I said in the previous post, during these months I will be playing with 
migrations, preparing some cases for a future (I hope) book. Well, during these 
days of confinement, I intend to continue with small articles around here to 
show experiences related to migrations.
<!--more-->
In the former post, I was writing about migrations in Drupal from a point of 
view based in the look for a tool-box, just a set of basic resources in order to
 focusing a migration. 
 
 There's a lot of information to process about it and some more concepts, 
 technics and tactics to resolving a migration, you can be sure. So this month 
 I want to write something that allows me play with migrations, maybe more 
 practical than theorical.

--------------------------------------------------------------------------------------

**Table of Contents**    

<!-- TOC -->
[1- Introduction](#1--introduction)  
[2- Arrangements](#2--arrangements)  
[3- Approach](#3--approach)  
[4- Migrations](#4--migrations)  
[5- Key Concepts](#5--key-concepts)  
[7- :wq!](#wq)  
<!-- /TOC -->  

-----------------------------------------------------------------------------------------

**This article is part of a series of posts about Drupal Migrations**  
<!-- TOC -->
[1- Thinking about Drupal Migrations (I): Resources](https://davidjguru.github.io/blog/thinking-about-drupal-migrations-resources)  
[2- Thinking about Drupal Migrations (II): Examples](https://davidjguru.github.io/blog/thinking-about-drupal-migrations-examples)  
<!-- /TOC -->

------------------------------------------------------------------------------------------------

## 1- Introduction



## 2- Arrangements

### First case: Migrating embedded data

For our first case we will need, on the one hand, to enable the Migrate module of 
the Drupal core, and on the other hand, to download and install a contributed 
module to be able to manage migrations.  

From the different options we have, we are going to choose [migrate_run](https://www.drupal.org/project/migrate_run ),
 which we have already mentioned in the previous post and could be interpreted 
 as a light version of migrate_tools (although it's actually a fork of the 
 project): both of wich provide drush commands to run migrations, so if you have
  migrate_tools installed you must uninstall it in order to avoid collide with 
  migrate_run. 

As a curious note, the first lesson here is that for running Drupal migrations, 
neither migrate_plus nor migrate_tools are "hard" dependencies, that is, we can
 implement migrations without having these modules enabled in our Drupal 
 installation. 

By the way you have to say that it's important to know that migrate_run is 
optimized for Drush 9 and later. If you use Drush 8 you will have to use an 
adapted version, [like the Alpha 4, which was still prepared for Drush 8](https://www.drupal.org/project/migrate_run/releases/8.x-1.0-alpha4 )

Using Composer and Drush:
```bash
composer require drupal/migrate_run
drush pmu migrate_tools # If you need
drush en migrate migrate_run -y
drush cr
```

Using Drupal Console:
```bash
drupal mou migrate_tools # If you need
drupal moi migrate migrate_run
```
And you will see in the path /admin/modules:

![Enabling Migrate and Migrate Run modules]({{ site.baseurl }}/images/davidjguru_drupal_migrations_examples_modules.png)


### Second Case: Migrating from a database


## 3- Approaches




## 4- Migrations







## 5- Key Concepts




--------------------------------------------------------------------------------------

## :wq!

