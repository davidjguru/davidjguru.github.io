---
layout: post
title: Drupal Fast Tips (VII) - Link fields from Twig  
permalink: /blog/drupal-fast-tips-link-fields-from-twig
published: true
date: 2021-04-30
author: davidjguru
categories: [Drupal & Coding]
sitemap: true
youtubeId: 6O192OAzMH8
---

| ![Picture from Unsplash, by @lazycreekimages]({{ site.baseurl }}/images/davidjguru_drupal_8_9_link_fields_from_twig_main.png) |
|:--:|
| *Picture from Unsplash, user [Michael Dziedzic, @lazycreekimages](https://unsplash.com/@lazycreekimages)* |  

In this new issue of the Drupal Fast Tips I would like to share some basic tips and examples related with the Drupal Link field and how to get the data from its sub-fields (title and link) to rendering in a Twig template. Sometimes we need render values from a link field in a structured way through a Twig template, and depending on the location of the Link field, this may have a different articulation.  
<!--more-->

Because of this, I wanted to show some small examples of extracting and handling the values of this particular type of fields. This will be a post for site-builders or developers with basic knowledge on the Drupal backend (yes, Twig and the rendering are in the backend, sorry.).  

---------------------------------------------------------------------------------------
<!-- /TOC -->
**This article is part of a series of posts about Drupal Tips.**

[1- Drupal Fast Tips (I) - Using links in Drupal 8](https://davidjguru.github.io/blog/drupal-fast-tips-using-links-in-drupal-8)  
[2- Drupal Fast Tips (II) - Prefilling fields in forms](https://davidjguru.github.io/blog/drupal-fast-tips-prefilling-fields-in-forms)  
[3- Drupal Fast Tips (III) - The Magic of '#attached'](https://davidjguru.github.io/blog/drupal-fast-tips-the-magic-of-attached)  
[4- Drupal Fast Tips (IV) - Xdebug, DDEV and Postman](https://davidjguru.github.io/blog/xdebug-ddev-and-postman)  
[5- Drupal Fast Tips (V) - Placing a block by code](https://davidjguru.github.io/blog/drupal-fast-tips-placing-a-block-by-code)  
[6- Drupal Fast Tips (VI) - From Arrays to HTML](https://davidjguru.github.io/blog/drupal-fast-tips-from-array-to-html)  
[7- Drupal Fast Tips (VII) - Link Fields from Twig](https://davidjguru.github.io/blog/drupal-fast-tips-link-fields-from-twig)  
<!-- /TOC -->

------------------------------------------------------------------------------------------------

## What are Condition Plugins? 

Although there is not a lot of information on this subject, nor does it form part of the documented APIs of Drupal, We can assemble interpretative pieces about how this small extensible sub-system based on Drupal Plugins works.  
Basically, we can say that Condition Plugins are an extensible way to generate new visibility conditions for Blocks in Drupal, based in the format of Plugins that you can implement following the Drupal rules for Plugins.  

The Condition Plugins are context-aware (many of them requires explicit context in its annotations blocks), but let's see some information located within the Drupal documentation:  

From the [Condition Plugin System](https://www.drupal.org/node/1961370), as was initially described in 2013: 

> _"To implement a condition in a module create a class in {module}/src/Plugin/Condition/{ConditionName}.php and extend ConditionPluginBase (which implements ConditionInterface). The class must also declare a plugin annotation in its docblock comment."_  
> 

And about the Contexts, from the [Plugin Contexts Definition](https://www.drupal.org/docs/drupal-apis/plugin-api/plugin-contexts):  

> _"Sometimes plugins require another object in order to perform their primary operation. This is known as plugin context. Using a practical example, almost all condition plugins require a context. Let's look at the NodeType condition's plugin definition [...] The context_definitions key stores an array of named context definitions the condition requires in order to perform its "evaluate()" method."_  

Actually, the Condition Plugin it's not a complicated concept, but it's true that you need to know quite well some basic previous steps in order to reach here the [Satori](https://en.wikipedia.org/wiki/Satori). These are key concepts for Drupal Development and maybe you'll need understand in deep the next topics:  
* [Creating Custom Modules in Drupal](https://www.drupal.org/docs/creating-custom-modules).  
* [The Plugin API in Drupal](https://www.drupal.org/docs/drupal-apis/plugin-api).  
* [Annotations Based Plugins in Drupal](https://www.drupal.org/docs/drupal-apis/plugin-api/annotations-based-plugins).  
* [Guide: How to integrate JavaScript in Drupal 8-9](https://www.therussianlullaby.com/blog/guide-how-to-integrate-javascript-in-drupal-8-9/)  

## Existing Condition Plugins in your Drupal Installation  

You can discover some existing Condition Plugins available in your Drupal installation from different core modules:   

**Current Condition Plugins in Core:**

* [NodeType Condition Class](https://api.drupal.org/api/drupal/core%21modules%21node%21src%21Plugin%21Condition%21NodeType.php/class/NodeType/9.2.x): namespace Drupal\node\Plugin\Condition, in core/modules/node  
* [RequestPath Condition Class](https://api.drupal.org/api/drupal/core%21modules%21system%21src%21Plugin%21Condition%21RequestPath.php/class/RequestPath/9.2.x): namespace Drupal\system\Plugin\Condition, in core/modules/system  
* [UserRole Condition Class](https://api.drupal.org/api/drupal/core%21modules%21user%21src%21Plugin%21Condition%21UserRole.php/class/UserRole/9.2.x): namespace Drupal\user\Plugin\Condition, in core/modules/user  

These previous Plugins are the three basic items availables by default in a Block Visibility Configuration, I mean:  

![Condition Classes related to basic visibility options]({{ site.baseurl }}/images/davidjguru_drupal_8_9_condition_plugins_for_visibility_2.png)  

But there are a few more in other locations (in addition to some used in test classes):  

* [CurrentTheme Condition Class](https://api.drupal.org/api/drupal/core%21modules%21system%21src%21Plugin%21Condition%21CurrentThemeCondition.php/class/CurrentThemeCondition/9.2.x): namespace Drupal\system\Plugin\Condition, in core/modules/system  
* [Language Condition Class](https://api.drupal.org/api/drupal/core%21modules%21language%21src%21Plugin%21Condition%21Language.php/class/Language/9.2.x): namespace Drupal\language\Plugin\Condition, in core/modules/language  
  
**And the central Interface for Conditions:**  
* [ConditionInterface](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Condition%21ConditionInterface.php/interface/ConditionInterface/9.2.x): namespace Drupal\Core\Condition, in core/lib/Drupal/Core/Condition  

## Available Condition Plugins in Drupal Contrib Modules  

As in Drupal Core you can find Condition Plugins in Contrib modules too. For instance, there are some modules developer by [Cambrico](https://www.drupal.org/cambrico) with many resources within:   

* [Drupal Module Condition Plugins](https://www.drupal.org/project/condition_plugins)  

* [Drupal Module Condition Plugins Commerce](https://www.drupal.org/project/condition_plugins_commerce)  

And one of the biggest contrib modules of Drupal -[Webform](https://www.drupal.org/project/webform)- also provides its own Plugin for Conditions:  
* [Webform Condition](https://git.drupalcode.org/project/webform/-/blob/6.x/src/Plugin/Condition/Webform.php)  

## Building your own Condition Plugin 

Ok, now let's go to build something useful. We already know conceptually what they are, what they do and where cand find them. So is the time to implement Condition Plugins!.  

### Our Goals
For this case, I have come up with a very simple and straightforward idea:  
We want to show a block only for nodes of type "Article" when they have checked a field. 

In order to prepare this, I made the quickest way:  
* First I added a new field for the content type "Article", the new "field_selected_article_check".  
* Then I created a new custom block "My Block" in the most easy way, just clicking in /block/add.  
* Finally I implemented scaffolding for a new custom module, called "visibility_conditions"  


![Placing Block and new field for the example]({{ site.baseurl }}/images/davidjguru_drupal_8_9_condition_plugins_for_visibility_3.png)  

We're going to make a new folder in path:  
`/modules/custom/visibility_conditions/src/Plugin/Condition/` 

and creating the new file `SelectedArticle.php` This will be the new Plugin class and will have all the basic resources. Let's see.  
### Annotations 

In order to create a new vertical tab for the block configuration page, we hace to register our new Plugin, and in Drupal the Plugins uses annotations to register themselves and provide all the relevant information for indexing. For instance:  

```
/**
 * Provides a condition for articles marked as selected.
 *
 * This condition evaluates to TRUE when is in a node context, and the node is 
 * Article content type and the article was marked as selected article in a field.
 *
 * @Condition(
 *   id = "selected_article",
 *   label = @Translation("Selected Article"),
 *   context_definitions = {
 *     "node" = @ContextDefinition("entity:node", label = @Translation("node"))
 *   }
 * )
 * 
 */
```
What about context? Well It's a way to know where is running our resource. Some Drupal's Plugins require context information to operate. In this case, we need to know if our Block is inside a node corpus, or not.  

For the next lines, we have to extend the [ConditionPluginBase Class](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Condition%21ConditionPluginBase.php/class/ConditionPluginBase/9.2.x) and implement the ContainerFactoryPluginInterface Class](https://api.drupal.org/api/drupal/core!lib!Drupal!Core!Plugin!ContainerFactoryPluginInterface.php/interface/ContainerFactoryPluginInterface/9.2.x):  

```
 class SelectedArticle extends ConditionPluginBase implements ContainerFactoryPluginInterface {
```

And this is the most relevant information from the top of our new class (as well as the '<?php' operture tag). Let's continue.  
### Configuration  

Ok, our next step is define the configure by our new Plugin: what values are important and how we go to get these values.  
For the first need, we build a basic method `defaultConfiguration()` from [the mother Class](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Condition%21ConditionPluginBase.php/function/ConditionPluginBase%3A%3AdefaultConfiguration/9.2.x). This will set the initial value of the new field in the tab:  

```
 /**
   * {@inheritdoc}
   */
  public function defaultConfiguration() {
    // This default value will mark the block as hidden.
      return ['show' => 0] + parent::defaultConfiguration();
    }

  /**
   * {@inheritdoc}
   */
  public function buildConfigurationForm(array $form, FormStateInterface $form_state) {
    // Build a checkbox to expose the new condition.
    $form['show'] = [
      '#title' => $this->t('Display only in Selected Articles'),
      '#type' => 'checkbox',
      // Is using the previous config value as the default.
      '#default_value' => $this->configuration['show'],
      '#description' => $this->t('If this box is checked, this block will only be shown for Selected Articles.'),
      ];
     
      return parent::buildConfigurationForm($form, $form_state);
    }
```

And then we call to the submit function:  

```
  /**
   * {@inheritdoc}
   */
  public function submitConfigurationForm(array &$form, FormStateInterface $form_state) {
    // Save the selected value to configuration.
    $this->configuration['show'] = $form_state->getValue('show'); 
      parent::submitConfigurationForm($form, $form_state);
    }
```

After that, we've built an extension for the Configuration Form of the visibility general block, adding a new item called `show` which contains the new element, a checkbox for select our new condition (or not).  

### Evaluate 

Now we need a way for measure if our new condition applies or not and we're using a function inherited from the mother class in order to evaluate the condition, returning TRUE or FALSE. It's from ConditionBase Class, which implements ConditionInterface:  

```
  /**
   * {@inheritdoc}
   */
  public function evaluate() {
    // First ensure that doesn't disable other blocks aren't using it.
    if (empty($this->configuration['show']) && !$this->isNegated()) {
      return TRUE;
    }
    
    // Gets the context value.
    $node = $this->getContextValue('node');

    // Then review if the node Article has setted the Selected Article field.
    if (($node->getType() == "article") && ($node->hasField('field_selected_article_check')) && ($node->field_selected_article_check->value)) {
      return TRUE;
    }
      // Finally if not exist marked value in Selected Article field.
      return FALSE;
    }
```
As you can see in the block above, we're using a first part to ensure that if none of the above fields are checked, TRUE is returned and the block is displayed. This will ensure that by default if we have not activated the visibility of the block, we have it visible in general terms until we limit its visibility.  


### Summary

One more time, we have a function available from the mother class ConditionBase and required by ConditionInterface, just a method for expose descriptions about the works of the Conditions. These descriptions will not be available from the vertical tabs of the block configuration page, but will be only available from code.  

```
  /**
   * {@inheritdoc}
   */
  public function summary() {
    // We have to check three options: 
    // 1- Condition enabled.
    // 2- Condition enabled and negated.
    // 3- Condition not enabled.
    if ($this->configuration['show']) {
      // Check if the 'negate condition' checkbox was enabled.
      if ($this->isNegated()) {
        // The condition is enabled and negated.
        return $this->t('The block will be shown in all Articles except the Selected.');
      } else {
        // The condition is only enabled.
        return $this->t('The block will be shown only in Selected Articles.');
      }
    }
      // The condition is not enabled.
      return $this->t('The block will be shown in all Articles.');
    }
```

### Some Futher Details  

In the previous section, we took care of preparing a Summary, but this is only available at code level, it's not visible in GUI. Could we improve the usability of our new element by adding this summary? Yes, it's possible and for this we can rely on a jQuery function in Drupal (sorry).  

I introduce you the function "drupalsetSummary", I discovered it consulting problems [in StackExchange](https://drupal.stackexchange.com/a/252723/94320) and then I identified it in real cases like [the JavaScript added for Nodes in the Drupal core](https://git.drupalcode.org/project/drupal/blob/HEAD/core/modules/node/node.js#L12). I haven't found much information about it, so I can't explain much more than this: "it serves to place summaries" ¯\_(ツ)_/¯ . Well, ok.  

The important thing is that I was playing with this function and it seems to work well for inserting elements. The pre-conditions are that JavaScript must be added to our custom module and that we must use the Drupal Behaviors format. Please review [this Drupal - JavaScript integration guide: Drupal Behaviors](https://www.therussianlullaby.com/blog/guide-how-to-integrate-javascript-in-drupal-8-9/#6--drupal-behaviors) in order to get more info about the Drupal Behaviors format and how implementing it, and here from [this github gist you can play with some examples of Drupal Behaviors](https://gist.github.com/davidjguru/d1df8edca861258b55b72c8659fb88a7).  


visibility_conditions.libraries.yml:  
```
block_special_articles: 
  js: 
    js/selected_articles_conditions.js: {}
  dependencies: 
    - block/drupal.block
```


The Drupal Behavior:  

```
  /**
   * Provide the summary information for the block settings vertical tabs.
   *
   */
  Drupal.behaviors.blockSettingsSummarySelectedArticles = {
    attach: function () {
      // Check if the function drupalSetSummary is available.
      if (jQuery.fn.drupalSetSummary !== undefined) {
        // Add the summary on tab.
        $('[data-drupal-selector="edit-visibility-selected-article"]').drupalSetSummary(checkSummary);
      }
    }
  };
```

And the function for setting the strings of summary:  

```
function checkSummary(context) {
    // Check if the condition has been selected.
    var selectedCondition = $(context).find('[data-drupal-selector="edit-visibility-selected-article-show"]:checked').length;
    // Check if the negate condition has been selected.
    var selectedNegate = $(context).find('[data-drupal-selector="edit-visibility-selected-article-negate"]:checked').length;

    // Reviews scenarios.
    if (selectedCondition) {
      if (selectedNegate) {
        // Condition and Negation were selected.
        return Drupal.t("The block will be shown in all Articles except the Selected.");
      }

      // Only the condition was selected.
      return Drupal.t("The block will be shown only in Selected Articles.");
    }

    // The condition has not been enabled and is not negated.
    return Drupal.t('The block will be shown in all Articles');
  }
``` 
That's all! interesting? it's true that it requires basic knowledge of other Drupal elements (Annotations, Plugins, JavaScript Libraries, Drupal Behaviors...) but in itself, the result is quite interesting. Now we can build our own visibility conditions Plugins!  
 
![Rendering new Visibility Condition Plugin]({{ site.baseurl }}/images/davidjguru_drupal_8_9_condition_plugins_for_visibility_4.gif)
  
I have uploaded this test module to [my custom modules repository for testing, in Gitlab](https://gitlab.com/davidjguru/drupal-custom-modules-examples). You can [download it from here](https://gitlab.com/davidjguru/drupal-custom-modules-examples/-/tree/master/visibility_conditions). And remember, don't use these modules in production environments!  
## :wq!

### Recommended song: Catalina - Rosalía & Refree

{% include youtubePlayer.html id=page.youtubeId %}
