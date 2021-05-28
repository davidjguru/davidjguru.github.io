---
layout: post  
title: 'Drupal Tips: Prefilling fields in forms'
permalink: /blog/drupal-tips-prefilling-fields-in-forms  
published: true  
date: 2019-09-27
author: davidjguru  
categories: [Drupal Tips]  
sitemap: true
---
| ![Picture from Unsplash, by @chadwiq]({{ site.baseurl }}/images/davidjguru_drupal_8_prefilling_fields_in_forms.jpeg) |
|:--:|
| *Picture from Unsplash, user Chad Walton @chadwiq* |


Today I would like to play a little with the forms in Drupal 8
, taking advantage of them and walking around interesting concepts such as
 the injection of services (and services by themselves) or dynamic queries to
  database. That's why today I'm thinking about a short and didactic
   exercise that integrates everything. If you are interested in Drupal
    8 forms, the services and the Database API, read on...
<!--more-->

---------------------------------------------------------------------------------------
<!-- /TOC -->
**This article is part of a series of posts about Drupal Tips.**

[1- Drupal Fast Tips (I) - Using links in Drupal 8](https://davidjguru.github.io/blog/drupal-fast-tips-using-links-in-drupal-8)  
[2- Drupal Fast Tips (II) - Prefilling fields in forms](https://davidjguru.github.io/blog/drupal-fast-tips-prefilling-fields-in-forms)  
[3- Drupal Fast Tips (III) - The Magic of '#attached'](https://davidjguru.github.io/blog/drupal-fast-tips-the-magic-of-attached)  
[4- Drupal Fast Tips (IV) - Xdebug, DDEV and Postman](https://davidjguru.github.io/blog/drupal-fast-tips-xdebug-ddev-and-postman)  
[5- Drupal Fast Tips (V) - Placing a block by code](https://davidjguru.github.io/blog/drupal-fast-tips-placing-a-block-by-code)  
[6- Drupal Fast Tips (VI) - From Arrays to HTML](https://davidjguru.github.io/blog/drupal-fast-tips-from-array-to-html)  
[7- Drupal Fast Tips (VII) - Link Fields from Twig](https://davidjguru.github.io/blog/drupal-fast-tips-link-fields-from-twig)  
<!-- /TOC -->

------------------------------------------------------------------------------------------------

## Introduction 
The first step is to decide what kind of forms we want to build. In Drupal 8, there are -basically- three types of forms that, in turn, are constructed from three specific classes: 

1- **Basic Form**: a normal form of general purpose, adaptable. Created from
 the FormBase Class in Drupal API.  
 [Class FormBase.php](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21FormBase.php/class/FormBase/8.7.x)  
 
2- **Config Form**: a form of specific use to establish an object and
 configuration values.  Created from the ConfigFormBase in Drupal API.
 [Class ConfigFormBase.php](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfigFormBase.php/class/ConfigFormBase/8.7.x)  
 
3- **Confirm Form**: a form to request confirmation from the user before
 executing an irreversible action. Created from the ConfigFormBase in Drupal
  API. [Class ConfirmFormBase.php](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfirmFormBase.php/class/ConfirmFormBase/8.7.x)

In this case, we will opt for **a form created as Basic Form**, more adaptable and elastic for general purposes. We will create a new custom module for Drupal 8, and in its /src/Form route we will include our test form. 
**For the nex trick, I'll use a new Drupal instance**, deployed with the more-than-interesting tool [DDEV](https://ddev.readthedocs.io/en/stable/).  
 
 ```bash
mkdir d8deploy8 && cd d8deploy8
ddev config --project-type php --php-version 7.3 
ddev composer create drupal-composer/drupal-project:8.x-dev --stability dev --no-interaction
ddev config --project-type drupal8
ddev exec drush site-install standard --site-name=My Drupal Website --account-name=admin --account-pass=admin --account-mail=mail@example.com --yes
ddev start
```
 
 **Note:** If you want to know more about how to use ddev, I recommend you
  this article about the tool: ["Development environments for Drupal with DDEV"](https://davidjguru.github.io/blog/creating-development-environments-for-drupal-with-ddev) or this related cheatsheet: ["Docker, Docker
  -Compose and DDEV - Cheatsheet"](https://davidjguru.github.io/blog/containers-docker-docker-compose-ddev-cheatsheet).  
  
  Okay, now let's look at a little sketch of the form we're planning to build:

![Creating custom basic Forms in Drupal 8]({{ site.baseurl }}/images/davidjguru_8_my_drupal_website_form.png)

We will see the code in the next section. 
## Building our Form

First of all, **we have to build a basic module structure** for our custom Form
. We can build the module using [Drupal Console](https://drupalconsole.com/docs/en) from your prompt with the
 generate:module option [(drupal generate:module)](https://hechoendrupal.gitbooks.io/drupal-console/en/commands/generate-module.html). 

```bash
drupal generate:module
drupal gm
```
Or you can use Drupal Console with parameters:
```bash
drupal generate:module \
--module="My Random module" \
--machine-name="my_random_module" \
--module-path="modules/custom" \
--description="This is a random generated custom module" \
--core="8.x" \
--package="Custom" \
--module-file \
--no-interaction
```
Then, **we'll need build an initial basic form**, and for that we can use (one
 more time) the Drupal Console and its functionalities related to scaffolding
  generation. In this case we will use the options associated with "_generate
  :form_" [(drupal generate:form)](https://hechoendrupal.gitbooks.io/drupal-console/en/commands/generate-form.html).   
  Taking the former module as reference: 
  
```bash
drupal generate:form
drupal gf
```
Or with params: 

```bash
drupal generate:form  \
--module="my_random_module"  \
--class="RandomClassForm"  \
--form-id="default_random_form"  \
--config-file  \
--inputs='"name":"name", "type":"textfield", "label":"Name", "options":"", "description":"User Name", "maxlength":"64", "size":"", "default_value":"", "weight":"0", "fieldset":""'  \
--inputs='"name":"id_user", "type":"number", "label":"User ID", "options":"", "description":"User ID", "maxlength":"64", "size":"", "default_value":"", "weight":"1", "fieldset":""'  \
--inputs='"name":"email", "type":"email", "label":"Email", "options":"", "description":"User email", "maxlength":"", "size":"", "default_value":"", "weight":"2", "fieldset":""'  \
--inputs='"name":"number_comments", "type":"number", "label":"Number of Comments", "options":"", "description":"Number of Coments", "maxlength":"", "size":"", "default_value":"", "weight":"3", "fieldset":""'  \
--inputs='"name":"types", "type":"checkboxes", "label":"Content Types", "options":"['1' => '1']", "description":"Select Content Types", "maxlength":"", "size":"", "default_value":"1", "weight":"4", "fieldset":""' \
--path="/my_random_module/forms/random_form" \
--no-interaction
```
We don't worry about the submit, **the Drupal Console will process 
 automatically the inputs and it will generate a submit button**. Just after a few
  small adjustments
  in the form at code level -such as assigning a weight to the Submit button
   (_'#weight' => 5,_)- we will already have the new module and its form
    available.Install the module and clear cache, accessing the route we
     have defined and we have it available.
     
```bash
ddev exec drush en my_random_module
ddev exec drush cr
```
Et voilá! in:
 ```bash 
 http://d8deploy8.ddev.local/my_random_module/forms/random_form 
```   
   we will have our new custom form:   
    
![Custom form in Drupal 8 created with Drupal Console]({{ site.baseurl }}/images/davidjguru_drupal_8_custom_form.png)



## Filling fields in our Form

### Inyecting services in Drupal 8
But as we want to play a little with services for filling fields, we'll use
 the options that allow us to load from Drupal Console the different services -[Services and dependency injection in Drupal 8](https://www.drupal.org/docs/8/api/services-and-dependency-injection/services-and-dependency-injection-in-drupal-8)-
  by injection **from the corresponding Services container**, simply by doing this:
  
```bash
drupal generate:form  \
--module="my_random_module"  \
--class="RandomClassForm"  \
--form-id="default_random_form"  \
--config-file  \
--inputs='"name":"name", "type":"textfield", "label":"Name", "options":"", "description":"User Name", "maxlength":"64", "size":"", "default_value":"", "weight":"0", "fieldset":""'  \
--inputs='"name":"id_user", "type":"number", "label":"User ID", "options":"", "description":"User ID", "maxlength":"64", "size":"", "default_value":"", "weight":"1", "fieldset":""'  \
--inputs='"name":"email", "type":"email", "label":"Email", "options":"", "description":"User email", "maxlength":"", "size":"", "default_value":"", "weight":"2", "fieldset":""'  \
--inputs='"name":"number_comments", "type":"number", "label":"Number of Comments", "options":"", "description":"Number of Coments", "maxlength":"", "size":"", "default_value":"", "weight":"3", "fieldset":""'  \
--inputs='"name":"types", "type":"checkboxes", "label":"Content Types", "options":"['1' => '1']", "description":"Select Content Types", "maxlength":"", "size":"", "default_value":"1", "weight":"4", "fieldset":""' \
--path="/my_random_module/forms/random_form" \
--services="database" \
--services="current_user" \
--services="email.validator" \
--no-interaction
```
So we can inject services through Drupal Console using the last lines:
```bash
--services="database" 
--services="current_user" 
--services="email.validator"
```
And it will include the corresponding services available in the class:

```php
 /**
   * Constructs a new RandomClassForm object.
   */
  public function __construct(
    Connection $database,
    AccountProxyInterface $current_user,
    EmailValidatorInterface $email_validator
  ) {
    $this->database = $database;
    $this->current_user = $current_user;
    $this->email_validator = $email_validator;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    return new static(
      $container->get('database'),
      $container->get('current_user'),
      $container->get('email.validator')
    );
  }
```

Let's see the method buildForm() generated by Drupal Console in
 RandomClassForm.php:

```php
public function buildForm(array $form, FormStateInterface $form_state) {
    $form['name'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Name'),
      '#description' => $this->t('User Name'),
      '#maxlength' => 64,
      '#weight' => 0,
    ];
    $form['id_user'] = [
      '#type' => 'number',
      '#title' => $this->t('User ID'),
      '#description' => $this->t('User ID'),
      '#maxlength' => 64,
      '#weight' => 1,
    ];
    $form['email'] = [
      '#type' => 'email',
      '#title' => $this->t('Email'),
      '#description' => $this->t('User email'),
      '#weight' => 2,
    ];
    $form['number_comments'] = [
      '#type' => 'number',
      '#title' => $this->t('Number of Comments'),
      '#description' => $this->t('Number of Coments'),
      '#weight' => 3,
    ];
    $form['types'] = [
      '#type' => 'checkboxes',
      '#title' => $this->t('Content Types'),
      '#description' => $this->t('Select Content Types'),
      '#options' => [1 => '1'],
      '#default_value' => 1,
      '#weight' => 4,
    ];

    $form['submit'] = [
      '#type' => 'submit',
      '#value' => $this->t('Submit'),
      '#weight' => 5,
    ];

    return $form;
  }
```

So now, we can use the injected services to load values in some fields. 

**User Name**  
To load the registered name, we can use two ways for two differentes values
: first we can use [the getAccountName() function](https://api.drupal.org/api
/drupal/core%21lib%21Drupal%21Core%21Session%21AccountProxy.php/function/AccountProxy%3A%3AgetAccountName/8.7.x) provided by the current_user service
, wich returns the login name of an account. Then we can use [the
 getDisplayName()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Session%21AccountProxy.php/function/AccountProxy%3A%3AgetDisplayName/8.7.x) function too, that  shows the Display name of an account. 
 
 Differences? the first function will show no value in case of an anonymous user (not registered, not logged in) and the second function will load an "Anonymous" value. So we will use the second option. 
 
 
```php
$form['name'] = [
      '#type' => 'textfield',
      '#value' => $this->current_user->getDisplayName(),
      '#title' => $this->t('Name'),
      '#description' => $this->t('User Name'),
      '#maxlength' => 64,
      '#weight' => 0,
    ];
```

**User ID**  
In order to get the ID of the current User, we'll use the current_user
 service loaded by Drupal Console as in the former example. This service use
  [the AccountProxy class
  from Drupal 8](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Session%21AccountProxy.php/class/AccountProxy/8.7.x), which implements
   [the AccountProxyInterface](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Session%21AccountProxyInterface.php/interface/AccountProxyInterface/8.7.x)
   and handles values from the current user as we made in the former example
    (User Name), in this case with
   the id method returning the number of the user in the system (id = 0 if it
   's an anonymous user). Using: '#value' => $this->currentUser->id().
```php 
$form['id_user'] = [
      '#type' => 'number',
      '#value' => $this->current_user->id(),
      '#title' => $this->t('User ID'),
      '#description' => $this->t('User ID'),
      '#maxlength' => 64,
      '#weight' => '1',
    ];
```

**User email**  
To load the email we will also use a function of the current_user service
 called getEmail(). 
 
```php 
$form['email'] = [
      '#type' => 'email',
      '#value' => $this->current_user->getEmail(),
      '#title' => $this->t('Email'),
      '#description' => $this->t('User email'),
      '#weight' => '2',
    ];
```
Furthermore, as we are not sure that we can trust the validation of the email to the HTML5 of the browser, we add the ad-hoc validation service in the form validation method. 
```php
/**
  * {@inheritdoc}
  */
public function validateForm(array &$form, FormStateInterface $form_state) {

  // Get the email value from the field.
  $mail = $form_state->getValue('email');

  // Test the format of the email. 
  if(!$this->email_validator->isValid($mail)) {
    $form_state->setErrorByName('email', $this->t('The %email is not valid email.',
                                ['%email' => $mail]));
   }
}
```

### Dynamyc Queries with Database API
So ok, we've already used at least two of the services we'd already requested from Drupal Console. Now we'll use the third one for a database query, but first, let's load some test values. 

Using the command lines, we'll install the devel module for Drupal 8 and
 using the devel_generate submodule we'll create a set of ten nodes with type
  'article' with random values for comments in nodes. This will be useful in
   the database query that we're going to generate. 

```bash
ddev exec composer require drupal/devel
ddev exec drush en devel devel_generate
ddev exec drush genc 10 5 --types=article
```
**Number of Comments**  
In the three previous instructions we are loading the devel module through composer from the dockerized environment with ddev (first line). 
Then we've activate the devel module and its submodule devel_generate through
 Drush (second line) and finally we ask for the creation of 10 nodes of type "Article" with a number of comments per random node between zero and five comments (in the third line). 
 
 Now we're going to set the next value for the field 'number_comments', from
  a dynamic query to the database. We want to count the number of comments
   associated with the current user. So if we want to get the number, we'll
    build a SELECT statement using SELECT COUNT(*). For this, we'll use [the
     countQuery() method from the Drupal Database API](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Database%21Query%21Select.php/function/Select%3A%3AcountQuery/8.7.x). 
     
The countQuery() method gives help returning a sentence from a statement
 $query. If we execute it, will return us the number of the results.
   
 ```php 
// Build the base query.
$query = $this->database->select('comment_field_data', 'c')
         ->fields('c')
         ->condition('c.uid', $this->currentUser->id(), '=');

// Get the number of registers.
$query_counter = $query->countQuery();
$result = $query_counter->execute();
$count = $result->fetchField();
```
So we have load the count value in the field:

```php
$form['number_comments'] = [
      '#type' => 'number',
      '#value' => $count,
      '#title' => $this->t('Number of Comments'),
      '#description' => $this->t('Number of Coments'),
      '#weight' => 3,
    ];

```
**Content Types**  
Now, we're going to set the last value in our custom form, using an specific
 query to the database through the standard Drupal API, although we can also
  use a function from the Node module, within the core of Drupal. I'm talking
   about the [node_type_get_function](https://api.drupal.org/api/drupal/core%21modules%21node%21node.module/function/node_type_get_names/8.8.x).
 
   We want to set as
  checkboxes options all the available content types in our Drupal site. 
  What can we do? We'll use the former function and then, we'll take the keys of the returned array, setting all as default options. Let's see:

```php
  $options = node_type_get_names();
  $defaults = array_keys($options); 
```
```php
$form['types'] = [
      '#type' => 'checkboxes',
      '#title' => $this->t('Content Types'),
      '#description' => $this->t('Select Content Types'),
      '#options' =>   $options,
      '#default_value' => $defaults,
      '#weight' => '4',
    ];
```
Do we have all our fields ready yet?
 Well, if we reload our form (after uninstalling and reinstalling the module), we see that there is nothing in the email field when the user is anonymous... What do we do?

We will protect our form, preventing the sending if you are not a registered user and also hide the email field. **How?** We will ask if the user is registered through our service $current_user and from there, we will decide to show some fields or others thanks to a basic structure if-else. Remember that we are inside a PHP class and therefore, we can use the control structures we need. 

We will pass a couple of fields (the email and Submit itself) to the user's registration confirmation:

```php 
if(!$this->current_user->isAuthenticated()) {
      $form['help'] = [
            '#type' => 'item',
            '#title' => $this->t('Please, read the conditions.'),
            '#markup' => $this->t('<strong>Only registered users can send info.</strong>'),
      ];

    }else {
       $form['email'] = [
             '#type' => 'email',
             '#value' => $this->current_user->getEmail(),
             '#title' => $this->t('Email'),
             '#description' => $this->t('User email'),
             '#weight' => '2',
    ];
      $form['submit'] = [
            '#type' => 'submit',
            '#value' => $this->t('Submit'),
            '#weight' => 5,
          ];
    }
```

And this is the result in screen for registered and unregistered users:

![Result form with prefilling]({{ site.baseurl }}/images/davidjguru_drupal_8_result_form_with_prefilling.png)

###  The final version of the class
And here we can see the final look of the class...we can do something with the submit method...maybe it's a good idea for a upcomming article...who knows :-) 
```php
<?php

namespace Drupal\my_random_module\Form;

use Drupal\Core\Form\FormBase;
use Drupal\Core\Form\FormStateInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Drupal\Core\Database\Driver\mysql\Connection;
use Drupal\Core\Session\AccountProxyInterface;
use Drupal\Component\Utility\EmailValidatorInterface;

/**
 * Class RandomClassForm.
 */
class RandomClassForm extends FormBase {

  /**
   * Drupal\Core\Database\Driver\mysql\Connection definition.
   *
   * @var \Drupal\Core\Database\Driver\mysql\Connection
   */
  protected $database;

  /**
   * Drupal\Core\Session\AccountProxyInterface definition.
   *
   * @var \Drupal\Core\Session\AccountProxyInterface
   */
  protected $currentUser;

  /**
   * Drupal\Component\Utility\EmailValidatorInterface definition.
   *
   * @var \Drupal\Component\Utility\EmailValidatorInterface
   */
  protected $emailValidator;

  /**
   * Constructs a new RandomClassForm object.
   */
  public function __construct(
    Connection $database,
    AccountProxyInterface $current_user,
    EmailValidatorInterface $email_validator
  ) {
    $this->database = $database;
    $this->current_user = $current_user;
    $this->email_validator = $email_validator;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    return new static(
      $container->get('database'),
      $container->get('current_user'),
      $container->get('email.validator')
    );
  }

  /**
   * {@inheritdoc}
   */
  public function getFormId() {
    return 'default_random_form';
  }

  /**
   * {@inheritdoc}
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    // Build the base query.
    $query = $this->database->select('comment_field_data', 'c')
      ->fields('c')
      ->condition('c.uid', $this->current_user->id(), '=');

    // Get the number of registers.
    $query_counter = $query->countQuery();
    $result = $query_counter->execute();
    $count = $result->fetchField();

    // Get the Content Types and its keys.
    $options = node_type_get_names();
    $defaults = array_keys($options);

    // Building the form.
    $form['name'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Name'),
      '#value' => $this->current_user->getDisplayName(),
      '#description' => $this->t('User Name'),
      '#maxlength' => 64,
    ];
    $form['id_user'] = [
      '#type' => 'number',
      '#value' => $this->current_user->id(),
      '#title' => $this->t('User ID'),
      '#description' => $this->t('User ID'),
      '#maxlength' => 64,
      '#weight' => '1',
    ];
    $form['number_comments'] = [
      '#type' => 'number',
      '#value' => $count,
      '#title' => $this->t('Number of Comments'),
      '#description' => $this->t('Number of Comments'),
      '#weight' => '3',
    ];
    $form['types'] = [
      '#type' => 'checkboxes',
      '#title' => $this->t('Content Types'),
      '#description' => $this->t('Select Content Types'),
      '#options' =>   $options,
      '#default_value' => $defaults,
      '#weight' => '4',
    ];

    // Testing if the user is logged or not.
    if(!$this->current_user->isAuthenticated()) {
      $form['help'] = [
        '#type' => 'item',
        '#title' => $this->t('Please, read the conditions.'),
        '#markup' => $this->t('<strong>Only registered users can send info.</strong>'),
        '#weight' => 5,
      ];

    }else {
      $form['email'] = [
        '#type' => 'email',
        '#value' => $this->current_user->getEmail(),
        '#title' => $this->t('Email'),
        '#description' => $this->t('User email'),
        '#weight' => '2',
      ];
      $form['submit'] = [
        '#type' => 'submit',
        '#value' => $this->t('Submit'),
        '#weight' => 5,
      ];
    }

    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function validateForm(array &$form, FormStateInterface $form_state) {
    // Get the email value from the field.
    $mail = $form_state->getValue('email');

    // Test the format of the email.
    if(!$this->email_validator->isValid($mail)) {
      $form_state->setErrorByName('email', $this->t('The %email is not valid email.',
        ['%email' => $mail]));
    }
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    // Display result.
    foreach ($form_state->getValues() as $key => $value) {
      \Drupal::messenger()->addMessage($key . ': ' . ($key === 'text_format'?$value['value']:$value));
    }
  }

}
```
**And at last,** you can download or clone the module from my Gitlab repo, just here:
[https://gitlab.com/davidjguru/drupal-custom-modules-examples](https://gitlab.com/davidjguru/drupal-custom-modules-examples), as "my_random_module". Greetings!
## :wq!
