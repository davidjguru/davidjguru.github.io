---
layout: post
title: Playing with PHP 8 Performance
permalink: /blog/playing-with-php-8-performance
published: true
date: 2020-12-10
author: davidjguru
categories: [General]
sitemap: true
---
| ![Picture from Unsplash, by @@thefredyjacob]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_main.png) |
|:--:|
| *Picture from Unsplash, user [Fredy Jacob](https://unsplash.com/@thefredyjacob)* |


The latest version of PHP ([PHP 8](https://www.php.net/releases/8.0/en.php)) was released recently, maybe at the end of November 2020 (almost a month ago) and a lot of articles has been dedicated to its new features, changes and advanced updates of the most popular programming language for web. I would like to write about this new version, but instead of talkin' bout features, I have been thinking about performance.  
<!--more-->

---------------------------------------------------------------------------------
  
  **Table of Contents**  
  <!-- TOC -->  
  [1- Introduction](#1--introduction)  
  [2- Environtment](#2--environment)  
  [3- Scenarios](#3--scenarios)  
  [4- Key Concepts](#4--key-concepts)  
    * [4.1- PHP under the hood](#41--php-under-the-hood)  
    * [4.2- Garbage Collector](#42--garbage-collector)  
    * [4.3- Some related functions](#43--some-related-functions)  
  [5- Observations](#5--observations)  
  [:wq!](#wq)  
  <!-- /TOC -->
  
  -------------------------------------------------------------------------------

## 1- Introduction   

I was gathering some notes, writing down texts about Drupal performance for my site [therussianlullaby.com](https://www.therussianlullaby.com/), when suddenly I realized that I didn't know if there had been any progress on perfomance issues. What could I do? Well, between continuing to prepare materials for my next article in English, some new tutorial in Spanish that will be published soon and the daily hours of work itself...I had very little time to take a deep look at it. I needed something fast, direct and that would allow me to evaluate results with simplicity. So I started to play a little with .php scripts.  

I have written down here the tasks carried out these moments, in case you want to explore and reproduce them in your surroundings. My main goal here has been to make some observations about the performance of certain actions in a PHP context over several versions of the language and its engine. All these small exercises have been executed in a context based on [DDEV-Local](https://ddev.readthedocs.io/en/stable/), the tool for the implementation of development environments based on Docker and Docker-Container. If you don't know the tool (you should, I recommend it), you'd better read this before about what it is and how to install it:  

* [How to develop a Drupal 9 Site on your local machine using Docker and DDEV](https://www.digitalocean.com/community/tutorials/how-to-develop-a-drupal-9-website-on-your-local-machine-using-docker-and-ddev).  
* [Creating development environments for Drupal with DDEV](https://www.therussianlullaby.com/blog/creating-development-environments-for-drupal-with-ddev/).  
* [Docker, Docker-Compose and DDEV - Cheatsheet](https://www.therussianlullaby.com/blog/docker-docker-compose-and-ddev-cheatsheet/).  
* [Quick Deploy of Drupal 9 using DDEV (6 steps) - Gist in Github](https://gist.github.com/davidjguru/a95329e0ec5b084ac0852ad958da2a14).  
  

## 2- Environment

This is the summary of my test environment and the sum of hardware and software virtualization I'm performing:   

```txt

|  **Host**           |                                 |
| -------------------:|:--------------------------------|
| Processor           | i7 - 10510U, 1.80GHz, 2.30GHz   |
| RAM                 | 16GB                            |
| Drive               | SSD, 476GB                      |
| System              | 64bit                           |
| OS                  | Windows 10, build 18363         |
| Virtualization      | Hardware                        |
| Virtualization tool | VirtualBox 6.1.16               |


|  **Guest**          |                                 |
| -------------------:|:--------------------------------|
| Processor           | 4CPUs                           |
| RAM                 | 11GB                            |
| Drive               | vdi, 30GB                       |
| System              | 64bit                           |
| OS                  | Ubuntu 18.05.5 (LTS)            |
| Virtualization      | Software                        |
| Virtualization tool | Containerization                |


|  **Containerization**  |                              |
| ----------------------:|:-----------------------------|
| Docker                 | 19.03.14                     |
| Docker-Compose         | 1.24.1                       |
| DDEV                   | 1.16.0                       |


```

## 3- Scenarios

Let's get to work! First, You'll need some diverse environments for your comparative study. As my idea is to establish a comparison, I will need several tools: different versions of PHP and scripts to create resources and be able to make measurements about Memory Consumption, my first key indicator for this testing. For the different PHP versions, I rely on the immense facility provided by DDEV as a tool to build base projects in the PHP version we request, with which I will create three different projects with their associated container networks and their PHP versions installed at three milestones: PHP5.x, PHP7.x and PHP8.x.  

Creating specific environments using DDEV is quite easy, you only have to install DDEV in your system and ask for a new build. Just select a config option in the response by prompt:  

![Select config option using DDEV]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_four.png)

As you can see you can pick diverse technologies as WordPress, Typo3, Laravel and the latest versions of Drupal. You can also choose PHP basic config, installing PHP 7.3.24 as default.  

Choosing Drupal 6, 8 and 9 you'll have a PHP setup with different versions:  

### PHP 5 // Drupal 6  

```bash
drupal@old-drupal-web:/var/www/html$ php -v
PHP 5.6.40-38+0~20201103.42+debian10~1.gbpb211e0 (cli) 
```

### PHP 7 // Drupal 8  

```bash
drupal@current-drupal-web:/var/www/html/docroot$ php -v
PHP 7.3.24-3+0~20201103.72+debian10~1.gbp945915 (cli) 
```

### PHP 8 // Drupal 9  

In this case, the default implementation of the DDEV-related containers for Drupal 9 is using PHP7, but whith a little change you can enable PHP8. Just stop your container network doing:  

```bash
$ ddev stop
```

After that, change the php_version param in `.ddev/config.yml` and then restart the containers:  

```bash
name: next-drupal-web
type: drupal9
docroot: web
php_version: "8.0"
webserver_type: nginx-fpm
router_http_port: "80"
router_https_port: "443"
```

So, after the restart, connect to the main container and get the PHP version:  

```bash
$ ddev ssh
drupal@next-drupal-web-web:/var/www/html/web$ php -v
PHP 8.0.0RC3 (cli)
```

## 4- Key Concepts

Let's review some key issues of this little home experiment, some important ideas to know about the PĤP context.  

### 4.1- PHP Under the hood

For many people PHP is just like an interpreted C that you can use in HTML documents. It's a big simplification, but somewhat controversial: there are aspects that are directly related and others that are not. PHP is a high-level programming language built in C to facilitate the development of web resources, which still maintains elements of the low-level C.  

Some of this basic elements from C are an importante key in the way we understand PHP and its performance. Let's see just a pair of items:  

* **Variables:** variables in PHP are really data structures called 'zval'(Zend Value) and implemented in C. It's a 'struct', it means, composite data types. It represents any PHP value (number, string, array...).  
  
  This is a register for a PHP variable, initially (PHP5 or so):  
  ```php
typedef struct _zval_struct {
    zvalue_value value; /* variable value */
    zend_uint refcount__gc; /* reference counter */
    zend_uchar type; /* value type */
    zend_uchar is_ref__gc; /* reference flag */
} zval;
  ``` 

Now evolved to:  

```php
typedef struct _zval_struct {
    union {
        zend_long lval;
        double dval;
        zend_refcounted *counted;
        zend_string *str;
        zend_array *arr;
        zend_object *obj;
        zend_resource *res;
        zend_reference *ref;
    …
    } value;
    zend_uchar type;
    zend_uchar type_flags;
    uint16_t extra;
    uint32_t reserved;
} zval;
```

As you know well, PHP is a dynamically-typed language, and the zval is ready to storage different types. In memory, zval is using two 64bit words, in this order:  

```txt
|  **Value**             |         Space                |
| ----------------------:|:-----------------------------|
| Type                   | 0 - 7                        |
| Type_flags             | 8 -                          |
| Extra                  | - 31                         |
| Reserved               | 32 - 63                      |
```

Get more info about zval:  
* [zend.com/basic-php-structures](https://www.zend.com/basic-php-structures)  
* [nikic.github.io/Internal-value-representation-in-PHP-7](https://nikic.github.io/2015/05/05/Internal-value-representation-in-PHP-7-part-1.html)  

* **Arrays:**  arrays in PHP are a special data structure, too. In fact in PHP doesn't exists as we know in other languages. In PHP are like maps, or maybe like ordered dictionaries: key/value pairs while the key/value mapping is implemented using hashtables. Structures with a very special treatment, according to the values it contains and the types of data.  
  
In this image you can see the model from the book [PHP 7 Data Structures and Algorithms, by Mizanur Rahman](https://www.packtpub.com/product/php-7-data-structures-and-algorithms/9781786463890):  

![Structure of Arrays in PHP]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_five.png) 

Get more info about arrays:  
* [zend.com/php-arrays](https://www.zend.com/php-arrays)
* [nikic.github.io/PHPs-new-hashtable-implementation.html](https://nikic.github.io/2014/12/22/PHPs-new-hashtable-implementation.html)




### 4.2- Garbage Collector

This is a system that allows PHP cleaning the memory. You can enable or disable the funcions using some parameters from the code or from internal config files. **How it works?** Well, we already know that a variable in PHP es stored in a container called zval, from C language. Here we're storing the type of variable, value and some pieces of info:  

* If the variable is part of the reference set.  
* If the variable is referenced from another places.  
  
So, periodically, some algorithms are reviewing the variables and resources, deleting the unusued registers.  

For cases of cyclical references, then PHP is using the algorithm created by Bacon and Rajan, present here: [researcher.watson.ibm.com/Bacon01Concurrent.pdf](https://researcher.watson.ibm.com/researcher/files/us-bacon/Bacon01Concurrent.pdf). And here is a short version: [php.net/features.gc.collecting-cycles.php](https://www.php.net/manual/en/features.gc.collecting-cycles.php).  

For Garbage Collection, we have some params available in php.ini: [php.net/session.configuration.php](https://www.php.net/manual/en/session.configuration.php)   

In general terms, we're configuring this in the php.ini file:  

```php
session.gc_maxlifetime = 3600
session.gc_probability = 1
session.gc_divisor = 1000
```

But in the Drupal context, we can change these values from a Drupal installation, inside the file 'services.yml' in docroot/sites/default/:  

```yml
parameters:
  session.storage.options:
    gc_probability: 1
    gc_divisor: 100
    gc_maxlifetime: 200000
    cookie_lifetime: 2000000
```


### 4.3- Some related functions  

There's a set of PHP functions linked to the memory consumption ready-to-play.  


* **memory_get_usage():** Return the amount of memory in bytes, from the allocated for a whole PHP script. [php.net/function.memory-get-usage.php](https://www.php.net/manual/en/function.memory-get-usage.php). In the current exercises for this post, I'm using this function in order to calculate memory usage. Is not an exact measure (is not measuring exactly memory consumption in variables, arrays or objects, but for the executed script). So, is only a relative evaluation, very basic and only for only for general assessments.  

* **memory_get_peak_usage():** Return the peak of memory allocated by PHP. [php.net/function.memory-get-peak-usage.php](https://www.php.net/manual/en/function.memory-get-peak-usage.php).  

* **debug_zval_dump():**  Dumps zend value. [php.net/function.debug-zval-dump.php](https://www.php.net/manual/en/function.debug-zval-dump.php).  

* **gc_enable():** Enables the reference collector. [php.net/function.gc-enable.php](https://www.php.net/manual/en/function.gc-enable.php).  


## 5- Observations  


### Scripting  


I commited the related scripting here in my Github repository:  
* [github.com/davidjguru/php_performance](https://github.com/davidjguru/custom_resources/tree/main/php_performance)  

### Memory consumption by creating variables  

For this case, I'm creating an unique variable with a string value taken from [chiquitoipsum.com/](http://www.chiquitoipsum.com/), in a loop from the first to 100000 iterations (100K variables created) over the memory system.  

```php
// Unique variable.
   $memory_first = memory_get_usage();
   $var = 'Lorem fistrum por la gloria de mi madre por la gloria de mi madre apetecan jarl.';
   echo 'Length of the string contained in the initial variable: ' . strlen($var), "\n";
   echo 'Initial Memory consumption, non-real in bytes:  ' . (memory_get_usage() - $memory_first) , "\n";
   echo 'Initial Memory consumption, non-real in bits: ' . ((memory_get_usage() - $memory_first) * 8), "\n"; 
   echo 'Initial variable size, STRLEN in bytes: ' . strlen($var) , "\n";
   echo 'Initial variable size, using bits: ' . (strlen($var) * 8) , "\n";
```

In this block, I'm watching the real memory consumption, comparing memory_get_usage() (for the whole script) and the allocation only for the variable. In this case I'm using strlen, that returns number of bytes (in the character set, number of bytes = number of characters, due to the asignation of 1 byte = 1 char). So I'm getting the values:  

```bash
Initial Memory consumption, non-real in bytes:  32
Initial Memory consumption, non-real in bits: 256
Initial variable size, STRLEN in bytes: 80
Initial variable size, using bits: 640
```

As you can see, the values differ.  


This is my testing script: [github.com/davidjguru/show_memory_usage_using_variables.php](https://github.com/davidjguru/custom_resources/blob/main/php_performance/show_memory_usage_using_variables.php).  

And here are the results:  

![Memory Consumption in variables]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_two.png)  


Showing the size in bytes, from zero up to 15MB (y-axis).  


### Memory consumption by creating arrays  

Ok. Here we're measuring some paradoxes from the PHP Arrays. Arrays in PHP are not - strictly speaking - fixed-length array structures (as in other languages). In fact is more like a HashMap, with a lot of data structure.
In this script we see what size a 100K element array occupies in memory within a 64-bit architecture system and we compare it with the use of a fixed and determined length array.  For the fixed-legth array, I'm using the [SPL Extension for PHP](https://www.php.net/manual/en/book.spl.php), and [the SplFixedArray Class](https://www.php.net/manual/en/class.splfixedarray.php) for building this kind of arrays.  

Let's think about an idea: In 64b - Architecture, one integer in format "long" is build using 8 bytes*, so theoretically for an 100K items array, we'll use 800.000 bytes (0.8 MB). But the results are returning differents values (due to the implementation of arrays in PHP showed in the former section).  

*[docs.oracle.com/index.html](https://docs.oracle.com/cd/E19253-01/817-6223/chp-typeopexpr-2/index.html).  



![Memory Consumption in arrays]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_one.png)  

Here we can see the direct effects of the changes in the implementation of the zval structures from PHP 5.x to PHP 7.x: Now the arrays are more lights. let's remember a previous calculation: Just for an array with 100K integer items = 0,8MB. But in PHP:  

```bash
Memory Usage in PHP 5.6.40: 13,97 MB  
Memory Usage in PHP 7.3.24: 4 MB
Memory Usage in PHP 8.0.0: 4 MB
```

There is almost 10 MB difference from PHP 5.x, but it remains the same in the latest versions.  


### Memory consumption by creating objects

Here I'm using just a basic PHP class with a single property, and then creating by 100K PHP objects.  

![Memory Consumption in objects]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_three.png)  

We can see the memory consumption peaks, just before the launch of the PHP garbage collector (explained in a previous section to give context), which releases memory and periodically lowers the consumption values. 

It's quite interesting see that the garbage collector is launched in PHP 5.6.40 later than PHP 7.3.24 and PHP 8.0.0, allowing reach peaks 5,695264 MB of memory consumption.  


## :wq! 

Finally, after comparing in the three previous sections the memory consumption in the three versions of PHP, it seems that the last one available (PHP 8) is similar in this dimension to the operations in memory of PHP 7 (in most tests the graphs of 8 and 7 overlap with the same values), and also moves away from the memory management of PHP 5.  
