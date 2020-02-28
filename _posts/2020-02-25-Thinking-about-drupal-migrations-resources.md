---
layout: post
title: Thinking about Drupal Migrations(I) - Resources
permalink: /blog/thinking-about-drupal-migrations-resources
published: true
date: 2020-02-25
author: davidjguru
categories: [Migrations]
sitemap: true
---
| ![Picture from Unsplash, by @inmasantiago]({{ site.baseurl
}}/images/davidjguru_drupal_8_thinking_about_drupal_migrations_main.png) |
|:--:|
| *Picture from Unsplash, user Inma Lesielle @inmasantiago* |

I am working on notes for a draft that will be a book about migration processes
made with Drupal and its Migrate API. It is expected to be released in June
2020 and the work of collecting, experimenting and articulating the content
is being quite extensive.
As there are still some months left for the launch, in order not to lose the
mental sanity and to be able to give partial sense to these tasks, I have
thought to publish here some small posts derived from the working notes.
<!--more-->
This way I will be able to give something useful to the complementary notes
and if the COVID-19 attacks me before seeing the book come out, at least I
will have shared something before.

Well, what do I want to talk about in this post? I would like to make a list
of Drupal modules related to migration processes, available as contrib
modules and that can be used to provide functionality to a migration. This
article will be only a lightweight set of basic resources (I swear).

--------------------------------------------------------------------------------------

