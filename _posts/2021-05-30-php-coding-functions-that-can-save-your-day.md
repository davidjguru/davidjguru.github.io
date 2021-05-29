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



## basename()

I really don't know why I had forgotten this function of PHP...but the fact is that I had been processing URLs for some time and cutting them to get paths, directory names and final files.  

One day I remembered it again and there it was! basename() is a function that returns the final item in a path and if you add a suffix (maybe you only need the name of a file, not its extension), the function will delete the .extension and will give you just a name.  

Remember:  
Basename returns the final element of a path.  
Using: basename($path);  basename($path, $suffix)

```php
// Trying to get the final component in url.
$cut_url = substr($url, 0, -6);
// Same as.
$example = basename($url);
```
+ **More Info:** [php.net/basename](https://www.php.net/manual/en/function.basename.php)


![Link field basic configuration]({{ site.baseurl }}/images/davidjguru_drupal_8_9_link_fields_from_twig_2.png)

## Building the link with separate items  


  
## Getting value of the target attribute from a Link Field in Paragraph




## Processing and building links from a View to a Twig Template



## :wq!

### Recommended song: Quemas - ede & Xoel López

{% include youtubePlayer.html id=page.youtubeId %}

