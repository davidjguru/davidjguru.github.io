---
layout: post
title: Drupal Fast Tips (I) - Using links in Drupal 8
permalink: /blog/drupal-fast-tips-using-links-in-drupal-8
published: true
date: 2019-08-15
categories: [Drupal]
sitemap: true
---
| ![Picture from Unsplash, by @osmanrana]({{ site.baseurl }}/images/davidjguru_drupal_8_using_links_from_unsplash_1.jpeg) |
|:--:|
| *Picture from Unsplash, user Osman Rana @osmanrana* |


The first thing I need to clarify is that we are not going to talk about
***"using links"*** in Drupal 8. We're actually going to talk about how to
 build 
custom links in Drupal 8.  Well, I wanted to write about this but because of
the character limitation of the title string I used that form. 
So keep reading if you want to know how to build links programmatically in Drupal 8. 
Do you follow me?...
<!--more-->

## Introduction
In the context of the Internet, any web application has many, thousands of
 links. Knowing how to operate with links (and above all how they work) within Drupal is a fundamental tool to build projects programmatically. We know how to do it through sitebuilding...but what about code? We can start by imagining it as a small two-step process for which we will need to meet two new friends from the Drupal API. 

## Your new key friends
There are two fundamental classes we need to know for this: First, a PHP
 class that belongs to Drupal's core called "Url" and another class from the
  core called "Link". Very intuitive, isn't it? Well now we'll see why these
   two new friends are so important. 

### The URL Class
The URL class is used to model an object that contains information about a
 URL (its name, whether it is absolute or relative, internal, external, etc.).  URLs in Drupal 8 are represented through this class with namespace Drupal\Core\Url. Ok. 
 This class has a set of static methods indicated for building a URL. Specifically we will stop in two interesting cases that this class provides us: 
  1. The ability to build URLs from paths declared in routing files (my_module
  .routing.yml) through the ::fromRoute() static method. 
  2. The ability to build URLs from a URI address (internal or external
  ), using the ::fromUri() static method. 
  **Examples**
  
   ```php
      use Drupal\Core\Url;

     // Link to the Drupal Home page (declared in the file 'system.routing.yml')
     $url1 = Url::fromRoute('<front>');

     // Link to /admin/people (declared in the file 'user.routing.yml')
     $url2 = Url::fromRoute('entity.user.collection');

     // External Link to Drupal.org.
     $url3 = Url::fromUri('https://www.drupal.org');
   ```
 
* See more at: [The URL Class in the Drupal API Documentation](https://api
.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Url.php/class/Url/8.8.x){:target="_blank"}

### The Link Class
This class is responsible for modeling the corresponding HTML link from a
 series of parameters and with the possibility of loading options added to the future rendering of the element \<a href>. 
Certainly, in order to build HTML links it is not specifically necessary to use this class: you can use the 'LinkGenerator' service and call its generate() method as an alternative, but here I want to focus on the joint use of these two classes Url and Link.

The Link class has a very interesting static method, ::fromTextAndUrl(), which allows you to build HTML links on the fly. Then we'll see how you can combine this element to be rendered on screen. 

```php 
use Drupal\Core\Link;

$link = Link::fromTextAndUrl('This is my link', $url);
```

* See more at: [The Link Class in the Drupal API Documentation](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Link.php/class/Link/8.8.x){:target="_blank"}

## How it works
As I have seen, schematically, we need to cover three necessary steps at this time: 
1. Generate our URL.
2. Build our HTML link. 
3. Take care of rendering it on screen.

**Let's see how:** 
```php
use Drupal\Core\Url;
use Drupal\Core\Link;

// Link to /admin/structure/blocks.
$url = Url::fromRoute('block.admin_display');
$link = Link::fromTextAndUrl(t('Block administration page'), $url);

// Adding the new link to an array. 
$list[] = $link1;

// Mount the render output.
$output['my_links_page'] = [
      '#theme' => 'item_list',
      '#items' => $list,
      '#title' => $this->t('Examples of links:'),
    ];

return $output;
```
But this previous code block is not exact, it is not complete. We end up
 forming a render array and returning it but from where? It's time to take a step further and observe the whole case within a function belonging to a Controller. 

## Example: case use
As I said, let's make a small case of use something basic but useful to know the mechanics of operation of the relationship between these two classes of Drupal core (Url, Link). 

For this recipe you will need a Drupal installed and active (local, Dockerized, remote, etc.). To activate the custom module that we will create would be great to have Drush or Drupal Console (for convenience). 


### Mission - Listing a set of links
Let's set ourselves the task of creating a list of links that we will show
 inside Drupal. To do this, following the development guidelines of Drupal 8
 , we'll build a new custom module, defining a path to our list and complete
  the task by implementing a controller class that will be responsible for
   managing
   the response to that route, building the small final view consisting only of a set of links of different types. 
   
   | ![Custom module for links]({{ site.baseurl }}/images/davidjguru_drupal_8_using_links_creating_custom_module.png) |
   |:--:|
   | *Final frame of the example inside a plain Drupal (not through Composer)* |

