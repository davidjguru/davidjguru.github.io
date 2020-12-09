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

I was gathering some notes, writing down texts about Drupal performance for my site [therussianlullaby.com](https://www.therussianlullaby.com/), when suddenly I realized that I didn't know if there had been any progress on perfomance issues. What could I do? Well, between continuing to prepare materials for my next article in English, some new tutorial in Spanish that will be published soon and the daily hours of work itself...I had very little time to take a deep look at it. I needed something fast, direct and that would allow me to evaluate results with simplicity. So I started to play a little with .php scripts.  

I have written down here the tasks carried out these moments, in case you want to explore and reproduce them in your surroundings. My main goal here has been to make some observations about the performance of certain actions in a PHP context over several versions of the language and its engine. All these small exercises have been executed in a context based on [DDEV-Local](https://ddev.readthedocs.io/en/stable/), the tool for the implementation of development environments based on Docker and Docker-Container. If you don't know the tool (you should, I recommend it), you'd better read this before about what it is and how to install it:  

* [How to develop a Drupal 9 Site on your local machine using Docker and DDEV](https://www.digitalocean.com/community/tutorials/how-to-develop-a-drupal-9-website-on-your-local-machine-using-docker-and-ddev).  
* [Creating development environments for Drupal with DDEV](https://www.therussianlullaby.com/blog/creating-development-environments-for-drupal-with-ddev/).  
* [Docker, Docker-Compose and DDEV - Cheatsheet](https://www.therussianlullaby.com/blog/docker-docker-compose-and-ddev-cheatsheet/).  
* [Quick Deploy of Drupal 9 using DDEV (6 steps) - Gist in Github](https://gist.github.com/davidjguru/a95329e0ec5b084ac0852ad958da2a14).  


This is the summary of my test environment and the sum of hardware and software virtualization I perform:   

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


Let's get to work! First, You'll need some diverse environments for your comparative study.,



Variables:

![Memory Consumption in variables]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_two.png)  





Arrays:

![Memory Consumption in arrays]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_one.png)  





Objects:

![Memory Consumption in objects]({{ site.baseurl }}/images/davidjguru_playing_with_php_8_performance_three.png)  








  