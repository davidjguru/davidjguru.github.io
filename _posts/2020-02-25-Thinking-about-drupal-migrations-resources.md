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