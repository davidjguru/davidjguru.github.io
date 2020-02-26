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

I am preparing notes for a draft that will be a book about migration processes
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

## 1- Introduction

It is not very easy to talk about migrations in general and, of course, it is
 not easy in the context of Drupal either.  To perform migrations it is
 necessary to have a good knowledge of the technology, data models (in origin
  and in destination), experience in ETL processes and a certain know-how
  about how to implement Drupal Plugins (In migrations there is an extensive
  use of Drupal-Way Plugins).


## 2- Basic Resources (Core Modules)

* **Migrate:** The Mainframe for migrations, the [migrate Module in Core](https://git.drupalcode.org/project/drupal/tree/8.7.x/core/modules/migrate), that provides the base API for migrating in Drupal.

* **Migrate Drupal:** [migrate_drupal Module in Core](https://git.drupalcode.org/project/drupal/tree/8.7.x/core/modules/migrate_drupal). Focused on
upgrades from Drupal 6 or Drupal 7. Allow reading of configuration entities in Drupal 8.

* **Migrate Drupal:** Multilingual [migrate_drupal_multilingual Module in Core](https://git.drupalcode.org/project/drupal/tree/8.7.x/core/modules/migrate_drupal_multilingual). Experimental module in core (¿?). See: [https://www.drupal.org/node/2959712](https://www.drupal.org/node/2959712).

* **Migrate Drupal UI:** [migrate_drupal_ui Module in Core](https://git.drupalcode.org/project/drupal/tree/8.7.x/core/modules/migrate_drupal_ui).
Interface for upgrading.


## 3- Other Basic Resources (Contrib Modules)


## 4- Extra Resources - (Contrib Modules for Plugins)



### Source Plugins



### Processing Plugins



### Destination Plugins



## 5- Migration Runners - (Contrib Modules Drush-Related)

* **Migrate Scheduler:** [https://www.drupal.org/project/migrate_scheduler](https://www.drupal.org/project/migrate_scheduler). This module offers
integration with the Drupal Cron API to execute migrations under predefined schedules.

* **Migrate Run:** [https://www.drupal.org/project/migrate_run](https://www.drupal.org/project/migrate_run). Drush commands to running migrations in a
lightweight mode. More lean than Migrate Tools but not offer support for
migrations groups. It also doesn't depend on the Migrate Plus module. It's
just like a little maverick.


* **Migrate Devel:** [https://www.drupal.org/project/migrate_devel](https://www.drupal.org/project/migrate_devel). Provides Drush options in order to show debug info while executing migrations. Also provides the ['debug'](https://git.drupalcode.org/project/migrate_devel/blob/8.x-1.x/src/Plugin/migrate/process/Debug.php) process plugin. [Here you can see an article about it](https://agaric.coop/blog/how-debug-drupal-migrations-part-2). By the way, the [Drush 9 compatibility is still unresolved](https://www.drupal.org/project/migrate_devel/issues/2938677), but [a patch seems to be available](https://www.drupal.org/files/issues/2018-10-08/migrate_devel-drush9-2938677-6.patch).

## 6- Authors you should know

* **[Mauricio Dinarte](https://twitter.com/dinarcon):** Mauricio is a
developer, consultant, trainer and owner of his own business [https://agaric.coop](https://agaric.coop), wrote what is probably the mandatory reading guide for all people who want to learn how to migrate on Drupal: 31
Days of Drupal Migrations, a set of 31 articles published in
[https://understanddrupal.com/migrations](https://understanddrupal.com/migrations) with the most important aspects...examples, exercises, descriptions...to understand the whole internal world of migrations inside Drupal. An essential training material. In addition, his company's website,
under the tag "migrate" also hosts many very good articles on migration:
[https://agaric.coop/tags/migrate](https://agaric.coop/tags/migrate). Some
examples:

1. Introduction to paragraphs migrations in Drupal:
* [https://agaric.coop/blog/introduction-paragraphs-migrations-drupal]
(https://agaric.coop/blog/introduction-paragraphs-migrations-drupal).

2. Using migration groups to share configuration among Drupal migrations:
* [https://agaric.coop/blog/using-migration-groups-share-configuration-among-drupal-migrations](https://agaric.coop/blog/using-migration-groups-share-configuration-among-drupal-migrations)

3. What is the difference between migration tags and migration groups in Drupal?
* [https://agaric.coop/blog/what-difference-between-migration-tags-and-migration-groups-drupal](https://agaric.coop/blog/what-difference-between-migration-tags-and-migration-groups-drupal).

His profile in Drupal.org: [https://www.drupal.org/u/dinarcon](https://www.drupal.org/u/dinarcon).

* **[Tess Flynn](https://twitter.com/socketwench):** I heard about Tess Flynn
 reading articles by Mauricio Dinarte.  That's how I met this expert
 developer, speaker and communicator of the Drupal community. On her website
  [https://deninet.com](https://deninet.com) I found content of
  a different nature, but above all, a series of very
  interesting articles about migrations under the tag
  "drupal-migration": [https://deninet.com/tag/drupal-migration](https://deninet.com/tag/drupal-migration).
  Along the way I also discovered that it has several contrib modules related to Migrations and Processing Plugins.

Some examples:

1. Migrate Process URL: Provides Process Plugin to migrate link fields.
* [https://www.drupal.org/project/migrate_process_url](https://www.drupal.org/project/migrate_process_url).

2. Migrate Process Vardump: Helping to debugging migrations.
* [https://www.drupal.org/project/migrate_process_vardump](https://www.drupal.org/project/migrate_process_vardump).

3. Many Process Plugins:
* [Migrate Process Array](https://www.drupal.org/project/migrate_process_array), [Migrate Process Skip](https://www.drupal.org/project/migrate_process_skip), [Migrate Process Trim](https://www.drupal.org/project/migrate_process_trim).

Her profile in Drupal.org: [https://www.drupal.org/u/socketwench](https://www.drupal.org/u/socketwench).

* **[Danny Sipos](https://twitter.com/drupalexp):** Author of a
well-considered Bible of Drupal ([Drupal 8 Module Development, nowadays in its second edition](https://www.amazon.com/Drupal-Module-Development-modules-version/dp/1789612365/) and writing also in his
website [https://www.webomelette.com/](https://www.webomelette.com/).
Lecturer, trainer and regular attendee at various international Drupal events,
has also written some interesting articles about Drupal migrations that you should read.
Some examples:
1. Your first Drupal 8 Migration:
* [https://www.sitepoint.com/your-first-drupal-8-migration](https://www.sitepoint.com/your-first-drupal-8-migration/).

2. Dynamic migrations using "templates" in Drupal 8:
* [https://www.webomelette.com/dynamic-migrations-using-templates-drupal-8](https://www.webomelette.com/dynamic-migrations-using-templates-drupal-8).

3. Quickly generate the headers for the CSV migrate source plugin using Drush:
* [https://www.webomelette.com/quickly-generate-headers-csv-migrate-source-plugin-using-drush](https://www.webomelette.com/quickly-generate-headers-csv-migrate-source-plugin-using-drush).

His profile in Drupal.org: [https://www.drupal.org/u/upchuk](https://www.drupal.org/u/upchuk).