---
layout: post
title: Three tips working with Drupal
permalink: /blog/three-tips-working-with-drupal
published: true
date: 2017-09-13
categories: [Drupal, Web, Projects]
sitemap: true
---


I have been working with Drupal 7 recently and I have done configuration and development tasks for projects based on Drupal 7: projects of work (consultancy) and other tasks
for Drupal modules on which I am working in my free time.
I would like to make a collection of snippets and ideas that I have implemented recently (some of which I have not done for a long time) and to register here. Maybe it
will help someone at some point who needs to do the same things.
<!--more-->


## First - Two basic hooks

**Context:**  A Very Quick Introduction to Drupal's hook_menu() and hook_help(). And what is a hook? Hooks in Drupal are just ways of modifying the website page's results
 through methods which connecting to the Drupal Core and are provided by the Drupal API (or specific APIs like Form API, for example).  

**Problem:** Well, the problem was very simple...I want to create a basic module for working on a crazy idea, and the first question is all about to complete the basic
Drupal hooks for give the elemental form to my new and little new module.  

**Questions:** Which are the elementary methods for a module in Drupal? well, although in reality there are many and the answer is relative, to start with a good foot and go "exercising"
 in the wonderful world of Drupal hooks I recommend starting with two very intuitive:

**Hook_help() and Hook_menu()**

