---
layout: post
title: Patterns and Aliases programmatically in Drupal
permalink: /blog/patterns-and-aliases-programmatically-in-drupal
published: true
date: 2021-02-22
author: davidjguru
categories: [Drupal & Coding]
sitemap: true
youtubeId: wqfeeRz_fwE
---

| ![Picture from Unsplash, by @jack_1]({{ site.baseurl }}/images/davidjguru_drupal_8_9_patterns_and_aliases_programmatically_in_drupal_main.jpg) |
|:--:|
| *Picture from Unsplash, user [Rémi Jacquaint](https://unsplash.com/@jack_1)* |

Sometimes in an initial phase of a Drupal project you need prepare some kind of tasks related with routing: creates new routes, defines some Controllers or Forms o maybe just define the so called "aliases". The aliases allows us define URLs more friendly than the natural, or even thinking in build some patterns in order to apply to our content or entities (users, taxonomy terms, nodes, etc). I'm talking about pretty and semantic URLs like "mydomain.com/blog/article/special-article".  
<!--more-->

Drupal allows define one by one these kinds of directions, but also we can work with batch processing, organizing the work from the code side. Let's see some ideas and examples and we're going to take a walk around [the pathauto module for Drupal](https://www.drupal.org/project/pathauto). Pay attention.   

 ---------------------------------------------------------------------------------
  
  **Table of Contents**  
  <!-- TOC -->  
  [1- Introduction](#1--Introduction)  
  + [1.1- Recipe](#11--recipe)  
  + [1.2- Extra](#12--extra)    

  [2- Let's go: Resources](#2--letsgo-resources)  
  + [2.1- Creating resources: Vocabularies](#21--creating-resources-vocabularies)  
  + [2.2- Creating resources: Taxonomy Terms](#22--creating-resources-taxonomy-terms)  

  [3- Adding new patterns by code](#3--adding-new-patterns-by-code)    
  [4- Creating an alias for an item](#4--creating-an-alias-for-an-item)  
  [5- Applying alias patterns to a vocabulary](#5--applying-alias-patterns-to-a-vocabulary)  
  [6- :wq!](#6--wq)  
  <!-- /TOC -->
  
  -------------------------------------------------------------------------------
## 1- Introduction 

For some ideas and side-projects, I was thinking in enabling sets of taxonomy terms from an external source, and so I could move the data just like a migration and also as part of the installing process of a custom module. For this case, I'll use the approach of a simple custom Drupal module created for registering some data, creating vocabularies, taxonomy terms and aliases but without using Migrations (I guess that I'll write about migrations of patterns for aliases next months in [www.therussianlullaby.com](https://www.therussianlullaby.com/)). I just wanna play with functions in a simple way in order to show how we can work along with a special kind of Drupal Entity: [The PathautoPattern Entity](https://git.drupalcode.org/project/pathauto/-/blob/8.x-1.x/src/Entity/PathautoPattern.php), provided by [the Pathauto module for Drupal](https://www.drupal.org/project/pathauto).  

By default Drupal implements URLs like `node/nid` or `taxonomy/term/tid` URL paths for entities and bundles. This is very easy to test, just creating a node in Drupal and seeing its related URL after published: `/node/4`. Ok.   

The Pathauto module offers some interesting options to update URLs related with specific entities / bundles in your Drupal installation (content, taxonomy terms, users), giving support for tokens, bulk updates and automatic generation of aliases by creating patterns directly related with entities (patterns for vocabularies but also for certain vocabularies, using more granularity). In addition, the pathauto module is part of needed resources in order to avoid attacks, as you can read in this recent article about prevention for enumeration attacks in Drupal: [hashbangcode.com - drupal 9 preventing enumeration attacks](https://www.hashbangcode.com/article/drupal-9-preventing-enumeration-attacks).  

The module works from a User Interface in your Drupal installation, in path `/admin/config -> /search/path -> /patterns` and you'll see its tabs: 


![URL aliases section in Drupal 8 or 9]({{ site.baseurl }}/images/davidjguru_drupal_8_9_patterns_and_aliases_programmatically_in_drupal_1.png)

  At the core of all this, there's a very interesting concept in order to work with patterns: the PathautoPattern Entity. This post is talking 'bout working with this Drupal entity from a programmatic point of view. We're going to do some tasks not from the UI, but from custom code.  


### 1.1- Recipe 

Well, for this recipe I'll use some of my usual ingredients:   

* A DDEV-Local based environment (Dockerized et al) for a Drupal 9 installation. Follow this guide from Digital Ocean: [digitalocean.com/deploy-drupal-using-docker-and-ddev](https://www.digitalocean.com/community/tutorials/how-to-develop-a-drupal-9-website-on-your-local-machine-using-docker-and-ddev).  
* A Drupal 9 deployed in your local. Follow the snippet: [Drupal 9 in six steps using DDEV: Quick Deploy](https://gitlab.com/-/snippets/2012512).  
* The Pathauto contrib module installed and enabled with its dependencies. Follow the Snippet: [Drupal 8-9 - Install Pathauto module from a DDEV deploy](https://gitlab.com/-/snippets/2080056).  
* A new custom Drupal module created by using `drush generate`. Follow the Snippet: [Drupal 8-9 - Creating custom resources using Drush generate](https://gitlab.com/-/snippets/2054593).  

### 1.2- Extra 
For this post, I'm using a simple [hook_install()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Extension%21module.api.php/function/hook_install/9.0.x) as a playground. All the code will be developed inside this classic hook that executes just in the installing process. You can use the same services, classes and resources from a more OOP context with Dependency Injection in Drupal.  In addition, as we alredy know- Drupal first loads the provisioned configuration and only then executes code from hooks. I mean you can create configuration objects while installing and after that you can manipulate the config object o doing changes over its values from a hook_install(). And this is pretty interesting. You can see an example of config + code in hook_install() in this other post from this same sketchbook: [Placing a Block by Code](https://davidjguru.github.io/blog/drupal-fast-tips-placing-a-block-by-code).   


## 2- Let's go: Resources 
If you followed the previous steps then you have now a ready-to-go environment to test. Remember install pathauto and creates a new custom module. Just by tiping:  
```
$ ddev composer require drupal/pathauto 
$ ddev drush en pathauto -y 
```
This will install pathauto and its dependencies: ctools, path and token (all in core now). Then you can use the example from the next gist for generate a new custom module:    
{% gist 590bf212c2a31528ea872a27f7bf3443 %} 

After you install the Pathauto module, you can access to new tabs with some related actions. Now, we're gonna execute some actions just by code and from our custom module, but with an eye in its options from UI. We need generate some resources in the Drupal installation. I'm gonna call my new module "testing_pathauto". Let's see.  



## 2.1- Creating resources: Vocabularies

Now we're going to create some resources. We'll need new vocabularies with loaded terms, so we can see how generate the data we need just when installs. We'll create three vocabularies: films, songs and series. In order to practise, I'm using two different ways to do this:  

### Vocabularies  
* Creating a vocabulary 'songs' by a config file in path `/testing_pathauto-> /config -> /install`.  I will put the module as a forced dependency by itself, for delete the vocabulary when disabling module.  
* Creating a pair of vocabularies 'films' and 'series' from code, inside the `testing_pathauto.install` file, within the hook_install() function.  

File: taxonomy.vocabulary.songs.yml  

```yaml
langcode: en
status: true
dependencies:
  enforced:
    module:
      - testing_pathauto
name: Songs
vid: songs
description: 'Provides categories for songs.'
hierarchy: 0
weight: 0
```
By code:   

```php
<?php

use Drupal\taxonomy\Entity\Term;
use Drupal\taxonomy\Entity\Vocabulary;

/**
 * Implements hook_install().
 */
function testing_pathauto_install() {
// Creates a pair of new vocabularies for taxonomy terms.
  $vid_1 = 'films';
  $name_1 = 'Films';
  $vocabulary_1 = Vocabulary::create([
    'vid' => $vid_1,
    'machine_name' => $vid_1,
    'description' => 'Stores items for categorizing films.',
    'name' => $name_1,
  ]);
  $vocabulary_1->save();

  $vid_2 = 'series';
  $name_2 = 'Series';
  $vocabulary_2 = Vocabulary::create([
    'vid' => $vid_2,
    'machine_name' => $vid_2,
    'description' => 'Stores taxonomy terms for series items.',
    'name' => $name_2,
  ]);
  $vocabulary_2->save();
}
```

These code blocks will create the three new vocabularies in your Drupal installation just in install.  

### 2.2- Creating resources: Taxonomy Terms 

Now I'm gonna to populate the previous vocabularies with some terms. For doing this, I'm creating two terms for the first vocabulary by the classical method using the related class: 

```php
  // Creates taxonomy terms for the first vocabulary.
  $term_one = Term::create([
  'vid' => 'films',
  'langcode' => 'en',
  'name' => 'T_1',
  'description' => [
    'value' => '<p>Term number one.</p>',
    'format' => 'full_html',
  ],
  'weight' => -1,
  'parent' => [0],
  ]);

  $term_two = Term::create([
    'vid' => 'films',
    'langcode' => 'en',
    'name' => 'T_2',
    'description' => [
      'value' => '<p>Term number two.</p>',
      'format' => 'full_html',
    ],
    'weight' => 1,
    'parent' => [0],
  ]);
  // Saves the taxonomy terms.
  $term_one->save();
  $term_two->save();
```

And for the other two cases, I'm using a loop in order to populate the vocabularies 'songs' and 'series', something like this:  

```php
// Creates sixty taxonomy terms.
for ($i = 1; $i <= 60; $i++) {
    ${'term_' . $i} = Term::create([
    'vid' => 'series',
    'langcode' => 'en',
    'name' => 'term_' . $i . '_NAME_SERIE',
    'description' => [
      'value' => '<p>Term number ' . $i .'</p>',
      'format' => 'full_html',
    ],
    'weight' => $i,
    'parent' => [0],
    ]);

    // Saves the current taxonomy term.
    ${'term_' . $i}->save();
  }
```

In both cases, you'll have the vocabularies already filled with terms when installs.   


## 3- Adding new patterns by code 

Well, maybe the first interesting thing is that the Pathauto module is providing us with a new Entity called "PathAutoPattern", available for processing new patterns. But we have to do some initial questions...what kind of entity is? For get the info, You can ask to the Entity system when Pathauto module is installed in your system. 

First, If I try to create a new pattern using the classical way for Drupal Entities (the EntityTypeManager), I can get the new created entity, only with some data that I can see from the UI, just doing this:  

```php 
  $data = [
    'id' => 'my_pattern_machine_name',
    'label' => 'Testing pattern for isolated terms',
    'type' => 'canonical_entities:taxonomy_term',
    'pattern' => '/random/[term:name]',
    'weight' => -1,
  ];

  // Creates the new configuration entity and saves it.
  $pattern = \Drupal::entityTypeManager()->getStorage('pathauto_pattern')->create($data);
  $pattern->save();
```
Ok, it works. But I suspect I need more data than these lines from the former block. I need to ask to the new entity about some questions about its nature. I can get some info asking to the new `$pattern` variable, so I'm doing this: 

```php
// Gets the entity type, group and ID. 
$group = $pattern->getEntityType->getGroup();
$type = $pattern->getEntityType();
$id = $pattern->getEntityTypeId();
```

See the returned info:  

![Getting info about the king of entity PathautoPattern is]({{ site.baseurl }}/images/davidjguru_drupal_8_9_patterns_and_aliases_programmatically_in_drupal_2.png)


So now my first doubt was...how I can create patterns entity by coding? As you can see in the former caption the entity is a Config entity, so I can access to its data and structure by going to the Config / Sync section (also I can generate it by config file, but I want doing by code). So I can create some pattern by interface and then, watching the config file of the new pattern, I can understand the data structure:  


![Config Object for Pathauto Pattern Entities]({{ site.baseurl }}/images/davidjguru_drupal_8_9_patterns_and_aliases_programmatically_in_drupal_3.png)

Ok! so there's some data related with "selection criteria" in the config object...Now I understand why my first pattern has the fourth columm "Conditions" empty. So I'm gonna add some data in a related new section. What's the problem? I need to think about transform the data structure in the related YAML file as in a classic nested array. Here we go! Now there will be some criteria selection configured in the pattern, in order to link the pattern with an bundle, just like in the next code block:  

```php
// Sets values for a new pattern for films vocabulary.
  $uuid_1 = \Drupal::service('uuid')->generate();

  $data_1 = [
    'id' => 'my_pattern_machine_name_1',
    'label' => 'Testing pattern 1 for Films',
    'type' => 'canonical_entities:taxonomy_term',
    'pattern' => '/films/[term:name]',
    'selection_criteria' => [
      $uuid_1 => [
        'id' => 'entity_bundle:taxonomy_term',
        'bundles' => [
          'films' => 'films',
        ],
        'negate' => FALSE,
        'context_mapping' => [
          'taxonomy_term' => 'taxonomy_term',
        ],
        'uuid' => $uuid_1,
      ],
    ],
    'weight' => -4,
  ];
  // Creates the new configuration entity and saves it.
  $pattern_1 = \Drupal::entityTypeManager()->getStorage('pathauto_pattern')->create($data_1);
  $pattern_1->save();
```

It works perfectly and now I'll have as many URL patterns created by code as I need:  

![Well formed patterns by pathauto created from custom code]({{ site.baseurl }}/images/davidjguru_drupal_8_9_patterns_and_aliases_programmatically_in_drupal_4.png)  

But remember: There will be (by now) some patterns with conditions ('selection_criteria') and others don't. This will be important in a next section.  


## 4- Creating an alias for an item 

Although we have been playing guessing games and practicing trial/error, what we have really been doing is intuitively approaching the most basic internal behavior of the Pathauto module. Specifically the behavior declared from its path to add new patterns via entity form (add_form), just as is declared in its routing file.  Open the Drupal installation in you main IDE and see the pathauto module. It's time to peek inside.  

File: pathauto.routing.yml

```yaml
entity.pathauto_pattern.add_form:
  path: '/admin/config/search/path/patterns/add'
  defaults:
    _entity_form: 'pathauto_pattern.default'
    _title: 'Add Pathauto pattern'
    tempstore_id: 'pathauto.pattern'
  requirements:
    _permission: 'administer pathauto'
```

The register form is marked as 'pathauto_pattern.default' and in its main Entity definition file, in annotations block:  

```php
 *      "form" = {
 *       "default" = "Drupal\pathauto\Form\PatternEditForm",
 *       "duplicate" = "Drupal\pathauto\Form\PatternDuplicateForm",
 *       "delete" = "Drupal\Core\Entity\EntityDeleteForm",
 *       "enable" = "Drupal\pathauto\Form\PatternEnableForm",
 *       "disable" = "Drupal\pathauto\Form\PatternDisableForm"
 *     },
 ```

Where we can see that in certain conditions we're walking the same steps (but with less complexity in our case, for sure):  

```php
if ($bundles = array_filter((array) $form_state->getValue('bundles'))) {
        $default_weight -= 5;
        $plugin_id = $entity_type == 'node' ? 'node_type' : 'entity_bundle:' . $entity_type;
        $entity->addSelectionCondition(
          [
            'id' => $plugin_id,
            'bundles' => $bundles,
            'negate' => FALSE,
            'context_mapping' => [
              $entity_type => $entity_type,
            ]
          ]
        );
      }
```

See more at:  
[drupal/pathauto/PatternEditForm.php](https://git.drupalcode.org/project/pathauto/-/blob/8.x-1.x/src/Form/PatternEditForm.php#L231)  

But now we can test how to apply a new pattern to a bundle, so we can use a service included in the Pathauto module, the so called "pathauto.generator". You can see all the exposed services by pathauto in the file `pathauto.services.yml`. This service delegates its functionality over a [class called `PathautoGenerator.php`](https://git.drupalcode.org/project/pathauto/-/blob/8.x-1.x/src/PathautoGenerator.php) oriented to provide methods for generating path aliases. From a procedural context (within a hook_install(), remember) we can call the service in a very simple way:  

```php
  $term = \Drupal::entityTypeManager()->getStorage('taxonomy_term')->load(14);
  \Drupal::service('pathauto.generator')->createEntityAlias($term, 'insert');
```
In this case, I'm applying a pattern to a taxonomy term using its tid value (14), by calling to the pathauto.generator service, passing the taxonomy term and a opt key 'insert' for the method. Ok, and it works too. You can see now:  

![Applying a new pathauto pattern to an existing taxonomy term]({{ site.baseurl }}/images/davidjguru_drupal_8_9_patterns_and_aliases_programmatically_in_drupal_5.png)  

And so a new pathauto pattern has been applied to our taxonomy term with tid = 14...eh, wait a minute...how knows the pathauto.generate service which pattern has to be applied? Interesting!   



## 5- Applying alias patterns to a vocabulary 

Well, the next question is...How knows Pathauto what pattern must be applied to the item?  And its a quite interesting topic. In short, we'll say that the PathautoGenerator class, has a protected function in order to get all the patterns linked to a Entity Type. How it works? Well, do you remember our two examples about building patterns? The key is in the "selection_critera", that defines the link between the pattern and the related bundle.  

```php
 'selection_criteria' => [
      $uuid_1 => [
        'id' => 'entity_bundle:taxonomy_term',
        'bundles' => [
          'films' => 'films',
        ],
        'negate' => FALSE,
        'context_mapping' => [
          'taxonomy_term' => 'taxonomy_term',
        ],
        'uuid' => $uuid_1,
      ],
    ],
```

Due to this, if you avoid creation of a pathauto pattern without selection criteria defined, then your pattern will be available for all the bundles from the Entity Type.  But its just the first key. Now we're going to talk about the second.  

So [you can see here](https://git.drupalcode.org/project/pathauto/-/blob/8.x-1.x/src/PathautoGenerator.php#L293) this:  

```php
protected function getPatternByEntityType($entity_type_id) {
    if (!isset($this->patternsByEntityType[$entity_type_id])) {

      $ids = $this->entityTypeManager->getStorage('pathauto_pattern')
        ->getQuery()
        ->condition('type', array_keys(
          $this->aliasTypeManager
            ->getPluginDefinitionByType($this->tokenEntityMapper->getTokenTypeForEntityType($entity_type_id))))
        ->condition('status', 1)
        ->sort('weight')
        ->execute();

      $this->patternsByEntityType[$entity_type_id] = $this->entityTypeManager
        ->getStorage('pathauto_pattern')
        ->loadMultiple($ids);
    }
```
As you can see in the former snippet, the function is returning the linked patterns using "weight" as a condition for the query. And it will return the results ordered by weight property value but using descending order, I mean: If you're creating a pattern without "selection criteria" and the assigned weight is lower than your previous defined pathauto patterns, then this will be the last pattern processed and finally, the aliases wet generated comes from the last pathauto pattern with the lowest weight: Think as a pathauto pattern without conditions for selections will be available, for example, for all the vocabularis in your Drupal installation.  

You can set the value for weight in your custom code or just changing the position by UI:  

![Change the order in pathauto patterns available]({{ site.baseurl }}/images/davidjguru_drupal_8_9_patterns_and_aliases_programmatically_in_drupal_6.png)  


Taking all of the above into account as operational criteria (selectio, weight) we can launch processing for applying pathauto patterns in vocabularies, for example doing something like this:  

```php
// Gets taxonomy terms from films vocabulary using entityTypeManager and conditions.
  $taxonomy_films_storage = \Drupal::entityTypeManager()->getStorage('taxonomy_term');
  $query_films = $taxonomy_films_storage->getQuery()
    ->condition('vid', 'films')
    ->execute();
  $entities_films = $taxonomy_films_storage->loadMultiple($query_films);

  // Updates URL aliases just in case of taxonomy terms in films vocabulary.
  foreach ($entities_films as $entity) {
    \Drupal::service('pathauto.generator')->createEntityAlias($entity, 'insert');
  }
```

I added a copy of my custom "testing_pathauto" module in my Gitlab folder for custom Drupal projects (only for testing, just for fun NOT FOR STAGE OR LIVE): [https://gitlab.com/davidjguru/drupal-custom-modules-examples](https://gitlab.com/davidjguru/drupal-custom-modules-examples).  

You can download or git clone the whole proyect or get the specific folder for this module in: [gitlab.com/davidjguru/testing-pathauto](https://gitlab.com/davidjguru/drupal-custom-modules-examples/-/tree/master/testing_pathauto).  

Just download the resource and enable the module by doing:  


```
$ ddev drush en testing_pathauto -y
```

And by enabling, you'll get three vocabularies with terms (2, 10, 60 terms for each). Four patterns for pathauto created and enabled and all the taxonomy terms with new aliases created by these new patters. On the fly!  

## 6- :wq!

### Recommended song: Asilos Magdalena - The Mars Volta

{% include youtubePlayer.html id=page.youtubeId %}
