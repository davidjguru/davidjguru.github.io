---
layout: post
title: 'PHP Coding: functions that can save your day' 
permalink: /blog/php-coding-functions-that-can-save-your-day
published: true
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

The old man PHP has over 1000 built-in functions that can be called and used directly from your code and will be independient of the platform/CMS/framework (Drupal, Symfony, Laravel...) so you can load the functions in your scripts or classes in order to execute specific tasks. I've given a little context to each one and possible uses. You probably already know them or they seem very obvious to you, but I thought it was fun to share them. I'm sure someone might find them useful.  

**Note:** This month (may 2021) I've published an article with a review about [the book '31 Days of Drupal Migrations' in The Russian Lullaby](https://www.therussianlullaby.com/blog/books-31-days-of-drupal-migrations). This could be interesting for you.  



## basename()

I really don't know why I had forgotten this function of PHP...but the fact is that I had been processing URLs for some time and cutting them to get paths, directory names and final files.  

One day I remembered it again and there it was! basename() is a function that returns the final item in a path and if you add a suffix (maybe you only need the name of a file, not its extension), the function will delete the .extension and will give you just a name.  

Remember:  
Basename returns the final element of a path.  
Using: basename($path);  basename($path, $suffix)

```php
<?php

// Do your things.
[...]
// Trying to get the final component in url.
$cut_url = substr($url, 0, -6);
// Same as.
$cut_url = basename($url);
```
**More Info:** 
+ [php.net/basename](https://www.php.net/manual/en/function.basename.php)


## getElementsByTagName()

Ok, well I was doing some data extractions from web scraping and suddenly remembered the existence of 
the DOMDocument class in PHP, a set of available functions for getting a HTML document and parsing / extracting diverse information from the DOM object.  

This function getElementsByTagName() gives you a set of results gettings values only from reffered tags in the processed DOM. For instance, I can do something like:  

```php
<?php
// Create a new DOM Document to get the HTML remote content.
$html_code = new DOMDocument();

// Load the url's contents into the DOM item.
$html_code->loadHTMLFile($url); 
```

But in my Drupal context will be just like:  
```php 
<?php
[...]
// Third: Gets a whole post from original website.
$post = file_get_contents($domain_link_post);
$dom = Html::load($post);
``` 
It's the same operation, and then we'll get specific values:  
```php
<?php

// Do your things.
[...]

// Fourth: Gets all the interesting values for the new node. 
// Gets title and subtitle from DOM.
$block_title = $dom->getElementById('title');
$title = $block_title->getElementsByTagName('h1')->item(0)->textContent;
$alias = $block_title->getElementsByTagName('h3')->item(0)->textContent;
```
In the former codeblock I was extracting some basic data for load in a new Drupal node, strings like the title of the node and information to fill in a field called "alias". My parsed DOM document is well known to me, this is very important for implementing the correct parsing: I must know well where are the data I am interested in.  
In fact Drupal is using this DOMDocument class under the hood through a wrapper, the Html class available from:  

```php
/core/lib/Drupal/Component/Utility/Html.php
```
In this class is doing an extensive use of the DOMDocument class. Just in the load() main function you can see for instance:  
```php
<?php
[...]
$dom = new \DOMDocument();
// Ignore warnings during HTML soup loading.
@$dom->loadHTML($document);

return $dom;
```

**More Info:**  
+ [DOMDocument class of PHP](https://www.php.net/manual/en/class.domdocument.php)  
+ [Html class of Drupal](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Component%21Utility%21Html.php/class/Html/8.2.x)  
  
## glob()

What is glob? (baby don't hurt me...) xD Well the glob() function will return naming of files in a certain path, and I was using the function for treating files and processing values, or delete existing files from a folder.  

Examples:  

Here deleting files by unlink function.  
```php
<?php
[...]
$initial_files = glob('sites/default/files/my_folder/*');
// Deleting all the files in the list
foreach($initial_files as $file) {
  if(is_file($file)){
    // Delete the given file
    unlink($file); 
  }
}
```

And here looping over the files for processing some specific values. 
```php
<?php
[...]
// Get all the existing files.
$geodata_files = glob('modules/custom/my_custom_module/geodata_files/*');

// Now we are going to loop over the existing files for doing stuff.
foreach($geodata_files as $geodata_file) {

  // Doing your things.
  [...]
}

```

**More Info:**  
+ [Glob function of PHP](https://www.php.net/manual/en/function.glob.php)  



## curl_init()

Curl is a function library supported by PHP that allows HTTP request and also is available as command line tool for prompt. In that context I've been using it for a while now, but only from the Linux console... The reason? Well, for almost all the code related to REST clients from Drupal I've implemented it using Guzzle (the PHP reference library for REST clients and integrated in Drupal) or indirectly using the HTTP Client Manager, a resource that allows you manage HTTP Clients from YAML description files -or JSON- (and is using Guzzle under the hood, by the way). 

But the last week I had to implement a small external query from my code to the Google Places API and I thought I'd try curl directly from PHP. This was my case:  

```php
<?php

[...]

// Enables a new cURL resource.
$ch = curl_init();
$url_map = "https://maps.googleapis.com/maps/api/place/details/json?cid=" . $required_cid_code . "&key=" . $this->myConfig->get('google_places_api_key');
curl_setopt($ch, CURLOPT_URL, $url_map);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true); 
curl_setopt($ch, CURLOPT_HEADER, 0);

// Get the geo file from google maps API for places.
$result_value = curl_exec($ch);
$converted_result = json_decode($result_value, true);
curl_close($ch);

[...]
```

curl is available from your server installation as a PHP resource, in my case the web container of [my DDEV local deploy](https://www.digitalocean.com/community/tutorials/how-to-develop-a-drupal-9-website-on-your-local-machine-using-docker-and-ddev), and only requires initialize a session, set your options for the work, then execute the session and finally close it.  

**More Info:**  
+ [The PHP's book of curl](https://www.php.net/manual/en/book.curl.php)  
+ [curl basic example](https://www.php.net/manual/en/curl.examples-basic.php)  
+ [Guzzle the PHP HTTP client](https://docs.guzzlephp.org/en/stable/)  
+ [Drupal HTTP Manager contrib module](https://www.drupal.org/project/http_client_manager)  

## :wq!

### Recommended song: Quemas - ede & Xoel López

{% include youtubePlayer.html id=page.youtubeId %}

