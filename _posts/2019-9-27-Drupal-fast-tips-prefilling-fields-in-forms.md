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

In this case, we will opt for a form created as Basic Form, more adaptable and elastic for general purposes. We will create a new custom module for Drupal 8, and in its /src/Form route we will include our test form. We will see the code in the next section. 

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
--inputs='"name":"email", "type":"email", "label":"Email", "options":"", "description":"User email", "maxlength":"", "size":"", "default_value":"", "weight":"1", "fieldset":""'  \
--inputs='"name":"submit", "type":"submit", "label":"Submit", "options":"", "description":"Submit", "maxlength":"", "size":"", "default_value":"", "weight":"3", "fieldset":""' \
--path="/my_random_module/forms/random_form" \
--no-interaction

```
## Filling fields in our Form

### Inyecting services in Drupal 8

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