I think getting closer to these basic Hooks is a good way to get started with Drupal. On one hand, the hook_help() provides help and additional information about the module to the user. You can learn more about this elementary hook in the official Drupal documentation and [this brief tutorial on how to implement it]( https://www.drupal.org/docs/7/creating-custom-modules/writing-comments-and-implementing-your-first-hook){:target="_blank"}.

On the other hand, hook_menu() is another elementary function provided by [the system API system.api.php](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_menu/7.x){:target="_blank"}, and consists of a method for this hook enables modules to register paths in order to define how URL requests are handled. Paths may be registered for URL handling only, or they can register the link to be placed in a menu (usually the Navigation menu).

And don't forget the legendary Drupal t() function, that wraps the text marks for translation. This function is for make strings used in Drupal translatable. Every string in a t() function can be translated through the Drupal UI, so you must use it when you "paint" strings on screen and you want that your module be translatable.

**Results:**


```php
/**
 * Implements hook_help().
 *
 * Displays help and module information.
 *
 * @param path
 *   Which path of the site we're using to display help
 * @param arg
 *   Array that holds the current path as returned from arg() function
 */

function module_help($path, $arg) {
  switch ($path) {
    case "admin/help#module":
            $output = '';
            $output .= '<h3>' . t('About') . '</h3>';
            $output .= '<p>' . t('This module allows you to create an awesome set of things') . '</p>';

            $output .= '<h3>' . t('Uses') . '</h3>';
            $output .= '<p>' . t("You can use this module as you can") . '</p>';
            return $output;
            break;
  }
}
```
```php

/**
 * Implements hook_menu().
 */

function module_menu() {
  $items = array();

  $items['admin/config/content/module'] = array(
    'title' => 'Module',
    'description' => 'Configuration for this module',
    'page callback' => 'drupal_get_form',
    'page arguments' => array('module_form'),
    'access arguments' => array('access administration pages'),
    'type' => MENU_NORMAL_ITEM,
  );

  return $items;
}

```




## Second -  Dynamic selects in a Drupal Form (changing options)


**Context:** My workmate [Diego Guillermo](https://twitter.com/diegoguillermo4){:target="_blank"} and me  are working on a slightly crazy idea to build a module (currently Drupal 7) that geolocates a visitor to a website and based on its geographic location, operate on the visibility of nodes and fields. This is a slightly longer idea, but this serves as a summary.

![Geohide just for Drupal by Rojomorgan]({{ site.baseurl }}/images/geohide_drupal_rojomorgan_branding.png)


We've called our future module "Geohide" and we're working inside [a Drupal.org sandbox](https://www.drupal.org/sandbox/diegol_de_los_bosques/2827812){:target="_blank"} and I promise myself to write a post about our future module. Ok.

**Problem:** The operating mode we had come up with it was to offer a module configuration menu in which the user could make several selections to set the country, city, or region. And then select what Content Type, Node and field wanted to apply the functions of Geohide itself. This generates a structure of location and dynamic filtering of the fields, in this order:

1. Select the type of content.
2. Select the node (s)
3. Select the field or fields to hide.

**Questions:** The mechanics had to be that when selecting a certain content type they would be loaded on the next select the created nodes and then the available fields. How to get successive dynamic changes in the three selects of the form?

The key was to combine two techniques: The possibility of working with "event" type attributes within the form component and the possibilities of [the AJAX API in Drupal](https://api.drupal.org/api/drupal/includes%21ajax.inc/group/ajax/7.x){:target="_blank"}.

*Idea:* describe an event on the first select, control it and activate AJAX operations for real-time modification.

**Results:**

This is the first select, oriented to get a Drupal content type created in the website. Bind an AJAX callback to the change event (wich is coded for the select form type of the first dropdown. It will replace the second dropdown and the third dropdown when rebuilt. When "event" ocurrs, Drupal will perform an AJAX request in background. Usually the default value is sufficient (eg. change for selec elements), but it's valid with values including any JQuery event, like 'mousedown', 'blur', 'submit' and many others.


```php
 $form['dropdown_first'] = array(
          '#type' => 'select',
          '#title' => t('Select a Content Type'),
          '#description' => t('Take a content type for use with Geohide'),
          '#options' => $options_first,
          '#default_value' => $value_dropdown_first,
          '#ajax' => array(
          'event' => 'change',
          'callback' => 'geohide_ajax_callback',
          'wrapper' => array('dropdown_second_replace', 'dropdown_thrid_replace'),
            ),
           );
```

Now, the second dropdown. With the parameter 'multiple' we'll allow multiple selection of nodes. But the most important thing here is the closing div created for replacement when dropdown_first is changed in the previous dropdown.
When the form is rebuilt during AJAX processing, the $value_dropdown_first variable will have the new value and so the options will change.

```php
 $form['dropdown_second'] = array(
          '#type' => 'select',
          '#title' => t('Select all the nodes what you need'),
          '#description' => t('Here you can select all the nodes whose fields you want to hide'),
          '#multiple' => TRUE,
          '#prefix' => '<div id="dropdown_second_replace">',
          '#suffix' => '</div>',
          '#options' => geohide_second_dropdown_options($value_dropdown_first),
          '#default_value' => isset($form_state['values']['dropdown_second']) ? $form_state['values']['dropdown_second'] : '',
    );

```

Here, in the third dropdown we allow the multiple selection as well and the processing is the same than the second dropdown.

```php
 $form['dropdown_third'] = array(
          '#type' => 'select',
          '#title' => t('Select the fields from the selected Content Type'),
          '#description' => t('And now, check all the fields to hide'),
          '#multiple' => TRUE,
          '#prefix' => '<div id="dropdown_third_replace">',
          '#suffix' => '</div>',
          '#options' => geohide_third_dropdown_options($value_dropdown_first),
          '#default_value' => isset($form_state['values']['dropdown_third']) ? $form_state['values']['dropdown_third'] : '',

     );

```

Ok, and how we launch the form rebuilt? Using a ajax_callback() function, which launch the two replace commands to re-draw the form on every change at the first dropdown.


```php

function geohide_ajax_callback($form, $form_state) {
          return array(
          '#type' => 'ajax',
          '#commands' => array(
          ajax_command_replace("#dropdown_second_replace", render($form['dropdown_second'])),
          ajax_command_replace("#dropdown_third_replace", render($form['dropdown_third']))
          )
        );

  }

```


## Third - Setting up Facebook Page Plugin for every node on Drupal



**Context:** I'm trying to set up the Drupal 7 Facebook Page Plugin module, and everything ok, it works fine.

**Problem:** I don't need a unique widget for the whole website. I need N Facebook PagePlugin widgets to display within M nodes of a specific content type. I have a content type "companies" and I need that in each profile created of company, the configuration of this widget is available in a segmented way. (1 node "company" = 1 facebook page plugin widget).

**Questions:** Is there any way to do this for each node? Does each node show a block from facebook page plugin configured ad-hoc? Code? Site-building?


**Limitations:** Each client of this website will be the drupal editor user who creates their company profile. It is not intuitive to ask them to generate the code and insert it. Best to do it automatically: request the link to Facebook page through a field in the form and then insert it into the widget settings, Yes, that's the way.


### Results: Ok, mission accomplished. I only used six (or seven :-P) parts:

1. First, I've created a new text field for the content type called facebook_direction.

2. Second, I got the facebook script given by Facebook Developers.

3. Third, I wrote some PHP code for get the info about the current node and processing the field values asociated to the node.

4. Fourth, I've changed the URL values from the snippet with PHP code to get every facebook page from every node.

5. Fifth, I made a new custom block in my Drupal site, called "facebook stream widget".

6. Sixth, I've inserted the code in the custom block body with PHP editor active, marking the block as only for content types of kind "companies".

7. Add: I made sure that only the Facebook widget view is created yes and only if the node has completed its field facebook direction. Otherwise, the widget is not generated (line if (!is_null($direction)) ), so it does not show it empty.

**Here is the code and works fine:**

```javascript
<div id="fb-root"></div>
<script>(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/es_ES/sdk.js#xfbml=1&version=v2.10";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>

```
```php
<?php

$field = 'field_facebook_direction';
$node = menu_get_object();
$items = field_get_items('node', $node, $field);
$direction = $items[0]['value'];
 if (!is_null($direction)) {
?>
<div class="fb-page" data-adapt-container-width="true" data-hide-
cover="false" data-href="<?php
 echo $direction;?>" data-show-facepile="false" data-small-
header="false" data-tabs="timeline">

<blockquote cite="<?php echo $direction;?>" class="fb-xfbml-parse-
ignore">
<a href="<?php echo $direction;?>">Facebook  Stream</a></blockquote>
</div>

<?php
}

?>
```
