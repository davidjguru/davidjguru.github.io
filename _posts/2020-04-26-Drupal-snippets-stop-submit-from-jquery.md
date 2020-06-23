---
layout: post
title: Drupal Snippets (I) - Stop submit from jQuery
permalink: /blog/drupal-snippets-stop-submit-from-jquery
published: true
date: 2020-04-26
author: davidjguru
categories: [Snippets]
sitemap: true
---
| ![Picture from Unsplash, by @jodaarba]({{ site.baseurl }}/images/davidjguru_drupal_snippets_stop_submit_jquery.jpg) |
|:--:|
| *Picture from Unsplash, user [Jose Aragones, @jodaarba](https://unsplash.com/@jodaarba)* |

I want to make a short and quick publication (I promise), just with a snippet, a piece of code that I have used recently and I wanted to share the idea: How to prevent that after a click on the main button of a Drupal form (based on Form API) it reloads again, stopping the process.  
<!--more-->
I was playing creating a new module [in the sandbox of my Drupal.org profile](https://www.drupal.org/sandbox/davidjguru/3130732), thinking about creating a simple form to test Regular Expressions within the context of a Drupal installation.   
My need was not only to create a couple of fields to collect one's regular expression and an input to matchear, but also a button to order the check.  But I didn't need the form to be reloaded again: I don't really want to do any kind of submit, I prefer to listen from the JavaScript side and process everything from a custom JavaScript / jQuery library.
To do this I remembered a method available in jQuery to stop specific actions, called preventDefault(). 

See the related parts of my custom form (based in Form API, inside a Form class, extending FormBase class, etc):

```php
  $form['regexp_checker_result'] = [
      '#type' => 'item',
      '#markup' => $this->t('Results zone.'),
      '#prefix' => '<div id="regexp_checker_final_result">',
      '#suffix' => '</div>',
    ];

    $form['regexp_checker_action'] = [
      '#type' => 'button',
      '#value' => $this->t('Check'),
      '#weight' => 5,
      '#prefix' => '<div id="regexp_checker_button">',
      '#suffix' => '</div>',
      '#attached' => [
        'library' => [
          'regexp_checker/regexp_checker.showing_results',
        ],
      ],
    ];

```
Ok, this is a placement for HTML and a button, just a fake-submit. How can I stop the reloading of the page? Let's see in showing_results.js: 

```javascript
(function ($, Drupal) {
    'use strict';

  $( document ).ready(function () {
    console.log( "ready!" );
    $("#regexp_checker_button").click(function (e) {
      // Stops the form from being sent.
      e.preventDefault();
      // Add info over the HTML element.
      $("#regexp_checker_final_result").html("<p>Processing the Regular Expression.</p>");
      // Only for check.
      console.log("The Submit was stopped");
    });
  });
})(jQuery, Drupal);
```

**What e.preventDefault() method really does in jQuery?**
Well, preventDefault() stops the default action of a selected element. Does not accept any parameter and works:

1. Prevents a submit button from finally performing the submit itself. 
2. Prevents link from following an URL so the browser don't go to another direction.

See the documentation in [the jQuery docs](https://api.jquery.com/event.preventDefault/).

Okay, but then... How do I launch the form? (we've made a form that should theoretically solve an action, right?) Well, after executing the corresponding actions you need on the form (sanitizing data, queries, settings, etc), you can submit it again by making it click using a trigger(click) event for itself, including a small boolean variable to recognize when it is on the first load and when the treatment has been solved. 

In the following example stopping the submission of a Drupal form, I check two fields (name and lastname) and relaunch the submit: 

```
(function ($) {
  'use strict';
  $(document).ready(function() {
    let finally_full_filled = false;

  $("#register_form_submit").click(function (e) {
        if(!finally_full_filled) {
              e.preventDefault();

            if($('#edit-managing-activities-register-name').val() != '') {
                if( $('#warning_name').length ) {
                    $( "#warning_name" ).remove();
                } 
            } else {
                    if ($("#warning_name").length === 0) {
                    $("#register_form_name").append('<p id="warning_name" style="color:red">Sorry but the name can\'t be empty.</p>');
                }
             }
           
            if ($('#edit-managing-activities-register-lastname').val() != '') {
                if( $('#warning_lastname').length ) {
                    $( "#warning_lastname" ).remove();
                }  
            } else { 
                    if ($("#warning_lastname").length === 0) {
                    $("#register_form_lastname").append('<p id="warning_lastname" style="color:red">Sorry but the lastname can\'t be empty.</p>');
                }
            }
               
            window.alert("Thanks for show your interest!.");
            finally_full_filled = true;
            $("#edit-submit").trigger('click');
           }
```

So you can stop the form submit and re-launch the same submit.
See [https://api.jquery.com/trigger/](https://api.jquery.com/trigger/) for more information. 

## :wq!