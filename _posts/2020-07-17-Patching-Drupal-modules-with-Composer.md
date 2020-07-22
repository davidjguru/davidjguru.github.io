---
layout: post
title: Patching Drupal modules with Composer
permalink: /blog/patching-drupal-modules-with-composer
published: true
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
  
  Two years ago, I wrote my first article about Composer and Drush for Drupal here in my medium profile: [Composer and Drush in a Drupal context](https://medium.com/drupal-y-yo/composer-y-drush-en-el-contexto-de-drupal-9883d2cfb007) (Well, in spanish / castilian, my primary language). Since then I had also been thinking of writing about Composer in english too, but the self-imposed pace of at least one article per-month along with contributions and training (for myself) prevented me from doing so. **Now is the time!**  
  
  ---------------------------------------------------------------------------------
  
  **Table of Contents**  
  <!-- TOC -->  
  [1- Composer for Drupal 101](#1--composer-for-drupal-101)  
  [2- Installing Composer](#2--installing-composer)  
  [3- Ten basics commands for Composer ](#3--ten-basics-commands-for-composer)  
  [4- Composer for patching](#4--composer-for-patching)  
  [5- Common Pitfalls](#5--common-pitfalls)  
  [6- Read More](#6--read-more)  
  [7- Notes](#7--notes)  
  [8- :wq!](#8--wq)    
  <!-- /TOC -->
  
  -------------------------------------------------------------------------------

## 1- Composer for Drupal 101 
Just now I have an debate/conversation with Matt Abrams, my Digital Ocean editor, about the use of Composer for Drupal projects. Essentially, I couldn't imagine working with Drupal now without using Composer. Well, I can imagine because sometimes I work on Drupal 8 legacy projects without being composerized platforms -and it's something that hurts too much- **Ouch >< !**.  

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

Composer is in [its branch / version 1.10 in Github](https://github.com/composer/composer/tree/1.10) but there is a 2.x version of Composer in progress and performance tests seem to give it [an incredible improvement in RAM consumption](https://github.com/composer/composer/pull/8850#issuecomment-660306196). And these are very very good news -I'm pretty sure-. Mike Anello [@ultimike](https://twitter.com/ultimike) has also published an article reviewing the performance of Composer 2 on his drupaleasy blog: [Composer 2.0-alpha2 performance comparison (speed and memory)](https://www.drupaleasy.com/blogs/ultimike/2020/07/composer-20-alpha2-performance-comparison-speed-and-memory). 


## 2- Installing Composer 

In orde to use Composer & Drupal, you have two main options: You can download an install it in your Operating System or You can use it from a containerized solution with Composer installed: solutions like DDEV have already Composer in its web container and ready-to-work[[1](#note-1)]. 

But if you want to run Composer in your system, you can install the tool. In this step we're going to install some resources like curl (to download and get info from remote scripts) and git (for the section where we'll applying patching to Drupal using Composer).

Follow these steps if you are in Ubuntu / Debian (my main scenarios) or adapt the instructions to your system[[2](#note-2)]: 

**1- Update, install dependencies and get the installer**
```
sudo apt update
sudo apt install php-cli unzip curl git 
cd ~
curl -sS https://getcomposer.org/installer -o composer-setup.php
```

**2- Review the security of the installer using the hash value**
```
HASH=`curl -sS https://composer.github.io/installer.sig`
php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

```
**3- Check the output for the installer**

```
-Output-
Installer verified
```

Check the former output by prompt: if the output says **Installer corrupt**, then you’ll need to download the installation script again and check that you’re using the correct hash. Then repeat the verification process.

**4- Install Composer globally**

To install composer globally you have to use the following command which will download and install Composer as a system-wide command named composer in /usr/local/bin:

```
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```
And get an output like this: 
```
- Output-
All settings correct for using Composer
Downloading...

Composer (version 1.10.5) successfully installed to: /usr/local/bin/composer
Use it: php /usr/local/bin/composer

To test your installation, run:
     composer
```

**5. Finally, run Composer**

Ok, now just launch ```composer``` and get the initial screen of Composer by prompt. Something like this, and old message from a Composer installation in one of my laptops (from 2018):  
![Composer initial message]({{ site.baseurl }}/images/davidjguru_drupal_composer_2018.png)  


In order to update Composer, you can run: 
```
composer self-update
```
And it will replace the composer.phar with the latest available version: 

![Composer message after update]({{ site.baseurl }}/images/davidjguru_drupal_composer_2020.png)  



## 3- Ten basics commands for Composer 

In this section I have tried to gather the ten most common commands in the day-to-day working with Composer & Drupal.  

### 1. composer create-project

The first command will create a new project in your current location, getting the requested template from Packagist.org (downloading a composer.json file model) and then executing composer install in order to run all the dependencies annotated in the former composer.json file.  

**Remember:** You always request a resource from a vendor/provider using the form: **vendor/resource:version**, although the version can be implicit or explicit (annotated or not). Let's see.

So when you run something like this: 
```
drupal@drupal-workshop:~/workspace$ composer create-project drupal/recommended-project drusal
```
Composer go to Packagist, using this web interface like a proxy, connecting to: [packagist.org/packages/drupal/recommended-project](https://packagist.org/packages/drupal/recommended-project), now in 9.0.2 version (this template will install Drupal 9.0.2 in your folder). Then download the template originally in its Github repository: [github.com/drupal/recommended-project](https://github.com/drupal/recommended-project/tree/9.0.x) and it will run the composer.json included there: [github.com/drupal/recommended-project/composer.json](https://github.com/drupal/recommended-project/blob/9.0.x/composer.json).  

```
- Output-
  - Installing drupal/core (9.0.2): Downloading (100%)         
  - Installing drupal/core-recommended (9.0.2)
```
Now you have a Drupal codebase in your local system (that you need to install).  

Maybe the ```create-project``` Composer command be the most complex instructions due to the big list of options that it have. You can take a look here: [getcomposer.org/doc/create-project](https://getcomposer.org/doc/03-cli.md#create-project).  

### 2. composer require

The second most basic command is the instruction for downloading resources and dependencies. 

Or you can simply ask for the last available version of a resource. Now I'm going to install Drush (and all its related dependencies):  
```
composer require drush/drush
```

![Composer Require Drush]({{ site.baseurl }}/images/davidjguru_drupal_composer_require_drush.png)  

See the options and parameters using composer require: 
[getcomposer.org/doc/require](https://getcomposer.org/doc/03-cli.md#require).  


### 3. composer show

Return a list of available packages for your installation. If you give a name, search the package or will show the package data: 

```
composer show
```
Now filtering by vendor: 
```
composer show drupal/*
```
![Composer Require Drush]({{ site.baseurl }}/images/davidjguru_drupal_composer_show_drupal.png)  


And now getting info about a certain resource:
```
composer show drupal/core
```
![Composer show drupal core]({{ site.baseurl }}/images/davidjguru_drupal_composer_show_drupal_core.png)  


### 4. composer outdated

This command shows a list of packages with availables updates, writing the current value and the latest versions. Is just like an alias for ```composer show -lo```:

![Composer outdated]({{ site.baseurl }}/images/davidjguru_drupal_composer_outdated.png)  


And is using a color code for the output, the color coding is as such:
```
green (=): Dependency is in the latest version.
yellow (~): Dependency has a new version available, upgrade when you can but it may involve work.
red (!): Dependency has a new version that is semver-compatible and you should upgrade it.
```
- Yes, for some reason the yellow color is marking a more dangerous status than red -  
¯\\_(ツ)_/¯  



### 5. composer diagnose 

Check the current status of your Composer installation.

### 6. composer update

Get more updated versions of resources, and is aliased as ```composer upgrade```.


### 7. composer self-update

Update Composer to its latest version.

![Composer Self Update]({{ site.baseurl }}/images/davidjguru_drupal_composer_self_update.png)  



(It's me updating Composer in the web container from a DDEV installation).

### 8. composer search

Search for a name in the remote repository. 

### 9. composer clearcache

This command will delete all the cache directories of your Composer installation. 


### 10. compose depends

Show the dependencies of a certain resources.

### 11. compose validate

Ok, are you sure about you composer.json file? Check if it's valid and well-formed executing this command, returning fails and format errors. Don't check line-to-line with your eyes, use validate.  

![Composer validate]({{ site.baseurl }}/images/davidjguru_drupal_composer_validate.png
)  


## 4- Composer for patching

In addition to using Composer to install / update dependencies, you can use Composer to apply repairs in a "patch" format, applied as resources and managed by Composer through its Plugins.  

In Composer, plugins are functional extensions that can be added to increase your options. You add new extensions to Composer as you add new dependencies. For example, in order to use patching, you can add the next Composer Plugin: [github.com/cweagans/composer-patches](https://github.com/cweagans/composer-patches).

**First**, install the patching Plugin requesting it like a new dependency:  

```
composer require cweagans/composer-patches
```

**Second**, add a new section in the ```"extra"``` section in your composer.json file, writing the new piece using the form:  

```
"extra": {
    "patches": {
        "drupal/MODULE_NAME": {
            "ANY_STRING_TO_DESCRIBE_THE_APPLYING_PATCH": "PATCH URL"
        }
    },
}
```
Where "ANY_STRING_TO_DESCRIBE_THE_APPLYING_PATCH" will be the output from prompt when Composer execute the patching.  

**Third** You can add many patches as you need, apply:  

```
"patches": {
   "drupal/MODULE_NAME_ONE": {
          "Patch name 1 Text": "Patch 1 Url", 
          "Patch name 2 Text": "Patch 2 Url" 
        },
   "drupal/MODULE_NAME_TWO": {
          "Patch name 3 Text": "Patch 3 Url"
        }
    },
```

**Fourth** You can put your patches in a separate file, just doing:  

```
 "extra": {
    "patches-file": "local/path/to/my/composer.patches.json"
  }
```
And then creating my own external patches file ```composer.patches.json```:

```
{
  "patches": {
    "drupal/MODULE_NAME_THREE": {
      "Patch name 4 Text": "Patch 4 Url"
    }
  }
}

```

**How it works:** Well, wen you add a new patching piece, the Composer Plugin take actions and goes to processing the patch and apply it over the named module or resource. 

You can see the actions from the repository of the plugin, starting in line 373, just here [github.com/cweagans/composer-patches/#373](https://github.com/cweagans/composer-patches/blob/master/src/Plugin/Patches.php#L373). 

Here you can see the function:  
**protected function getAndApplyPatch(RemoteFilesystem $downloader, $install_path, $patch_url)**

Where at one point: 

```
$checked = $this->executeCommand(
                'git -C %s apply --check -v %s %s',
                $install_path,
                $patch_level,
                $filename
            );
```

This is, in practise, like launch a git instruction like this: 
```
git -C 'web/modules/contrib/config_installer' apply '-p1' '/path/to/project/patches/my_own_patch.patch'
```
We're launching:
 1. A git instruction (git apply)   
 2. In not the current folder (-C 'path/folder' as context)  
 3. Removing -pN elements from the beginning (-p1, deletes '/path' in route)
 
Or if git apply fails, then will launch the ```patch``` command, see [line 432](https://github.com/cweagans/composer-patches/blob/a18d1ca38ae09d16aa21846f60649d99d6775639/src/Plugin/Patches.php#L432): 

```
// In some rare cases, git will fail to apply a patch, fallback to using
// the 'patch' command.
if (!$patched) {
  foreach ($patch_levels as $patch_level) {
  // --no-backup-if-mismatch here is a hack that fixes some
  // differences between how patch works on windows and unix.
    if ($patched = $this->executeCommand(
      "patch %s --no-backup-if-mismatch -d %s < %s",
        $patch_level,
        $install_path,
        $filename
        )
[...]
```

And all will be executed the next time you run ```composer install```.


## 5- Common pitfalls

In this section I would like to gather some mistakes I have made / observed working with Composer and Drupal when applying patches. 

### 1. Install some basic resources
Sometimes in contexts where we have not participated in their installation (external systems, already built Docker containers, etcetera), application errors occur just without feedback.  
Simply compose runs but the resource is not patched, it stays the same.  

Please, review some basic resources. For example, when using composer patching, we're doing a sequence

1. Getting the remote patching file (or local from another folder). Here you're using [the copy() method from the RemoteFileSystem Class of the Composer API](https://getcomposer.org/apidoc/master/Composer/Util/RemoteFilesystem.html#method_copy).
2. Applying the patch using git apply (or trying).

So, ensure you have installed some related tools like: 

```
sudo apt install -y build-essential apt-transport-https ca-certificates jq curl software-properties-common file git

```
To download external resources from your machine, supporting https, with curl (if need) some extra resources like git. Is git installed and configured on your sistem?...


### 2. Grant permissions
Sometimes git can't apply the patch because of a specific permission setting on the resource's destination folder where it can't write. 

### 3. Error handling 
If a patch cannot be applied (due to diverse reasons) the patch will be skipped and the process continues. If you want stop the processing just after the error, then you can add one specific instruction ```composer-exit-on-patch-failure": true``` inside the ```extra``` section of the composer.json file: 

```
    "extra": {
        "drupal-scaffold": {
            "locations": {
                "web-root": "web/"
            }
        },
        "installer-paths": {
            "web/core": ["type:drupal-core"],
            "web/libraries/{$name}": ["type:drupal-library"],
            "web/modules/contrib/{$name}": ["type:drupal-module"],
            "web/profiles/contrib/{$name}": ["type:drupal-profile"],
            "web/themes/contrib/{$name}": ["type:drupal-theme"],
            "drush/Commands/contrib/{$name}": ["type:drupal-drush"],
            "web/modules/custom/{$name}": ["type:drupal-custom-module"],
            "web/themes/custom/{$name}": ["type:drupal-custom-theme"]
        },
        "composer-exit-on-patch-failure": true,
        "patches": {
          "drupal/migrate_devel": {
          "Updates Drush for Migrate Devel Commands": "https://www.drupal.org/files/issues/2018-10-08/migrate_devel-drush9-2938677-6.patch"
        }
      }
    }
```



### 4. Getting patches from HTTP 
Sometimes Composer blocks you from downloading anything from HTTP URLs, but you can disable this adding a ```secure-http``` setting parameter in the config section of composer.json.  
```
{
  "config": {
    "preferred-install": "source"
    "secure-http": false
  }
}
```
This occurs (for example) when your patches are located in an external directory but within your corporate network, accessible from uncertified HTTP.  

 

## 6- Read More

* **Drupal 8 Composer Best Practices**, from [James Sansbury, @q0rban](https://twitter.com/q0rban) in Lullabot's website: [lullabot.com/drupal-8-composer-best-practices](https://www.lullabot.com/articles/drupal-8-composer-best-practices).

* **Using packages.drupal.org as repository**, from Drupal.org documentation: [drupal.org/docs/using-packagesdrupalorg](https://www.drupal.org/docs/develop/using-composer/using-packagesdrupalorg).  

* **Using Composer to Install Drupal and Manage Dependencies**, from Drupal.org documentation: [drupal.org/docs/using-composer-to-install-drupal-and-manage-dependencies](https://www.drupal.org/docs/develop/using-composer/using-composer-to-install-drupal-and-manage-dependencies).  

## 7- Notes

### <a name="note-1">1</a>.  
Please, consider using DDEV for your local deployments.  
* [Creating development environments for Drupal with DDEV](https://www.therussianlullaby.com/blog/creating-development-environments-for-drupal-with-ddev/).
* [Docker, Docker-Compose and DDEV - Cheatsheet](https://www.therussianlullaby.com/blog/docker-docker-compose-and-ddev-cheatsheet/).  
* [Books/ Local Web development with DDEV](https://www.therussianlullaby.com/blog/books-local-web-development-with-ddev-explained/).  


### <a name="note-2">2</a>.  
Review the tutorial series about how to install Composer in diverse Ubuntu / Debian versions, from Digital Ocean.  
* [How to Install and use Composer, Digital Ocean](https://www.digitalocean.com/community/tutorial_collections/how-to-install-and-use-composer).

## 8- :wq! 