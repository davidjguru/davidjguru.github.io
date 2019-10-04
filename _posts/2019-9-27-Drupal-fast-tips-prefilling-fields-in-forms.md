---
layout: post  
title: Drupal Fast Tips (II) - Prefilling fields in forms  
permalink: /blog/drupal-fast-tips-prefilling-fields-in-forms  
published: true  
date: 2019-09-27  
categories: [Drupal]  
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

## Introduction 
The first step is to decide what kind of forms we want to build. In Drupal 8, there are -basically- three types of forms that, in turn, are constructed from three specific classes: 

1- **Basic Form**: a normal form of general purpose, adaptable. Created from
 the FormBase Class in Drupal API.  
 [https://api.drupal.org/api/drupal/core/FormBase.php](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21FormBase.php/class/FormBase/8.7.x)  
 
2- **Config Form**: a form of specific use to establish an object and
 configuration values.  Created from the ConfigFormBase in Drupal API.
 [https://api.drupal.org/api/drupal/core/ConfigFormBase.php](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfigFormBase.php/class/ConfigFormBase/8.7.x)  
 
3- **Confirm Form**: a form to request confirmation from the user before
 executing an irreversible action. Created from the ConfigFormBase in Drupal
  API. [https://api.drupal.org/api/drupal/core/ConfigFormBase.php](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Form%21ConfigFormBase.php/class/ConfigFormBase/8.7.x)

In this case, we will opt for a form created as Basic Form, more adaptable and elastic for general purposes. We will create a new custom module for Drupal 8, and in its /src/Form route we will include our test form. 
For the nex trick, I'll use a new Drupal instance, deployed with the
 interesting tool [DDEV](https://ddev.readthedocs.io/en/stable/).  
 
 ```bash
mkdir mydrupalwebsite && cd mydrupalwebsite
ddev config --project-type php --php-version 7.3 
ddev composer create drupal-composer/drupal-project:8.x-dev --stability dev --no-interaction
ddev config --project-type drupal8
ddev exec drush site-install standard --site-name=My Drupal Website --account-name=admin --account-pass=admin --account-mail=mail@example.com --yes
ddev start
```
 
 **Note:** If you want to know more about how to use ddev, I recommend you
  this article about the tool["Development environments for Drupal with DDEV"](https://davidjguru.github.io/blog/creating-development-environments-for-drupal-with-ddev) or this related cheatsheet: ["Docker, Docker
  -Compose and DDEV - Cheatsheet"](https://davidjguru.github.io/blog/containers-docker-docker-compose-ddev-cheatsheet).  
  
  Okay, now let's look at a little sketch of the form we're planning to build:

![Creating custom basic Forms in Drupal 8]({{ site.baseurl }}/images/davidjguru_8_my_drupal_website_form.png)

We will see the code in the next section. 
## Building our Form

First of all, we have to build a basic module structure for our custom Form
. We can build the module using Drupal Console from your prompt with the
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
Then, we'll need build an initial basic form, and for that we can use (one
 more time) the Drupal Console and its functionalities related to scaffolding
  generation. In this case we will use the options associated with "generate
  :form" [(drupal generate:form)](https://hechoendrupal.gitbooks.io/drupal-console/en/commands/generate-form.html).   
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
We don't worry about the submit, the Drupal Console will process 
 automatically the inputs and it will generate a submit button. Just after a few
  small adjustments
  in the form at code level -such as assigning a weight to the Submit button
   ('#weight' => 5,)- we will already have the new module and its form
    available.Install the module and clear cache, accessing the route we
     have defined and we already have it available.
     
```bash
ddev exec drush en my_random_module
ddev exec drush cr
```
Et voilá! in:
 ```bash 
 https://d8deploy8.ddev.local/my_random_module/forms/random_form 
```   
   we will have our new custom form:   
    
![Custom form in Drupal 8 created with Drupal Console]({{ site.baseurl }}/images/davidjguru_drupal_8_custom_form.png)



## Filling fields in our Form

### Inyecting services in Drupal 8
But as we want to play a little with services for filling fields, we'll use
 the options that allow us to load from Drupal Console the different services
  by injection from the corresponding container, simply by doing this:
  
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
    $this->currentUser = $current_user;
    $this->emailValidator = $email_validator;
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
```php
$form['name'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Name'),
      '#description' => $this->t('User Name'),
      '#maxlength' => 64,
    ];
```

**User ID**
```php 
$form['id_user'] = [
      '#type' => 'number',
      '#title' => $this->t('User ID'),
      '#description' => $this->t('User ID'),
      '#maxlength' => 64,
      '#weight' => '1',
    ];
```

**User email**
```php 
$form['email'] = [
      '#type' => 'email',
      '#title' => $this->t('Email'),
      '#description' => $this->t('User email'),
      '#weight' => '2',
    ];
```

### Dynamyc Queries with Database API

###  The final version of the class

```php
<?php

namespace Drupal\forcontu_forms\Form;

use Drupal\Core\Form\FormBase;
use Drupal\Core\Form\FormStateInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Drupal\Core\Session\AccountProxyInterface;

/**
 * Class CurriculumForm.
 */
class CurriculumForm extends FormBase {
  /**
   * Drupal\Core\Session\AccountProxyInterface definition.
   *
   * @var \Drupal\Core\Session\AccountProxyInterface
   */
  protected $currentUser;

  /**
   * Constructs a new SimpleForm object.
   */
  public function __construct(
    AccountProxyInterface $current_user
  ) {
    $this->currentUser = $current_user;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    return new static(
      $container->get('current_user')
    );
  }

  /**
   * Returns a unique string identifying the form.
   *
   * The returned ID should be a unique string that can be a valid PHP function
   * name, since it's used in hook implementation names such as
   * hook_form_FORM_ID_alter().
   *
   * @return string
   *   The unique string identifying the form.
   */
  public function getFormId() {
    return 'curriculum_form';
  }

  /**
   * Form constructor.
   *
   * @param array $form
   *   An associative array containing the structure of the form.
   * @param \Drupal\Core\Form\FormStateInterface $form_state
   *   The current state of the form.
   *
   * @return array
   *   The form structure.
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    // General Structure.
    $form['curriculum'] = [
      '#type' => 'vertical_tabs',
    ];

    // First tab.
    $form['datos_personales'] = [
      '#type' => 'details',
      '#title' => $this->t('Datos Personales'),
      '#weight' => 0,
      '#group' => 'curriculum',
    ];

    $form['datos_personales']['info_personal'] = [
      '#type' => 'details',
      '#title' => $this->t('Información personal'),
      '#description' => $this->t('Grupo de campos de información personal.'),
    ];
    $form['datos_personales']['info_personal']['nombre'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Nombre'),
      '#required' => TRUE,
      '#maxlength' => 20,
      '#size' => 20,
    ];
    $form['datos_personales']['info_personal']['apellidos'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Apellidos'),
      '#required' => TRUE,
      '#maxlength' => 70,
      '#size' => 70,
    ];
    $form['datos_personales']['info_personal']['fec_nac'] = [
      '#type' => 'date',
      '#title' => $this->t('Fecha de Nacimiento'),
      '#required' => TRUE,
      '#default_value' => '2021-01-13',
    ];
    $form['datos_personales']['info_personal']['pais'] = [
      '#type' => 'select',
      '#title' => $this->t('País de nacimiento'),
      '#required' => TRUE,
      '#default_value' => "ES",
      '#options' => [
        "AF" => "Afghanistan",
        ],
      '#description' => t('Seleccione su país de origen, por favor. '),
    ];
    $form['datos_personales']['info_personal']['foto'] = [
      '#type' => 'file',
      '#title' => $this->t('Fotografía personal'),
      '#required' => TRUE,
      '#description' => t('Suba una imagen, formatos permitidos: jpg, png.'),
      '#upload_location' => 'public://managed',
      '#upload_validators' => [
        'file_validate_extensions' => ['jpg png'],
      ],
    ];

    $form['datos_personales']['info_contacto'] = [
      '#type' => 'details',
      '#title' => $this->t('Información de contacto'),
      '#description' => $this->t('Grupo de campos de información de contacto.'),
    ];
    $form['datos_personales']['info_contacto']['direccion'] = [
      '#type' => 'textarea',
      '#title' => $this->t('Dirección'),
      '#cols' => 60,
      '#rows' => 5,
      '#description' => $this->t('Dirección de contacto.'),
    ];
    $form['datos_personales']['info_contacto']['email'] = [
      '#type' => 'email',
      '#title' => $this->t('Correo electrónico'),
      '#required' => TRUE,
      '#size' => 50,
      '#maxlength' => 120,
      '#default_value' => $this->currentUser->getEmail(),
      '#description' => $this->t('Correo electrónico de contacto.'),
    ];
    $form['datos_personales']['info_contacto']['telefono'] = [
      '#type' => 'tel',
      '#title' => $this->t('Teléfono de contacto'),
      '#maxlength' => 64,
      '#size' => 30,
      '#description' => $this->t('Teléfono de contacto personal.'),
    ];

    // Second tab.
    $form['datos_academicos'] = [
      '#type' => 'details',
      '#title' => $this->t('Datos Académicos'),
      '#group' => 'curriculum',
      '#weight' => 1,
    ];
    $form['datos_academicos']['info_titulacion'] = [
      '#type' => 'details',
      '#title' => $this->t('Titulación'),
      '#description' => $this->t('Grupo de campos de información de titulación.'),
    ];
    $form['datos_academicos']['info_titulacion']['titulacion'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Titulación'),
      '#description' => $this->t('Complete la titulación.'),
      '#required' => FALSE,
      '#maxlength' => 100,
      '#size' => 100,
    ];
    $form['datos_academicos']['info_titulacion']['element_machine_name'] = [
      '#type' => 'machine_name',
      '#description' => $this->t('Nombre Máquina de sistema para campo título.'),
      '#machine_name' => [
        'source' => ['titulacion'],

      ],
    ];
    $form['datos_academicos']['info_titulacion']['fecha_fin'] = [
      '#type' => 'date',
      '#title' => $this->t('Fecha de finalización'),
      '#required' => FALSE,
      '#description' => $this->t('Fecha de finalización de los estudios.'),
    ];
    $form['datos_academicos']['info_titulacion']['centro_estudios'] = [
      '#type' => 'radios',
      '#required' => FALSE,
      '#title' => $this->t('Centro de Estudios Universitarios'),
      '#options' => [
        1 => $this->t('Universidad de Sevilla'),
        2 => $this->t('Universidad Autónoma de Barcelona'),
        3 => $this->t('Universidad de Córdoba'),
        4 => $this->t('Universidad Complutense de Madrid'),
        5 => $this->t('Universidad a distancia - UNED'),
      ],
      '#description' => $this->t('Marque el centro donde realizó sus estudios universitarios.'),
      '#default_value' => 1,
    ];

    // Third tab.
    $form['experiencia_laboral'] = [
      '#type' => 'details',
      '#title' => $this->t('Experiencia Laboral'),
      '#group' => 'curriculum',
      '#weight' => 2,
    ];
    $form['experiencia_laboral']['descripcion'] = [
      '#type' => 'textarea',
      '#required' => FALSE,
      '#title' => $this->t('Descripción'),
      '#cols' => 60,
      '#rows' => 5,
      '#description' => $this->t('Describa aquí su experiencia laboral.'),
    ];
    $form['experiencia_laboral']['fichero_curriculum'] = [
      '#type' => 'managed_file',
      '#required' => TRUE,
      '#title' => $this->t('Adjuntar curriculum'),
      '#description' => $this->t('Adjunte su curriculum para enviar'),
      '#upload_location' => 'public://managed',
      '#upload_validators' => [
        'file_validate_extensions' => ['pdf'],
      ],
    ];

    // Fourth tab.
    $form['formacion_complementaria'] = [
      '#type' => 'details',
      '#title' => $this->t('Formación Complementaria'),
      '#group' => 'curriculum',
      '#weight' => 3,
    ];
    $form['formacion_complementaria']['cursos'] = [
      '#type' => 'checkboxes',
      '#title' => $this->t('Cursos'),
      '#default_value' => ['proyectos','drupal'],
      '#options' => [
        'recursos' => $this->t('Recursos Humanos'),
        'ofimática' => $this->t('Ofimática'),
        'proyectos' => $this->t('Gestión de proyectos'),
        'administracion' => $this->t('Administración'),
        'drupal' => $this->t('Drupal'),
      ],
      '#description' => $this->t('Marque los cursos realizados.'),
    ];

    // Extra fields.
    $form['user_id'] = [
      '#type' => 'hidden',
      '#value' => $this->currentUser->id(),
    ];

    // Submit.
    $form['actions'] = [
      '#type' => 'actions',
    ];


    if(!$this->currentUser->isAuthenticated()) {
      $form['help'] = [
        '#title' => $this->t('Por favor, lea las siguientes condiciones:'),
        '#type' => 'item',
        '#markup' => $this->t('<strong>Solo personas usuarias registradas pueden realizar el envío de este formulario.</strong>'),
      ];

    }else {
      $form['actions']['submit'] = [
        '#type' => 'submit',
        '#value' => $this->t('Submit'),
      ];
    }

    return $form;
  }

  /**
   * Form submission handler.
   *
   * @param array $form
   *   An associative array containing the structure of the form.
   * @param \Drupal\Core\Form\FormStateInterface $form_state
   *   The current state of the form.
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    // TODO: Implement submitForm() method.
  }

  /**
   * {@inheritdoc}
   */
  public function validateForm(array &$form, FormStateInterface $form_state) {

  }

}
```