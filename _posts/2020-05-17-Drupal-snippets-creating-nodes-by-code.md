---
layout: post
title: Drupal Snippets (II) - Creating nodes by code
permalink: /blog/drupal-snippets-creating-nodes-by-code
published: true
date: 2020-05-17
author: davidjguru
categories: [Snippets]
sitemap: true
---
| ![Picture from Unsplash, by @manuelsardo]({{ site.baseurl }}/images/davidjguru_drupal_snippets_creating_nodes_programmatically.jpg) |
|:--:|
| *Picture from Unsplash, user [Manuel Sardo, @manuelsardo](https://unsplash.com/@manuelsardo)* |

Last week I needed to develop some functionality for a project and I did something I hadn't implemented for a long time: I had to create nodes through programming and so I realized some things that had been deprecated at this point, So I take this opportunity to publish a simple snippet about this.
<!--more-->

First of all I've created a new custom module to gather these snippets, called: `creating_nodes` using drupal console:

```
drupal generate:module \
--module="Creating Nodes
--machine-name="creating_nodes
--module-path="modules/custom" \
--description="Creating nodes in Drupal 8|9 programmatically" \
--core="8.x" \
--package="Workshop Drupal"
--module-file \
--no-interaction
```
Well, then I've created a .install file in order to use the hook_install() for testing the node creation, just like this: 

```php
function creating_nodes_install() {

  // Ask for the current time.
  $requested_time = \Drupal::time()->getRequestTime();

 // Define basic values for the new node.
  $node_article = Node::create([
    'type' => 'article',
    'langcode' => 'en',
    'created' => $requested_time,
    'changed' => $requested_time,
    'uid' => 1,
    'title' => 'Article number one',
  ]);

  // Save the node.
  $node_article->save();
}
```

Now, enabling the module by Drush using:  `drush en creating_nodes` you can see in path: `/admin/content` the new created node in your Drupal installation: 

![New node article just created]({{ site.baseurl }}/images/davidjguru_drupal_snippets_creating_nodes_programmatically_two.png)  


But then I wanted some taxonomy terms, so I did:  

```php
// Create two taxonomy terms.
  $term_one = Term::create([
    'vid' => 'tags',
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
    'vid' => 'tags',
    'langcode' => 'en',
    'name' => 'T_2',
    'description' => [
      'value' => '<p>Term number two.</p>',
      'format' => 'full_html',
    ],
    'weight' => 1,
    'parent' => [0],
  ]);

  // Saving the taxonomy terms.
  $term_one->save();
  $term_two->save();

  // Get all the terms from a taxonomy.
  $terms = \Drupal::entityTypeManager()->getStorage('taxonomy_term')->loadTree('tags', 0, NULL, FALSE);
```
Now, $terms is a just an array of partial objects (not the whole Entity, very heavy-weight for testing) based in Taxonomy Term Entity model. See [TermStorage::loadTree](https://api.drupal.org/api/drupal/core%21modules%21taxonomy%21src%21TermStorage.php/function/TermStorage%3A%3AloadTree/8.8.x). And so, I can load a term as a tag in my testing node. I will add also a text for the body: 

```php
// Building the new node.
  $node_article = Node::create([
    'type' => 'article',
    'langcode' => 'en',
    'created' => $requested_time,
    'changed' => $requested_time,
    'uid' => 1,
    'title' => 'Article number one',
    'field_tags' => $terms[1]->tid,
    'body' => [
      'summary' => 'Summary for the node created programmatically.',
      'value' => "This is the body of the node <br> allows HTML tags if needed.",
      'format' => 'full_html',
    ],
  ]);
```


**Extra:**
Remember that you can create nodes for testing using Drupal Console -for example- or Drush using the Devel Generate module from the [Devel family](https://www.drupal.org/project/devel). 
Creating nodes in Drupal 8 with Drupal Console (Module Devel Generate style):

```
drupal create:nodes article --limit="6" --title-words="2" --time-range="10" --language="en"
# It will ask about add revisions by prompt
```
