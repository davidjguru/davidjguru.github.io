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

## Introduction   

I was gathering some notes, writing down texts about Drupal performance for my site [therussianlullaby.com](https://www.therussianlullaby.com/), when suddenly I realized that I didn't know if there had been any progress on perfomance issues. What could I do? Well, between continuing to prepare materials for my next article in English, some new tutorial in Spanish that will be published soon and the daily hours of work itself...I had very little time to take a deep look at it. I needed something fast, direct and that would allow me to evaluate results with simplicity. So I started to play a little with .php scripts.  

I have written down here the tasks carried out these moments, in case you want to explore and reproduce them in your surroundings. My main goal here has been to make some observations about the performance of certain actions in a PHP context over several versions of the language and its engine. All these small exercises have been executed in a context based on [DDEV-Local](https://ddev.readthedocs.io/en/stable/), the tool for the implementation of development environments based on Docker and Docker-Container. If you don't know the tool (you should, I recommend it), you'd better read this before about what it is and how to install it:  

* [How to develop a Drupal 9 Site on your local machine using Docker and DDEV](https://www.digitalocean.com/community/tutorials/how-to-develop-a-drupal-9-website-on-your-local-machine-using-docker-and-ddev).  
* [Creating development environments for Drupal with DDEV](https://www.therussianlullaby.com/blog/creating-development-environments-for-drupal-with-ddev/).  
* [Docker, Docker-Compose and DDEV - Cheatsheet](https://www.therussianlullaby.com/blog/docker-docker-compose-and-ddev-cheatsheet/).  
* [Quick Deploy of Drupal 9 using DDEV (6 steps) - Gist in Github](https://gist.github.com/davidjguru/a95329e0ec5b084ac0852ad958da2a14).  
  

## Environment

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

## Scenarios

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

So, after the restar, connect to the main container and get the PHP version:  

```bash
$ ddev ssh
drupal@next-drupal-web-web:/var/www/html/web$ php -v
PHP 8.0.0RC3 (cli)
```

## Key Concepts


### Garbage Collector

https://www.php.net/manual/en/session.configuration.php




## Observations  

Variables:

![Memory Consumption in variables]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_two.png)  





Arrays:

![Memory Consumption in arrays]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_one.png)  





Objects:

![Memory Consumption in objects]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_three.png)  








  