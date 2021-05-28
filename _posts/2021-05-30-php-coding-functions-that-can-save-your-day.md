---
layout: post
title: 'PHP Coding: functions that can save your day' 
permalink: /blog/php-coding-functions-that-can-save-your-day
published: false
date: 2021-05-30
author: davidjguru
categories: [PHP Coding]
sitemap: true
youtubeId: MxXNNOXiUgI
---

| ![Picture from Unsplash, by @umby]({{ site.baseurl }}/images/davidjguru_php_coding_functions_can_save_your_day_main.png) |
|:--:|
| *Picture from Unsplash, user [Umberto, @umby](https://unsplash.com/@umby)* |  

Well, this month I've been doing some things I haven't practiced for some time: data migrations, intensive connections to Gitlab API v4 and even some web-scraping to get data. Along the way I've recovered some old PHP functions that I had forgotten or hadn't used for a long time. As I didn't want to forget them again, I took this notebook as a reference and wrote down a small post with some functions of our old friend PHP that can give us a little help in the day to day.  
<!--more-->

I've given a little context to each one and possible uses. You probably already know them or they seem very obvious to you, but I thought it was fun to share them. I'm sure someone might find them useful. .  

**Note:** This month (april 2021) I've published an article with a review about [the book '31 Days of Drupal Migrations' in The Russian Lullaby](https://www.therussianlullaby.com/blog/books-31-days-of-drupal-migrations). This could be interesting for you.  



## Using a Link Field 

Only starts when you add a new Link field to your content, directly or from a Paragraph using this field. Now you have two sub-fields (title and link) and some config options.  


![Link field basic configuration]({{ site.baseurl }}/images/davidjguru_drupal_8_9_link_fields_from_twig_1.png)

It's also quite possible that you have added [the contrib module 'Link Attributes'](https://www.drupal.org/project/link_attributes) for adding some extra widgets to the Link field. For instance, this is a very common configuration for saving values in `target` attribute. After passing by `/admin/structure/types/manage/article/form-display` and select `link with attributes` for your Link field display, now you have got something like this:  

![Link field basic configuration]({{ site.baseurl }}/images/davidjguru_drupal_8_9_link_fields_from_twig_2.png)

So you're building links to render with two subfields and one related attribute.  
## Building the link with separate items  

When you're getting the link field values from a view and you've selected as formatter: `separate link element and text` in config. First check if title exists and render it. If not, only builds the link data:  

```twig
{% raw %}
  <div class="link-item">
    {% if title %}
      <div class="link-title">{{ title }}</div>
    {% endif %}`
    <div class="link-url">{{ link }}</div>
  </div>
{% endraw %}
```

This is basic structure for showing the link from Twig if you have separate values for link and title, and you aren't returning the values mounting in a single object.  
## Getting value of the target attribute from a Link Field in Paragraph

Sometimes you can use the Link Field within a Paragraph and then you need get the values of the link from a Twig Template. 
Here the syntax can be more strange, but let's see how to extract some values from a field in a Paragraph: Link with url, text and target values.  

```php
{% raw %}
// key -> target="{{ paragraph.field_link_link[0].options.attributes.target }}
<div>
<a href="{{ paragraph.field_link_link.0.url  }}" target="{{ paragraph.field_link_link[0].options.attributes.target }}">
{{ content.field_link_title }}
</a>
</div>
{% endraw %}
```
In the snippet from above, you can see that we're using as title of the link the data from the content, mixing origins.  

## Processing and building links from a View to a Twig Template

I'm getting some fields from a specific view, passing values from a Link field (text, link), and a I want review if external / internal links and put some attributes from the link text.    

The next snippet is a piece of code from a classic `hook_preprocess_views_view_fields()` included in a *.theme file, where we're using a control variable registering if the link was external or not. Then we'll try to reach this variable and take some decisions about the structure in Twig. This case involves three areas of work:  

1. The view itself, when we configure the exit of the fields to the render system (You can overwrite the fields output here).
2. The `theme_name.theme` file, with hooks and methods for altering the future render.  
3. The related Twig template, catching the variables from the theme file and processing values.  

Let's see:    

```php
  if ($view->storage->id() == 'user_page' && $view->current_display == 'page_5') {

    // Loads and cuts the field resources_link for processing.
    $link = $variables['fields']['field_resources_link']->content->__toString();

    //$link -> "<a href="https://www.google.com/" target="_blank">External Link Example</a>"
    //$link -> "<a href="/blog">Internal Link Example</a>

    $sliced_link = explode ('"', $link);
    $url_link = $sliced_link[1];

    // Check if external link.
    if(isset($link) && $link!=""  && str_contains($link, 'target="_blank"')){
      $variables['external'] = true;
      $url_title = explode( "<", substr($sliced_link[4], 1))[0];
    } else if (isset($link) && $link!=""  && !str_contains($link, 'target="_blank"')) {
      // Check if internal link.
      $variables['external'] = false;
      $url_title = explode( "<", substr($sliced_link[2], 1))[0];
    }
    
    // Loads the final values for twig template.
    $variables['url_link'] = $url_link;
    $variables['url_title'] = $url_title;
  }
```

Then mounting the received values in twig:  

```php{% raw %}
<div class="row-news">
	<div class="row-news-header {% if external %} resources-link {% endif %}">
	  <a href="{{ url_link }}" alt={{ url_title }} title= {{ url_title }} 
        {% if external %} 
          target="_blank" 
        {% endif %}> 
        {{ fields.title.content }} 
      </a>
	</div>
	{{fields.field_resource_description.content}}
</div>
{% endraw %}
```

## :wq!

### Recommended song: Catalina - Rosalía & Refree

{% include youtubePlayer.html id=page.youtubeId %}

