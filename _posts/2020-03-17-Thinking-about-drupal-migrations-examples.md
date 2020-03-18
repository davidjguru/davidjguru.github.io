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

Well, for this article I had proposed to model two different migration processes,
 under a point of view that could be summarized as **"primum vivere, deinde 
 philosophari"** (first you experiment, then you theorize). This is why I have 
 decided to organize it in a particular way: 

* The first thing to say is that the two processes are divided into sections 
that are common to both and instead of finishing one and starting the next one, 
both go in parallel (you choose your own adventure). 

* Then, Only at the end of this post you will find some key concepts used in 
this article. First we gonna to play with the structures, then we'll understand
them. 

So, in the next steps, we'll working around two certain experiencies:

1. **Migrating Data from a embedded format** (maybe the most simple example of
Drupal migrations). 

2. **Migration Data from a classical CSV file format** (just a little more advanced 
than the previous example).

Both of the cases are perhaps the most basic scenarios for a migration, so I 
recommend reading this article for those who want to get started on its own 
mechanics, as a practical complement to get into Drupal migrations.

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

By the way I have to say that it's important to know that migrate_run is 
optimized for Drush 9 and later. If you use Drush 8 you will have to use an 
adapted version, [like the Alpha 4, which was still prepared for Drush 8](https://www.drupal.org/project/migrate_run/releases/8.x-1.0-alpha4).

**Using Composer and Drush:**
```bash
composer require drupal/migrate_run
drush pmu migrate_tools # If you need
drush en migrate migrate_run -y
drush cr
```

**Using Drupal Console:**
```bash
composer require drupal/migrate_run
drupal mou migrate_tools # If you need
drupal moi migrate migrate_run
```
And you will see in the path /admin/modules:

![Enabling Migrate and Migrate Run modules]({{ site.baseurl }}/images/davidjguru_drupal_migrations_examples_modules.png)

#### Building the resources

Now, we're going to create a new custom module for our Migrations: 
```bash
cd project/web/modules/custom
mkdir migration_basic_module
```
Then, the basic info.yml file with content:
```bash
name: 'Migration Basic Module'
type: module
description: 'Just a basic example of basic migration process.'
package: 'Migrations Examples 2000'
core: 8.x
dependencies:
  - drupal:migrate
```

Create the new migration definition file with path: /migration_basic_module/migrations/basic_migration_one.yml And values:

In our new declarative file basic_migration_one.yml, which describes the migration as a list of parameters and values in a static YAML-type file, we will include the embedded data of two nodes for the content type "basic page" to be migrated, loading only two values: 

1. A title (a text string).  
2. A body (A text based on the ChiquitoIpsum generator*, [http://www.chiquitoipsum.com](http://www.chiquitoipsum.com)).  

*[Chiquito de La Calzada](https://en.wikipedia.org/wiki/Chiquito_de_la_Calzada) was a national figure in the Spanish state, a legendary comedian.  

**basic_migration_one.yml**

```bash
id: basic_migration_one
label: 'Custom Basic Migration 2000'
source:
  plugin: embedded_data
  data_rows:
    -
      unique_id: 1
      creative_title: 'Title for migrated node - One'
      engaging_content: 'Lorem fistrum mamaar se calle ustée tiene musho pelo.'
    -
      unique_id: 2
      creative_title: 'Title for migrated node - Two'
      engaging_content: 'Se calle ustée caballo blanco caballo negroorl.'
  ids:
    unique_id:
      type: integer
process:
  title: creative_title
  body: engaging_content
destination:
  plugin: 'entity:node'
  default_bundle: page
```
And this will be the structure of the new custom module for basic migration example:

```text
/project/web/modules/custom/  
                     \__migration_basic_module/  
                         \__migration_basic_module.info.yml  
                             \__migrations/  
                                 \__basic_migration_one.yml  
```

**Enabling all the required modules using Drush:**
```bash
drush pm:enable -y migrate migrate_run migration_basic_module
drush cr
```

**Or using Drupal Console:**
```bash
drupal moi migrate migrate_run migration_basic_module
```

#### Getting info about the available migrations  

```bash
drush migrate:status
drush ms

Output from console:
----------------- -------- ------- ---------- ------------- --------------------- 
  Migration ID      Status   Total   Imported   Unprocessed   Last Imported        
----------------- -------- ------- ---------- ------------- --------------------- 
basic_migration_one   Idle     2       0          2               
----------------- -------- ------- ---------- ------------- --------------------- 
```

#### Running migrations  

```bash
drush migrate:import basic_migration_one
drush mi basic_migration_one  

Output from console:
----------------- -------- ------- ---------- ------------- ------------------- 
  Migration ID      Status   Total   Imported   Unprocessed  Last Imported        
----------------- -------- ------- ---------- ------------- ------------------- 
basic_migration_one   Idle     2       2 (100%)   0            2020-03-17 23:19:36  
----------------- -------- ------- ---------- ------------- ------------------- 
```

And so, going to the path /admin/content you'll see the two new nodes: 

![Drupal Basic Migration Embedded Data]({{ site.baseurl }}/images/davidjguru_drupal_migrations_basic_migration_embedded.png)


#### Rollbacking migrations (undoing)

```text
drush migrate:rollback basic_migration_one
drush mr basic_migration_one  

Output from console: 

[notice] Rolled back 2 items - done with 'basic_migration_one'
```

![Drupal Basic Migration Commands]({{ site.baseurl }}/images/davidjguru_drupal_migrations_basic_migration_command.png)


### Second Case: Migrating from csv files


## 3- Approaches




## 4- Migrations







## 5- Key Concepts




--------------------------------------------------------------------------------------

## :wq!