### Creating a custom module
As usual, we'll start by creating a folder in the path **ourdrupal/modules
/custom/name_module**
 Creating a folder called *"links_example"*. 
 
 Also as usual, we will create the
  small informative file so that our Drupal knows with whom it is relating
   **name_module.info.yml** (and be careful with the indexing of the  YAML
    files :-P ). For the example, the file will be called *links_example.info
    .yml*.
 
  
 links_example.info.yml
 
 ```yaml
name: Links Example
type: module
description: 'Just a basic case of Links Processing.'
core: 8.x
package: 'Testing'
```

### Defining a route
The next step is to declare a route for our Drupal system using a routing file of the type **name_module.routing.yml**, where we model a route: we give it a specific name, an access path, we declare that Controller will be in charge of managing it and what type of access permissions it requires. 

This will be our routing file: 
*links_example.routing.yml*
```yaml
links_example.links:
  path: '/example/page/links'
  defaults:
    _controller: '\Drupal\links_example\Controller\LinksExampleController::links'
  requirements:
    _permissions: 'access content'
    _access: 'TRUE'
```
In the line "_controller" we are specifying which controller class will be in
 charge of processing the route and specifically through which method
 . (class LinksExampleController, method links()). 

### Implements a Controller
The time has come...to define our Controller class following the recommended
 guidelines for development in Drupal. We will implement our controller as a
  .php extension file in the path **ourdrupal/modules/custom/name_module/src
  /Controller/** .
  
  We will extend the ControllerBase
  class and we'll creating our method responsible for the management of the
   route
   that will build the output to the screen through a render array. 

In this case we will mount up to seven different examples of links and assign
 HTML attributes to the last one. Let's see: 

```php
<?php

/**
 * @file
 * Contains \Drupal\links_example\Controller\LinksExampleController.
 */

namespace Drupal\links_example\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\Core\Link;
use Drupal\Core\Url;

class LinksExampleController extends ControllerBase
{
  public function links()
  {
    // Link to /admin/structure/blocks.
    $url1 = Url::fromRoute('block.admin_display');
    $link1 = Link::fromTextAndUrl(t('Go to the Block administration page'), $url1);
    $list[] = $link1;

    // Link to /admin/content.
    $url2 = Url::fromRoute('system.admin_content');
    $link2 = Link::fromTextAndUrl(t('Go to the Content administration page'), $url2);
    $list[] = $link2;

    // Link to /admin/people.
    $url3 = Url::fromRoute('entity.user.collection');
    $link3 = Link::fromTextAndUrl(t('Go to the Users page'), $url3);
    $list[] = $link3;

    // Link to Home page.
    $url4 = Url::fromRoute('<front>');
    $link4 = Link::fromTextAndUrl(t('Go to the front page'), $url4);
    $list[] = $link4;

    // Link to the node with id = 1.
    $url5 = Url::fromRoute('entity.node.canonical', ['node' => 1]);
    $link5 = Link::fromTextAndUrl(t('Go to node with id = 1'), $url5);
    $list[] = $link5;

    // Link to the edit mode of the node with id = 1.
    $url6 = Url::fromRoute('entity.node.edit_form', ['node' => 1]);
    $link6 = Link::fromTextAndUrl(t('Go to the edit mode'), $url6);
    $list[] = $link6;

    // External Link to Drupal.org.
    $url7 = Url::fromUri('https://drupal.org.com');
     
    // We'll add some HTML attributes to the link. 
    $link_options = [
      'attributes' => [
        'target' => '_blank',
        'title' => 'Link to Drupal home page',
      ],
    ];
    $url7->setOptions($link_options);
    $link7 = Link::fromTextAndUrl(t('Go to Drupal.org site'), $url7);
    $list[] = $link7;

    // Mount the render output.
    $output['links_example'] = [
      '#theme' => 'item_list',
      '#items' => $list,
      '#title' => $this->t('Examples of links:'),
    ];
    return $output;
  }
}
```

### Rendering our links

First of all, we'll have to turn on our new custom module, enabling it within
 Drupal. We can use Drush or Drupal Console, or maybe through sitebuilding on
  path **/admin/modules**, as you wish. 
```bash
// Using Drush
drush en links_example
./vendor/bin/drush en links_example

// Using Drupal Console
drupal module:install links_example
drupal moi links_example
```

Go to path: **yourdrupalsite/example/page/links**

| ![Rendering custom links in Drupal 8]({{ site.baseurl }}/images/davidjguru_drupal_8_using_links_rendering_links.png) |
   |:--:|
   | *Rendering the custom set of Links the view of the "title" attribute is
    shown.* |

[Here you have the code of the example as a custom Drupal module ready to
 clone from gitlab (or download)](https://gitlab.com/davidjguru/drupal-custom-link-example){:target="_blank"}

## :wq!

Greetings. wq!    :-*