**Table of Contents**  
<!-- TOC -->
[1- Introduction](#1--introduction)  
[2- Basic Resources - Core Modules](#2--basic-resources---core-modules)  
[3- Other Basic Resources - Contrib Modules](#3--other-basic-resources---contrib-modules)  
[4- Extra Resources - Contrib Modules for Plugins](#4--extra-resources---contrib-modules-for-plugins)  
[5- Migration Runners - Contrib Modules Drush-Related](#5--migrations-runners---contrib-modules-drush-related)  
[6- Authors you should know](#6--authors-you-should-know)  
[7- :wq!](#wq)  
<!-- /TOC -->

-----------------------------------------------------------------------------------------


## 1- Introduction

It's not very easy to talk about migrations in general and, of course, it is
 not easy in the context of Drupal either.  To perform migrations it is
 necessary to have a good knowledge of the technology, data models (in origin
  and in destination), experience in [ETL processes](https://en.wikipedia.org/wiki/Extract,_transform,_load) and a certain know-how
  about how to implement Drupal Plugins (In migrations there is an extensive
  use of Drupal-Way Plugins).

  In any case, since the topic is extensive and my time is now short, I
  thought of this article as a summary catalogue (for quick consumption) of
  tools and basic resources for working with migrations.


## 2- Basic Resources - Core Modules

* **Migrate:** The Mainframe for migrations, the [migrate](https://git.drupalcode.org/project/drupal/tree/8.7.x/core/modules/migrate), that provides the base API for migrating in Drupal.


* **Migrate Drupal:** [migrate_drupal](https://git.drupalcode.org/project/drupal/tree/8.7.x/core/modules/migrate_drupal). Focused on
upgrades from Drupal 6 or Drupal 7. Allow reading of configuration entities in Drupal 8.


* **Migrate Drupal:** Multilingual [migrate_drupal_multilingual](https://git.drupalcode.org/project/drupal/tree/8.7.x/core/modules/migrate_drupal_multilingual). Experimental module in core (¿?). See: [https://www.drupal.org/node/2959712](https://www.drupal.org/node/2959712).


* **Migrate Drupal UI:** [migrate_drupal_ui](https://git.drupalcode.org/project/drupal/tree/8.7.x/core/modules/migrate_drupal_ui).
Interface for upgrading.


## 3- Other Basic Resources - Contrib Modules

* **Migrate Plus:** [migrate_plus](https://www.drupal
.org/project/migrate_plus). Migrate Plus is an essential contrib module wich
extends the features and capabilities of the Migrate core module with a lot
of plugins and extensions.


* **Migrate Tools:** [migrate_tools](https://www.drupal.org/project/migrate_tools). Another essential resource: provides a lot of
Drush commands for running and managing Migrations.


* **Migrate Status:** [migrate_status](https://www.drupal.org/project/migrate_status).
This little contrib module allows get a feedback about a migration process.
Do you need to know if a migration is running? this module gives you a
service that you can call in order to check the migration.


* **Migrate Files:** [migrate_files](https://www.drupal.org/project/migrate_file).
It's such an interesting set of process plugins that you will want to move
files and images with it.


* **Migrate Commerce:** [commerce_migrate](https://www.drupal.org/project/commerce_migrate).
General-purpose framework that extends to the main Migrate module from Drupal
 Core, for moving data in a Drupal Commerce scenario.


## 4- Extra Resources - Contrib Modules for Plugins

In the Drupal migration processes, we'll use diverse resources in order to
processing the [ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load) migration plan.
One of these basic resources (as I mentioned in the introduction) are the
[Drupal Plugins](https://www.drupal.org/docs/8/api/plugin-api), of which you need to
have good knowledge and some practice. In a migration scenario, plugins help
us processing information from the E:Source ([Source Plugins](https://www.drupal.org/docs/8/api/migrate-api/migrate-source-plugins)), making
T:Processing([ProcessPlugins](https://www.drupal.org/docs/8/api/migrate-api/migrate-process-plugins)) in order to
save data at L:Destination ([Destination Plugins](https://www.drupal.org/docs/8/api/migrate-api/migrate-destination-plugins-examples)).  The
assembly of these three parts (usually) results in a correct migration
process.

Many modules of the core already bring their own Plugins to facilitate migration processes (as the user module). So let's review some migration plugins packaged in contributed modules.


### Source Plugins [Migrate Source Plugin](https://www.drupal.org/docs/8/api/migrate-api/migrate-source-plugins)


* **Migrate Source CSV:** [migrate_source_csv](https://www.drupal.org/project/migrate_source_csv). Contrib Module for migrating data to Drupal
 8 from a classical and simple CSV file.


* **Migrate Source SQL:** [custom_sql_migrate_source_plugin](https://www.drupal.org/project/custom_sql_migrate_source_plugin).
As a peculiarity of the Plugins used for databases, this module allows to integrate in the .yml file describing a migration, directly SQL queries that will be executed against the source database.


* **Migrate Source YAML:** [migrate_source_yaml](https://www.drupal.org/project/migrate_source_yaml). It's just a simple tool for migrating content from YAML files.


### Processing Plugins [Migrate Process Plugins](https://www.drupal.org/docs/8/api/migrate-api/migrate-process-plugins)


* **Migrate Process Geofield:** [geofield](https://www.drupal
.org/project/geofield).
The contrib Geofield module comes with a custom process plugin for migrations. See [Process Plugin in Geofield](https://www.drupal.org/docs/8/api/migrate-api/migrate-process-plugins/contrib-process-plugin-geofield_latlon).


* **Migrate Process XML:** [migrate_process_xml](https://www.drupal.org/project/migrate_process_xml). Provides process plugins for xpath and
xvalue.


* **Migrate HTML to Paragraphs:** [migrate_html_to_paragraphs](https://www.drupal.org/project/migrate_html_to_paragraphs). Helps to transform HTML from a
migration Source in a Paragraph item (managed by a Destination Plugin).


### Destination Plugins: [Migrate Destination Plugins & Examples](https://www.drupal.org/docs/8/api/migrate-api/migrate-destination-plugins-examples)
What kind of Drupal entities will be created in the migrating process?
content entities? configuration entities? Take a look.

* **Migrate Destination CSV:** [d8migrate](https://github.com/jonathanfranks/d8migrate/tree/master/web/modules/custom/migrate_destination_csv). It's a light custom module created by @jonathanfranks.


* **Migrate Destination Config:** [Class Config.php](https://api.drupal.org/api/drupal/core%21modules%21migrate%21src%21Plugin%21migrate%21destination%21Config.php/class/Config/8.5.x). Offers a plugin for config migration.


* **Migrate Destination Block:** [Class EntityBlock.php](https://api.drupal.org/api/drupal/core%21modules%21block%21src%21Plugin%21migrate%21destination%21EntityBlock.php/class/EntityBlock/8.7.x). Just like and example about the
resources that every element can offers in a migration scene, in case of
moving Block Entities (are Config Entities) see the PHP classes
included in its own module for migrating (Source, Process and Destination).

![Drupal 8 Migrate Entity Block]({{ site.baseurl }}/images/davidjguru_drupal_8_migrate_entity_block.png)


## 5- Migrations Runners - Contrib Modules Drush-Related

* **Migrate Scheduler:** [migrate_scheduler](https://www.drupal.org/project/migrate_scheduler). This module offers
integration with the Drupal Cron API to execute migrations under predefined schedules.


* **Migrate Run:** [migrate_run](https://www.drupal.org/project/migrate_run). Drush commands to running migrations in a
lightweight mode. More lean than Migrate Tools but not offer support for
migrations groups. It also doesn't depend on the Migrate Plus module. It's
just like a little maverick.


* **Migrate Devel:** [migrate_devel](https://www.drupal.org/project/migrate_devel). Provides Drush options in order to show debug info while executing migrations. Also provides the ['debug'](https://git.drupalcode.org/project/migrate_devel/blob/8.x-1.x/src/Plugin/migrate/process/Debug.php) process plugin. [Here you can see an article about it](https://agaric.coop/blog/how-debug-drupal-migrations-part-2). By the way, the [Drush 9 compatibility is still unresolved](https://www.drupal.org/project/migrate_devel/issues/2938677), but [a patch seems to be available](https://www.drupal.org/files/issues/2018-10-08/migrate_devel-drush9-2938677-6.patch).


## 6- Authors you should know

* **[Mauricio Dinarte](https://twitter.com/dinarcon):** Mauricio is a
developer, consultant, trainer and owner of his own business [https://agaric.coop](https://agaric.coop), wrote what is probably the mandatory reading guide for all people who want to learn how to migrate on Drupal: 31
Days of Drupal Migrations, a set of 31 articles published in
[https://understanddrupal.com/migrations](https://understanddrupal.com/migrations) with the most important aspects...examples, exercises,
descriptions...to understand the whole internal world of migrations inside Drupal.

An essential training material. In addition, his company's website,
under the tag "migrate" also hosts many very good articles about migration
topics: [https://agaric.coop/tags/migrate](https://agaric.coop/tags/migrate).

 **Some examples from Mauricio Dinarte:**

1. Introduction to paragraphs migrations in Drupal:
* [https://agaric.coop/blog/introduction-paragraphs-migrations-drupal](https://agaric.coop/blog/introduction-paragraphs-migrations-drupal).

2. Using migration groups to share configuration among Drupal migrations:
* [https://agaric.coop/blog/using-migration-groups-share-configuration-among-drupal-migrations](https://agaric.coop/blog/using-migration-groups-share-configuration-among-drupal-migrations)

3. What is the difference between migration tags and migration groups in Drupal?
* [https://agaric.coop/blog/what-difference-between-migration-tags-and-migration-groups-drupal](https://agaric.coop/blog/what-difference-between-migration-tags-and-migration-groups-drupal).

His profile in Drupal.org: [https://www.drupal.org/u/dinarcon](https://www.drupal.org/u/dinarcon).

------------------------------------------------------------------------------------

* **[Tess Flynn](https://twitter.com/socketwench):** I heard about Tess Flynn
 reading articles by Mauricio Dinarte.  That's how I met this expert
 developer, speaker and communicator of the Drupal community. On her website
  [https://deninet.com](https://deninet.com) I found content of
  a different nature, but above all, a series of very
  interesting articles about migrations under the tag
  "drupal-migration": [https://deninet.com/tag/drupal-migration](https://deninet.com/tag/drupal-migration).

  Along the way I also discovered that it has several contrib modules related to Migrations and Processing Plugins.

**Some examples from Tess Flynn:**

1. Migrate Process URL: Provides Process Plugin to migrate link fields.
* [https://www.drupal.org/project/migrate_process_url](https://www.drupal.org/project/migrate_process_url).

2. Migrate Process Vardump: Helping to debugging migrations.
* [https://www.drupal.org/project/migrate_process_vardump](https://www.drupal.org/project/migrate_process_vardump).

3. Many Process Plugins:
* [Migrate Process Array](https://www.drupal.org/project/migrate_process_array), [Migrate Process Skip](https://www.drupal.org/project/migrate_process_skip), [Migrate Process Trim](https://www.drupal.org/project/migrate_process_trim).

Her profile in Drupal.org: [https://www.drupal.org/u/socketwench](https://www.drupal.org/u/socketwench).

------------------------------------------------------------------------------------

* **[Danny Sipos](https://twitter.com/drupalexp):** Author of a
well-considered Bible of Drupal -[Drupal 8 Module Development, nowadays in its second edition](https://www.amazon.com/Drupal-Module-Development-modules-version/dp/1789612365/)- and writing also in his website [https://www.webomelette.com/](https://www.webomelette.com/).
Lecturer, trainer and regular attendee at various international Drupal events,
has also written some interesting articles about Drupal migrations that you
should read.

**Some examples from Danny Sipos:**

1. Your first Drupal 8 Migration:
* [https://www.sitepoint.com/your-first-drupal-8-migration](https://www.sitepoint.com/your-first-drupal-8-migration/).

2. Dynamic migrations using "templates" in Drupal 8:
* [https://www.webomelette.com/dynamic-migrations-using-templates-drupal-8](https://www.webomelette.com/dynamic-migrations-using-templates-drupal-8).

3. Quickly generate the headers for the CSV migrate source plugin using Drush:
* [https://www.webomelette.com/quickly-generate-headers-csv-migrate-source-plugin-using-drush](https://www.webomelette.com/quickly-generate-headers-csv-migrate-source-plugin-using-drush).

His profile in Drupal.org: [https://www.drupal.org/u/upchuk](https://www.drupal.org/u/upchuk).

--------------------------------------------------------------------------------------

## wq!