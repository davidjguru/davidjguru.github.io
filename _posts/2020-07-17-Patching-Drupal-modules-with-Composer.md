---
layout: post
title: Patching Drupal modules with Composer
permalink: /blog/patching-drupal-modules-with-composer
published: false
date: 2020-07-17
author: davidjguru
categories: [Drupal Tips]
sitemap: true
---
| ![Picture from Unsplash, by @tony_wanli]({{ site.baseurl }}/images/davidjguru_drupal_8_patching_drupal_modules_with_composer_main.png) |
|:--:|
| *Picture from Unsplash, user [Tony Wan, @tony_wanli](https://unsplash.com/@tony_wanli)* |

I was thinking about I could write my as my little monthly post in this sketchbook just when I saw a new comment coming -through email notifications- to my main website, [The Russian Lullaby](https://www.therussianlullaby.com/) in one of my last articles about migration debugging in Drupal. Specifically, the comment is a query about patching a Drupal module, since the article includes a section where the module has to be patched in order to work fine (at least earlier, for certain versions prior to the last one). So I thought I'd publish a little post about Drupal patches applied from Composer.  
<!--more-->
Two years ago, I wrote my first article about Composer and Drush for Drupal here in my medium profile: (Well, in spanish / castilian, my primary language). Since then I had also been thinking of writing about Composer in english too, but the self-imposed pace of at least one article per-month along with contributions and training (for myself) prevented me from doing so. Now is the time!  

## Composer for Drupal 101 
Just now I have an debate/conversation with Matt Abrams, my Digital Ocean editor, about the use of Composer for Drupal projects. Essentially, I couldn't imagine working with Drupal now without using Composer. Well, I can imagine because sometimes I work on Drupal 8 legacy projects without being composerized platforms Ouch >< !.  

[Composer](https://getcomposer.org) is a command line application built with PHP, launched in March 2012 and focused on the management of dependencies in projects based on the PHP language. It's based on previous ideas from other dependency managers already available for NodeJS (npm+ ) or Ruby (bundler+ ) and tries to bring the same concept to PHP language based environments.  

Basically, dependency handlers exist to make our life easier around two concepts:  
1. **Automation:** launching processes that work by themselves.
2. **Standardization:** having those processes operate in the same unified way, especially in the context of the third party libraries we usually use in our projects.

Ok, Composer is a dependency manager and deals with packages, libraries and resources of different types. But in addition, it is possible to interpret an initial installation as an initial unresolved dependency, so it can also be used as an initial Drupal installation tool.  
We can say that Composer consists of two key pieces: on the one hand a command line tool to manage these dependencies and interact from the terminal console and on the other hand a repository where the packages are stored called [**Packagist.org**](https://packagist.org/).  

In between, there's an element that usually connects them both: a **composer.json** file that records the needs of each project to respond as a command tool log, which relies on these annotations to request packages and dependencies from the repository. In addition to this .json file there is another file called **composer.lock**. What is the latter? It's a log file, like a notebook where Composer notes the exact version that has been installed of each library. This is Composer's way of stabilizing a project in a series of specific versions of its dependencies. Thus, any person or system that downloads the project will have the same version as the rest.  

Once installed on a system, Composer allows through the use of its commands to locate, download and install the necessary dependencies with their required versions through a connection to an external repository for PHP resources called [Packagist](https://packagist.org) that we commented before and from where it extracts all the resources it needs. And for all this it relies on the file composer.json, whose minimum registration unit is the following:

```
{
    "require": {
        "vendor/project": "1.0.*"
    }
}
```



## Installing Composer 

Something like this, and old message from a Composer installation in one of my laptops (from 2018):
![Composer initial message]({{ site.baseurl }}/images/davidjguru_drupal_composer_2018.png)  


In order to update Composer, you can run: 
```
composer self-update
```
And it will replace the composer.phar with the latest available version: 

![Composer message after update]({{ site.baseurl }}/images/davidjguru_drupal_composer_2020.png)  


## Composer for patching

https://github.com/cweagans/composer-patches


```
"extra": {
    "patches": {
        "drupal/MODULE_NAME": {
            "ANY_STRING_TO_DESCRIBE_THE_APPLYING_PATCH": "PATCH URL"
        }
    },
    "enable-patching": true
}
```

```
"patches": {
   "drupal/MODULE_NAME": {
          "Patch name 1 Text": "Patch 1 Url", 
          "Patch name 2 Text": "Patch 2 Url" 
        }
    },
```

git -C 'web/modules/contrib/config_installer' apply '-p1' '/path/to/project/patches/my_own_patch.patch'

## Read More

* Drupal 8 Composer Best Practices, from [James Sansbury, @q0rban](https://twitter.com/q0rban) in Lullabot's website: [https://www.lullabot.com/articles/drupal-8-composer-best-practices](https://www.lullabot.com/articles/drupal-8-composer-best-practices).

* Using packages.drupal.org as repository, from Drupal.org documentation: [https://www.drupal.org/docs/develop/using-composer/using-packagesdrupalorg](https://www.drupal.org/docs/develop/using-composer/using-packagesdrupalorg). 




## :wq! 