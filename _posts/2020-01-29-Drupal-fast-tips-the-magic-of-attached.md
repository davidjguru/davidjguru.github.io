---
layout: post
title: Drupal Fast Tips (III) - The Magic of '#attached'
permalink: /blog/drupal-fast-tips-the-magic-of-attached
published: true
date: 2020-01-29
author: davidjguru
categories: [Drupal]
sitemap: true
---
| ![Picture from Unsplash, by @gaspanik]({{ site.baseurl }}/images/davidjguru_drupal_8_magik_of_attached_1.jpg) |
|:--:|
| *Picture from Unsplash, user Masaaki Komori @gaspanik* |

Since a few days ago, I'm preparing a tutorial about the integration of JavaScript in Drupal that will be released soon 
(it will be written in Spanish in Medium). On the other hand, recently I was preparing some Drupal patches for the 
portability to Drupal 8 of a small contrib module, the [humans.txt](https://www.drupal.org/project/humanstxt). 
Okay, but what do the two activities have in common?...
<!--more-->


The answer is simple: in both cases I had to make use of the #attached property, available for use in Drupal's 
render arrays. And this made me think of a property available in the Drupal arrays that I still write about a little 
bit, and it's very interesting. 
Don't expect a big meditation, just some ideas and advice that I hope will be useful to someone. 


## Retrospective
In Drupal 7, you used to do the next steps using differents procedural functions:


### Adding HTML Meta tag: drupal_add_html_head()


* $meta_charset = array('#tag' => 'meta', '#attributes' => array('charset' => 'utf-8'));


* drupal_add_html_head($meta_charset, 'meta_charset');


* [https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_add_html_head/7.x](https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_add_html_head/7.x)


### Adding JavaScript: drupal_add_js()


* drupal_add_js('http://cdn.jquerytools.org/1.2.6/jquery.tools.min.js', 'external');


* [https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_add_js/7.x](https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_add_js/7.x)


### Adding CSS: drupal_add_css()


* drupal_add_css('http://fonts.googleapis.com/css?family=News+Cycle', array('type' => 'external'));


* [https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_add_css/7.x](https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_add_css/7.x)

## Current Usage
But now in Drupal 8 and onwards, all is centralized in the #attached property. Using this property, you'll be able to add some stuff, describe in the sub-properties of #attached, like:

* Library -> $render_array[‘#attached’][‘library’]
* drupalSettings (from PHP to JavaScript) -> $render_array[‘#attached’][‘drupalSettings’]
* Http_Header -> $render_array[‘#attached’][‘http_header’]
* HTML Link in Head -> $render_array[‘#attached’][‘html_head_link’]
* HTML Head -> $render_array[‘#attached’][‘html_head’]
* Feed -> $render_array[‘#attached’][‘feed’]
* Placeholders -> $render_array[‘#attached’][‘placeholders’]
* HTML Response Placeholders -> $render_array[‘#attached’][‘html_response_attachment_placeholders’]

For More Info, Visit: [https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21HtmlResponseAttachmentsProcessor.php/function/HtmlResponseAttachmentsProcessor%3A%3AprocessAttachments/8.7.x](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21HtmlResponseAttachmentsProcessor.php/function/HtmlResponseAttachmentsProcessor%3A%3AprocessAttachments/8.7.x)

## Examples
How can we modify some sections in our rendered HTML code from Drupal 8?
For example, insert a new tag link in the <head> section for every rendered page...well, we'll using the magical 
\#attached property, such a key for our Drupal Render Arrays, ready to add some resources to our code.


## :wq!

