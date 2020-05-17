---
layout: post
title: Drupal Snippets (II) - Creating nodes programmatically
permalink: /blog/drupal-snippets-creating-nodes-programatically
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

First of all I've created a new custom module to gather these snippets, called `creating_nodes` using drupal console:

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

Now, enabling the module by Drush using  `drush en creating_nodes` you can see in path `/admin/content' the new created node in your Drupal installation: 

![New node article just created]({{ site.baseurl }}/images/davidjguru_drupal_snippets_creating_nodes_programmatically_two.png)  



