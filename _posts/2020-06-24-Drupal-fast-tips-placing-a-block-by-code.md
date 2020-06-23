---
layout: post
title: Drupal Fast Tips (V) - Placing a block by code
permalink: /blog/drupal-fast-tips-placing-a-block-by-code
published: true
date: 2020-06-24
author: davidjguru
categories: [Drupal Coding]
sitemap: true
---
| ![Picture from Unsplash, by @xavi_cabrera]({{ site.baseurl }}/images/davidjguru_drupal_placing_a_block_by_code_main_1.jpg) |
|:--:|
| *Picture from Unsplash, user [Xavi Cabrera, @xavi_cabrera](https://unsplash.com/@xavi_cabrera)* |

Today I would like to type a little bit about a technical exercise I did the other day: I played to activate and locate Drupal blocks in themes avoiding the site - building, just with code. So I'm going to show some ideas related with Forms, Blocks, Themes and the Config system of Drupal. This will be valid for Drupal 8 and Drupal 9. Let's go.  
<!--more-->

The context of this post is that I was practicing with modules that at the time of installation, are dedicated to building a whole subsystem: in particular I was implementing a small event attendance management from a Drupal installation.  

1. The configuration management system (files that can be in /config/install or in /config/optional, settings or assembly of content types, views, etc in .yml format).  
1. The actions described in a hook_install() // hook_uninstall(), that is, tasks that can be implemented during the installation process of a module.  

In the mechanics I'm thinking, I have modifiable values in configuration files... and I also have a form based on Form API, a new Plugin for a visual block, and everything inside a new custom module. **How can I make the configuration and the code relate right at the time of installation of my module?**

This can be done as Drupal when installing a module, first loads the provisioning configuration it provides and only then executes the actions that are contained in a hook_install(), so when installing we can create resources by code like creating nodes, new entities, taxonomies and from the hook_install() we can populate it with some terms. Then we'll remove the created items from hook_uninstall(), both hooks within the .install file of the module.  

You can see this processing within the code of the install() method from the ModuleInstaller class of Drupal:
[https://api.drupal.org/ModuleInstaller.php/9.0.x](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Extension%21ModuleInstaller.php/function/ModuleInstaller%3A%3Ainstall/9.0.x).  
Where You can see first:  

```php
// Install default configuration of the module.
$config_installer = \Drupal::service('config.installer');
if ($sync_status) {
  $config_installer
  ->setSyncing(TRUE)
  ->setSourceStorage($source_storage);
}
  \Drupal::service('config.installer')
  ->installDefaultConfig('module', $module);
```
And then:

```php
// Allow the module to perform install tasks.
$this->moduleHandler
   ->invoke($module, 'install');
```
So you can think of actions from hook_install() that can use configuration objects installed from your configuration files folder, all on-the-fly. Ok, so I have some config files and as settings.yml I'm loading this (short version):

**File:** managing_activities.settings.yml  
**Path:** managing_activities/config/install/  
```yaml
module_name: 'managing_activities'
region_for_block: 'sidebar_first'
```
Where the values are described in a schema file:  
**File:** managing_activities.schema.yml  
**Path:** managing_activities/config/schema/  
```yaml
managing_activities.settings:
  type: config_object
  label: 'Managing Activities Settings'
  mapping:
    module_name:
      type: string
      label: 'Module Name for references in Hooks.'
    region_for_block:
      type: string
      label: 'Visual Region of your current Theme.'
```
As you can guess, my custom module is called 'managing_activities'. Ok. Next I have a form based in the Form API of Drupal, with some fields, validation steps and actions in submit (Creating new nodes for a new content type migrated by config files). Here is my custom Form (short version, of course):  

**File:** ManagingActivitiesRegisterForm.php  
**Path:** managing_activities/src/Form/  
```php
<?php

namespace Drupal\managing_activities\Form;
[...]
/**
 * Class ManagingActivitiesRegisterForm implements the Managing Activities Register Form.
 *
 * @package Drupal\managing_activities\Form
 * @access public
 * @see \Drupal\Core\Form\FormBase
 */
class ManagingActivitiesRegisterForm extends FormBase {

[...]

  /**
   * @inheritDoc
   */
  public function getFormId() {
    return 'managing_activities_register';
  }

  /**
   * @inheritDoc
   */
  public function buildForm(array $form, FormStateInterface $form_state) {

    // Building the form.
    $form['#prefix'] = '<div id="register_form_wrapper">';
    $form['#suffix'] = '</div>';

    $form['managing_activities_register_about'] = [
      '#type' => 'item',
      '#markup' => $this->t('We will process your request and respond by mail as soon as possible.'),
      '#prefix' => '<div id="register_form_about">',
      '#sufix' => '</div>',
    ];
    
    [...]
    return $form;
    
  }
```

```php
// Getting info about the current active Theme in the Drupal Installation.
  $theme_name = \Drupal::service('theme.manager')->getActiveTheme()->getName();
```