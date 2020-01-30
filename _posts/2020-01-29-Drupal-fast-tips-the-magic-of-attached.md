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
Don't expect a big meditation, just some ideas and tips that I hope will be useful to someone. 
But first, let's start at the beginning (or almost). 


## Retrospective
In some occasions, you may need add some resources to your Drupal project, in order to alter the rendering of some 
 page, block or some stuff. In this case, you could add your resources in the Drupal 7 way, using some functions.
 You used to do the next steps using different procedural functions:


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
But now in Drupal 8 and onwards, all is centralized in the #attached property. Well, no. It's really based on two 
concepts that centralize everything in a shared way:  

1.  The definition of "library".
2. The use of the property \#attached.

**First**, you have to know what's the format for libraries for your modules. [Here you can see a set of examples defining libraries for custom modules](https://gitlab.com/snippets/1929232)

**Second**, you have to know the property. Using this property, you'll be able to add some stuff, describe in the 
sub-properties of #attached. Using these sub-properties, you'll can inform to the Render System of Drupal about the
 resources that you wanna use. It could be stuff like libraries, transported values from Server (PHP) to Client 
 (JavaScript) using drupalSettings, and more things like diverse HTML tags. 
 
 We're talking about some concepts like:

* **Library** -> Libraries defined in a name_module.libraries.yml file, for CSS or/and JavaScript resources.  
 **Format:** $render_array[‘#attached’][‘library’]  
 
* **drupalSettings** -> It's an internal tool in order to transport values from a multidimensional array in PHP to the
JavaScript code.   
 **Format:** $render_array[‘#attached’][‘drupalSettings’]  
 
* **Http_Header** ->  Use this when add HTTP headers or / and status response codes. 
 **Format:** $render_array[‘#attached’][‘http_header’]  
 
* **HTML Link in Head** ->  Just for write more \<link> tags in the HTML \<head> section.   
 **Format:** $render_array[‘#attached’][‘html_head_link’]  
 
* **HTML Head** ->  Value to insert in case of tags in the HTML section \<head>.   
 **Format:** $render_array[‘#attached’][‘html_head’]  
 
* **Feed** ->  Only for add classical RSS feeds.  
 **Format:** $render_array[‘#attached’][‘feed’]   
 
* **Placeholders** -> Playing with placeholders in texts.  
 **Format:** $render_array[‘#attached’][‘placeholders’]  
 
* **HTML Response Placeholders** ->  Put placeholders in the HTML responses.   
  **Format:** $render_array[‘#attached’][‘html_response_attachment_placeholders’]  
  
 Here you can see the defined allowed types, from a diff looking for unsupported types:  
  ![Supported keys for the attached property in Drupal 8]({{ site.baseurl }}/images/davidjguru_drupal_8_keys_for_attached_property.png)

*Supported keys for the attached property, from the Drupal.org official documentation.* |



**For More Info, Visit:** [https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21HtmlResponseAttachmentsProcessor.php/function/HtmlResponseAttachmentsProcessor%3A%3AprocessAttachments/8.7.x](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21HtmlResponseAttachmentsProcessor.php/function/HtmlResponseAttachmentsProcessor%3A%3AprocessAttachments/8.7.x)

## Examples
How can we modify some sections in our rendered HTML code from Drupal 8?
For example, insert a new tag link in the <head> section for every rendered page...well, we'll using the magical 
\#attached property, such a key for our Drupal Render Arrays, ready to add some resources to our code.

### Adding a new \<link> in the \<head> HTML Tag  
In this case, I was trying to prepare a patch for the contrib module humans.txt and needed to insert a link in the HTML 
header pointing to the link. 

The following example is not very optimized (the code can be reduced much more and I have to modify the sent patch) but 
this way it can be more didactic. It's based on the use of the page_attachments_hook for Drupal. In the last line you 
can see the 'attached' property with the key 'html_head_link':

```php
/**
 * Implements hook_page_attachments().
 */
function humanstxt_page_attachments(array &$attachments) {

    // Getting the current front path.
    $current_front_url = Url::fromRoute('<front>');
    $current_front_path = $current_front_url->toString();

    // Adding the filename to the path.
    $humanstxt_path = $current_front_path . "humans.txt";

    // Building the link data.
    $link_description = [
     'rel' => 'author',
      'link' => $humanstxt_path,
    ];

    // Adding the new head link tag.
    $attachments['#attached']['html_head_link'][] = [$link_description];
}
```
And here you can [see the patch on its own issue](https://www.drupal.org/project/humanstxt/issues/3104647), commented by
 [Pedro Cambra](https://www.drupal.org/u/pcambra) from Cambrico [http://cambrico.net/](http://cambrico.net/), a Drupal 
 agency very focused on contributing and mentoring on Drupal.org. 
 
### Adding libraries in a render array    
 In the following case, it is only a matter of adding custom libraries of JavaScript resources to a render array 
 returned by a controller method. A controller that ends up "painting" a simple data table through a path, and that is 
 dynamized through custom libraries with Vanilla JavaScript. 
 
 ```php
// Before:
$final_array['welcome_message'] = [
  '#type' => 'item',
  '#markup' => $this->t('Hello World, I am just a text.'),
];
// After: 
$final_array['welcome_message'] = [
  '#type' => 'item',
  '#markup' => $this->t('Hello World, I am just a text.'),
  '#attached' => [
    'library' => [
      'javascript_custom_module/js_hello_world_console',
    ],
  ],
];
```
As we can see, we can use the property at the same time of building the renders arrays or later, through some hook to 
modify elements. **What do you think?**...***Interesting?*** The truth is that despite its simplicity and discretion, 
it's something I almost use in all Drupal-based projects. 

I hope you like it as well. 

Greetings! 

## :wq!

