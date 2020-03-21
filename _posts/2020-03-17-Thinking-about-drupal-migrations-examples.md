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
[6- Resources](#6--resources)  
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

[The Drupal Migration API](https://www.drupal.org/docs/8/api/migrate-api) can be one of the most interesting, but also one of the most complex, since its activities are often related to classes and methods of other Drupal APIs (so it's especially particular when debugging). In any case, and as the amount of concepts can be overwhelming, I think we could practice migration mechanics through a couple of exercises. 

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

2. **Migration Data from a classical CSV file format** (just a little more complex
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
And you will see in the path ```/admin/modules```:

![Enabling Migrate and Migrate Run modules]({{ site.baseurl }}/images/davidjguru_drupal_migrations_examples_modules.png)



#### Building the resources

Now, we're going to create a new custom module for our first Migration: 
```bash
cd project/web/modules/custom
mkdir migration_basic_module
```
Then, the ```migration_basic_module.info.yml``` file with content:

```bash
name: 'Migration Basic Module'
type: module
description: 'Just a basic example of basic migration process.'
package: 'Migrations Examples 2000'
core: 8.x
dependencies:
  - drupal:migrate
```

Create the new migration definition file with path: ```/migration_basic_module/migrations/basic_migration_one.yml```.

In our new declarative file ```basic_migration_one.yml```, which describes the migration as a list of parameters and values in a static YAML-type file, we will include the embedded data of two nodes for the content type "basic page" to be migrated, loading only two values: 

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
      page_title: 'Title for migrated node - One'
      page_content: 'Lorem fistrum mamaar se calle ustée tiene musho pelo.'
    -
      unique_id: 2
      page_title: 'Title for migrated node - Two'
      page_content: 'Se calle ustée caballo blanco caballo negroorl.'
  ids:
    unique_id:
      type: integer
process:
  title: article_title
  body: article_content
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

### Second Case: Migrating from csv files

For this second case we are going to deactivate migrate_run (if applicable) and activate the superset of modules: migrate, migrate_plus and migrate_tools.  Besides, for the treatment of CSV files we are going to use a Source Plugin stored in a contrib module called Migrate Source CSV [migrate_source_csv](https://www.drupal.org/project/migrate_source_csv). This contrib module in its version 3.x is using league/csv for processing CSV files. Ok, let's go. 
So using Composer + Drush: 

```bash
composer require migrate_plus migrate_tools migrate_source_csv
drush pmu migrate_run # If you need 
drush en migrate migrate_plus migrate_tools migrate_source_csv -y
drush cr
```
So, now in the path ```/admin/modules/```:

![Enabling Migrate and Migrate Plus Migrate Tools]({{ site.baseurl }}/images/davidjguru_drupal_migrations_csv_source_modules.png)

#### Building the resources

We're going to create another new custom module for our second Migration: 

```bash
cd project/web/modules/custom
mkdir migration_csv_module
```
With a new migration_csv_module.info.yml file: 

```yaml
name: 'Migration CSV Module'
type: module
description: 'Just a basic example of basic migration process with a CSV source.'
package: 'Migrations Examples 2000'
core: 8.x
dependencies:
  - drupal:migrate
  - drupal:migrate_tools
  - drupal:migrate_plus
```
In this example we're going to require a declarative file of the migration too (as in the previous case) but with the exception that we're going to locate it in a different place. This will be placed in the  ```/migration_csv_module/config/install/``` path.

The structure will look like this just now:

```text
/project/web/modules/custom/  
                     \__migration_csv_module/  
                         \__migration_csv_module.info.yml
                          \__csv/
                               \_migration_csv_articles.csv
                           \__config/
                                \__install/
                                     \__migrate_plus.migration.article_csv_import.yml
```

So we need a csv with original data to migrate. It's easy to solve this using web tools like [Mockaroo, a pretty good random data generator](https://www.mockaroo.com). I've created a CSV file with some fields like: 
id, title, body, tags, image. [Download it from here](https://gist.github.com/davidjguru/07c1f469a48de165b8fc53adec0398d6).
This file will be our datasource for the Migration process. Ok, by now create the directories for the module and put the new custom CSV in the ```/csv``` path:
 
 ![CSV Migrate module structure]({{ site.baseurl }}/images/davidjguru_drupal_csv_migrate_module.png)

And now, our ```migrate_plus.migration.article_csv_import.yml``` file (In later sections we will explain its construction and sections): 


```yaml
uuid: 1bcec3e7-0a49-4473-87a2-6dca09b91aba
langcode: en
status: true
dependencies: {  }
id: article_csv_import
label: 'Migrating articles'
source:
  plugin: csv
  path: modules/custom/migration_csv_module/csv/migration_csv_articles.csv
  delimiter: ','
  enclosure: '"'
  header_offset: 0
  ids:
    - id
  fields:
    -
      name: id
      label: 'Unique Id'
    -
      name: title
      label: Title
    -
      name: body
      label: 'Post Body'
    -
      name: tags
      label: 'Taxonomy Tag'
    -
      name: image
      label: 'Image Field'
process:
  title: title
  body: body
  tags: field_tags
  image: field_image
  type:
    plugin: default_value
    default_value: article
destination:
  plugin: 'entity:node'
```

Okay, we now have all the resources we need to create our new migration. Now let's see how we approach the process. 

## 3- Approaches

We're going to describe the different approaches that we will apply to our example cases, in order to understand them better. 

### First case: Migrating embedded data

In this first case, we considered making the lightest possible case of migration in Drupal: Only two nodes with two basic fields each under an embedded format: the lightest possible.  

Also, in this example we are going to use for the three ETL phases of the migration (Extract, Transformation and Loading) processing plugins already provided by Drupal (we will not develop any custom plugin). If you don't know anything about the concept of Migration Plugins, please stop by for a moment and [back here to read a little introduction to the topic](https://davidjguru.github.io/blog/thinking-about-drupal-migrations-resources#4--extra-resources---contrib-modules-for-plugins).     

To make things lighter, we will keep the "lite" version of Migration Tools, Migrate Run. Besides, we will only use the basic commands without any other options or complementary parameters, only with the basic argument of the migration file identifier. 


### Second Case: Migrating from csv files

For this execution, I would like to play with something pretty interesting...due to we'll running this second migration example as configuration, I was thinking that will be funny do the inverse road...Yes, I propose not to install (activate, drush enable) the new custom module for CSV and leave it...only as storage for the CSV file.  

Let's move and run the migration from somewhere else. Surprise.  Visit the path ```/admin/config/development/configuration/single/import``` into your Drupal installation and we'll see there!. 

## 4- Migrations


### First case: Migrating embedded data

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

Well, now in the path ```/admin/config/development/configuration/single/export``` we have to import our new custom migration definition file, Ok?

#### Loading the migration config data

Just go to Import -> Single Item, select the configuration type as "Migration" and paste the content of the original migration file: 

![Drupal Migration load File by Config]({{ site.baseurl }}/images/davidjguru_drupal_migration_load_file_by_config.png)

Click The "Import" button and the new Config object will be created in the Config System. 

**And now?**

#### Running the migration 

With the Migration file under the Config management, you can run the process with the same tools as in the former case. Now, we have available a new migration that we can run from console: ```drush migrate:status```

![Drush Migrate Status]({{ site.baseurl }}/images/davidjguru_drupal_migration_ask_for_migrations.png)

Now you can execute the migration with: ```drush migrate-import article_csv_import```
And all the new nodes will be created. The limit? well, tags and image not will be migrated, cause tag is an entity reference and image is not a link, is a file, and both types must use some differents Plugins...but we'll talk about this in future posts.


#### Drush cex / Drush cim 

With the migration under the config system, now you can edit, import and export the migration using the basic resources from Drush. For example, testing ```drush cex```:

![Drush Cex]({{ site.baseurl }}/images/davidjguru_drupal_migration_drush_cex.png)


As you can see, the Config System has directly put the new migration file under the management of Migrate Plus and It has performed some actions, such as: renamed the file by placing migrate_plus.migration as a prefix in the file name or added a new file for group (only a way to group migration processes). 

Remember the name of the file? It's just the same that we were using in the ```/config/install``` directory, the so-called ```migrate_plus.migration.article_csv_import.yml```.
We've done exactly the same process, but from a different direction. Are you impressed? No? Do you find it interesting?

Remember also that with this config file, you can use ```drush cim``` and load the migration in any other Drupal (with access to the CSV file as datasource, indeed).

Thus we have migrated some 102 new nodes using two different approaches and different methodologies. Not bad. 


## 5- Key Concepts

### Migration Plugins

Ok, It's very important so we have to repeat one more time the same song...You must to know the Plugin Format and the diverse world of the existing Migration Plugins. 

Every Plugin points to a specific data type, a specific format or a different source. You should know the main ones very well and also investigate those you may need, since in migrations they are used extensively. Because of this, for example, we have not been able to migrate taxonomy terms or images in the second case from the CSV file as datasource. 

Let's see the Plugins involved in these two migrations, watching its descriptive files: 

#### Basic Embedded Migration 

```text
source:
  plugin: embedded_data
  data_rows:
         ...
process:
  title: creative_title
  body: engaging_content
destination:
  plugin: 'entity:node'
  default_bundle: page
```

We're using for extract data from the source the Embedded Data Plugin, a PHP class available in ```/web/core/modules/migrate/src/Plugin/migrate/source/EmbeddedDataSource.php``` where in its annotations block you can see some configuration keys that you can use in your migrate file: 

```text
 *
 * Available configuration keys
 * - data_rows: The source data array.
 * - ids: The unique ID field of the data.
 *
```

And ```data_rows``` and ```ids``` are the keys that we're using in our migration description file. [Read more about the EmbeddedDataSource class in Drupal.org API](https://api.drupal.org/api/drupal/core%21modules%21migrate%21src%21Plugin%21migrate%21source%21EmbeddedDataSource.php/class/EmbeddedDataSource/8.8.x).   


Now, watching the process block and looking for...**where's the Processing Plugin?** Well I think this might be interesting...usually, all the field mappings in a processing block requires a process plugin for each. Then, with some of "sintactic sugar", the Migrate API offers a way to reduce and simplify this: if no specific treatment is required for each field, then a single Plugin can take care of all the processing.  This "default" Plugin may also be implicit, so that in the absence of a declaration, the Drupal Migrate API will always apply the same Processing Plugin by default.   

This "implicit" and by-default Plugin is the Get class and is provided as the basic solution in processing fields. You can find the Get class in the path ```/web/core/modules/migrate/src/Plugin/migrate/process/Get.php```. [Read more info about the Get.php class in Drupal.org API](https://api.drupal.org/api/drupal/core%21modules%21migrate%21src%21Plugin%21migrate%21process%21Get.php/class/Get). So actually, what we are saying in a complementary way is that is the same thing write: 

```text
process:
  title: page_title
```
as this other:

```text
process:
  title:
    plugin: get
    source: page_title
```
And so life is a little simpler, isn't it? **Remember: in the absence of a processing plugin declaration for a field, Drupal will apply the "Get" plugin by default**. 
 
Ok and finally, for destination we're using the Entity General Plugin with param "node", in order to create diverse elements with node type and for bundles "page". This calls to the  Destinatio Plugin Entity.php, abstract class in path: ```web/core/modules/migrate/src/Plugin/migrate/destination/Entity.php``` and get its own derivative Plugin. [Read more about derivative Plugins in Drupal](https://www.drupal.org/docs/8/api/plugin-api/plugin-derivatives) and [read about the Entity.php destination Plugin](https://api.drupal.org/api/drupal/core%21modules%21migrate%21src%21Plugin%21migrate%21destination%21Entity.php/8.8.x) or [the derivative migration class](https://api.drupal.org/api/drupal/core%21modules%21migrate%21src%21Plugin%21Derivative%21MigrateEntity.php/class/MigrateEntity/8.8.x).  

#### CSV datasource Migration

```text
source:
  plugin: csv
 ...
 process:
 ...
   type:
     plugin: default_value
     default_value: article
 destination:
   plugin: 'entity:node'
```



### Migration as code or as configuration

As you could see, we have treated each migration process differently. The first process (Embedded Data) has been treated as part of the "code", without any further particularities.  
 But the second process has been treated as a configuration element of the system itself, making it part of the config/install path, which will create a new configuration object from the installation. 
 
 In both cases you write the migration definition in a YAML format and then you put the migration file in a place or another. But there are more differences...Let's make a little summary of these keys: 
 
 * Migration "as code" is provided out of the box, but the module "Migrate Plus" 
 allows you treating the file as a configuration entity.
 
 * Depending on which approach you use, the location of the files and the 
 workflow will differ: 
 
   * As code, in order to make changes to the migration definition you'll need 
 access to the file system and manage the migration file as a code file, 
 something developers-oriented.  
 
   * As configuration, you'll can do changes to the migration definition file 
  using the config sync interface in Drupal, path: ```/admin/config/development/configuration```, in addition to being able to use configuration export/import 
  tools: ```drush cex```, ```drush cim```, cause now you sync the migration (the migration file will be saved in database). This means that you can write, modify, and execute migrations using the user interface. Big surprise.   
  
   * As a configuration entity, now your migration file will be create a new 
  configuration registry in your Drupal Config System, and keep it alive also 
  when your migrate module will be disabled. To avoid this and delete the config, 
  put your own custom module as a new dependency of the migration in your migration  description file.yml, so the migration will be deleted from Drupal's Active Config just in this moment:

```yaml 
  dependencies:
    enforced:
      module:
        - my_own_migration_custom_module
```
    
   * Another change is that now, in a config-way, your migration file needs a UUID,
  just a global identifier for the Drupal Config Management System. Add at first
  line an unique and custom UUID for your file, to facilitate the processing of the configuration. Remember: UUID is a string of 32 hexadecimal digits in blocks of 5 groups using the pattern: 8-4-4-4-12. Make your own!   
  ```uuid: cacafuti-1a23-2b45-3c67-4d567890a1b2```.
 



--------------------------------------------------------------------------------------

## 6- Resources

Download, play and test the different resources using along this post. I uploaded to Github ready to use. 

1. Basic Migration File, [basic_migration_one.yml, available in Github as Gist](https://gist.github.com/davidjguru/8eb16d04535dbe1523bfea0f358acf0f#file-basic_migration_one-yml).  

1. CSV Migration File, [article_csv import.yml, available in Github as Gist](https://gist.github.com/davidjguru/90705e44ff984f6268374ea37e0db621).   

1. CSV Source File with random data, [Gist in Github](https://gist.github.com/davidjguru/07c1f469a48de165b8fc53adec0398d6).  

1. Codebase of the two migration modules (basic and csv), [Available in Github](https://github.com/davidjguru/drupal_migration_examples).  This will be a central repository for all the modules of this series of posts about Migrations, so get the direct link to these two examples: 
   * [Basic Migration one with embedded data](https://github.com/davidjguru/drupal_migration_examples/tree/master/migration_basic_module).
   * [Basic Migration two with CSV file as datasource](https://github.com/davidjguru/drupal_migration_examples/tree/master/migration_csv_module).


## :wq!

