---
layout: post
title: 'Drupal Techniques: Creating nodes by code'
permalink: /blog/drupal-snippets-creating-nodes-by-code
published: true
date: 2020-05-17
author: davidjguru
categories: [Drupal Techniques]
sitemap: true
---
| ![Picture from Unsplash, by @manuelsardo]({{ site.baseurl }}/images/davidjguru_drupal_snippets_creating_nodes_programmatically.jpg) |
|:--:|
| *Picture from Unsplash, user [Manuel Sardo, @manuelsardo](https://unsplash.com/@manuelsardo)* |

Last week I needed to develop some functionality for a project and I did something I hadn't implemented for a long time: I had to create nodes through programming and so I realized some things that had been deprecated at this point, So I take this opportunity to publish a simple snippet about this.
<!--more-->

## Creating a custom module
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

## Creating a basic scaffolding for a node
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

## Creating taxonomy terms for the node
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

  // Save the node.
  $node_article->save();
```
## Creating an image for the node
Following with the example case, now I have to add an image to the node. First a need an available image inside my public path for files, within my Drupal installation, so I'll put an image on my /files folder:  

![Adding an image file to the node]({{ site.baseurl }}/images/davidjguru_drupal_snippets_creating_nodes_programmatically_files.png)  

Existing now the referenced image, I can build a file using the resource:

```php
// Create a file.
  $file = File::create([
    'uid' => 1,
    'filename' => 'drupalea.png',
    'uri' => 'public://creating_nodes_files/drupalea.png',
    'status' => 1,
  ]);

  // Save the file item.
  $file->save();
```

So now my node is being well-formed: 

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
    'field_image' => [
      [
        'target_id' => $file->id(),
        'alt' => 'Alt text for the image',
        'title' => 'Title for the image',
      ],
    ],
  ]);

  // Save the node.
  $node_article->save();
```
Ok, the node goes well, but I think I need load a custom path, so let's go to create it.

## Creating paths for the node
Okay, I got some surprises here. As I remembered, to create paths I can use some like this: 

```php
\Drupal::service('path.alias_storage')->save("/node/" . $node->id(), "/newsletter", "en");
```

But doesn't work...why? well, seems some deprecated...  
*See:*  
* [Deprecate the custom path alias storage](https://www.drupal.org/project/drupal/issues/2233595).
* ['Path aliases have been converted to revisionable entities'](https://www.drupal.org/node/3013865). 

So I did it with two options: 

1-Using pathauto module: You have patterns for the content type already defined, so you can load directly the node using functions of the pathauto internal API: 

```php
pathauto_entity_insert($node_article);
```
2-Without pathauto:  
```
use Drupal\path_alias\Entity\PathAlias;

$path_alias = PathAlias::create([
  'path' => '/node/' . $node_article->id(),
  'alias' => '/newsletter/kplan',
  ]);
$path_alias->save();
```
## Adding a Paragraph

Maybe you have some paragraphs available in your Drupal installation. You can load some paragraph in your content type and fill the values by code. 

In this case we're using a simple paragraph with a text field that allows full HTML.   

**First:** Creating a text for the paragraph.  

```
$text = '<h2 class="h2-my-paragraph">See the last info in this paragraph.</h2>';
```
**Second:** Creating a new paragraph using the type already created in your Drupal installation.
```
use Drupal\paragraphs\Entity\Paragraph;
...
  $paragraph = Paragraph::create([
    'type' => 'paragraph_custom',   
    'pc_text' => [  
      'value' => $text,                  
      'format' => 'full_html', 
    ],
  ]);
  $paragraph->save();
```

**Third:** Add the paragraph element to the main node. 
```
use Drupal\file\Entity\File;
use Drupal\node\Entity\Node;
use Drupal\path_alias\Entity\PathAlias;
use Drupal\taxonomy\Entity\Term;
use Drupal\paragraphs\Entity\Paragraph;
...

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
    'field_image' => [
      [
        'target_id' => $file->id(),
        'alt' => 'Alt text for the image',
        'title' => 'Title for the image',
      ],
    ],
    'field_paragraph' => [
      [
        'target_id' => $paragraph->id(),
        'target_revision_id' => $paragraph->getRevisionId(),
       ],
     ],
  ]);

  // Save the node.
  $node_article->save();
```

## Adding fields for the Content Type
If you need add more fields to the content type by code, you can use the next code and put it in a .install file or in a hook_update_N().  

The next snippet will add a basic text field to the Content Type "Article", creating its own tables (node__field_*, node_revision__field_*) in database:  

```
use Drupal\field\Entity\FieldConfig;
use Drupal\field\Entity\FieldStorageConfig;

$field_storage = FieldStorageConfig::create([
    'field_name' => 'field_calasparra', // Using machine_name
    'entity_type' => 'node',
    'type' => 'text',
  ]);
  $field_storage->save();

  $field = FieldConfig::create([
    'field_name' => 'field_calasparra',
    'entity_type' => 'node',
    'bundle' => 'article',
    'label' => 'Testing field creation by code', // Extended name.
  ]);
  $field->save();
```

You can see this custom module example in [my Gitlab repository here](https://gitlab.com/davidjguru/drupal-custom-modules-examples/-/tree/master/creating_nodes), and download with more resources from [the main repository in Gitlab](https://gitlab.com/davidjguru/drupal-custom-modules-examples/-/tree/master/).

**Extra:**
Remember that you can create nodes for testing using Drupal Console -for example- or Drush using the Devel Generate module from the [Devel family](https://www.drupal.org/project/devel). 
Creating nodes in Drupal 8 with Drupal Console (Module Devel Generate style):

```
drupal create:nodes article --limit="6" --title-words="2" --time-range="10" --language="en"
# It will ask about add revisions by prompt
```

## :wq! 
