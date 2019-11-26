---
layout: post  
title: Drupal Backend Workshop in Drupal Day Spain 2019 
permalink: /blog/drupal-backend-workshop-in-drupal-day-spain-2019
published: true  
date: 2019-11-26 
author: davidjguru
categories: [Drupal]  
sitemap: true
---
| ![Picture from Unsplash, by @ryanhafey]({{ site.baseurl }}/images/davidjguru_drupal_8_drupal_day_spain_2019_zaragoza_main.jpeg) |
|:--:|
| *Picture from Unsplash, user Ryan Hafey @ryanhafey* |

I write these lines (or I start them) just as I return from Drupal Day Spain 2019, which this year took place in the city of Zaragoza. I'm typing while I recreate in my head various anecdotes, details, conversations and above all, learning. 

It has been a very enriching meeting and I liked it very much from a technical point of view, but now I want to write mainly about what has been my personal contribution to the encounter: an introduction workshop to the backend of Drupal 8. 
<!--more-->

This year, the event has been organized in the city of Zaragoza, capital of the "autonomous community" (region with self-government in the Spanish state) of Aragon, in the north of the country. 
A very beautiful city that in winter accentuates even more its attractiveness: [Take a look of Zaragoza in the Flickr albums of the City Council](https://www.flickr.com/photos/zaragozaturismo/albums/72157662195031429).


| ![Group photo of the meeting Drupal Day Spain 2019]({{ site.baseurl }}/images/davidjguru_drupal_8_group_photo_drupal_day_spain_2019.jpeg) |
|:--:|
| *Group photo of the meeting Drupal Day Spain 2019 - Zaragoza, 23 November,2019.* |

## TL;DR 

1- Install VirtualBox in your laptop. [Download VirtualBox](https://www.virtualbox.org/wiki/Downloads).

2- Then, download this Virtual Machine and import it from VirtualBox. [Download the Virtual Machine ](https://drive.google.com/drive/folders/1DWTsw0Amzw2f-muGgK5OKo5HIhZZ0RQK)

3- Read the slides from the former folder and follow the steps. 

## Introduction
It seems that for a semester and with some continuity, I will be trying to explain how Drupal works in different places and environments. Due to an accumulation of circumstances, opportunities and total absence of sense of danger, between the last quarter of 2019 and the first quarter of 2020 I will be trying to transmit in the best possible way (those laughs) how Drupal works and how we can approach projects based on Drupal 8.

Well, **in my experience one of the main limitations in a workshop is the time devoted to configurations in general and in particular to some basic alignment of environments**, enough so that the fact of giving support in situ eat the time of practice and exercise. So thinking about it I was trying multiplatform solutions, in a pre-configured way and that allowed to assimilate quickly people with little experience in virtualization / containerization, as well as avoid all the tedious parts of common installations - configuration. You've got two hours for a workshop and you don't want to spend them explaining what Docker is and how to use it.

**But without much luck:** what was agile in deploying on the one hand, greatly limited the customization on the other. What ended up with a Drupal deployed in the web browser, then turned out to be a hell of a question of installations and command-line access. I needed something transversal, preconfigured, agile and with all the initial work already done. 
So as almost no existing solution convinced me for these purposes, I built my own: I decided to set up my own virtual machine for these activities, a drupal.ova ready to import into VirtualBox and start practicing, with everything you need as standard.


## The Drupal.ova 

## Installing VirtualBox

## Importing Drupal.ova

## Characteristics, values and parameters (Config)

### System
```bash
SO: Ubuntu 18.04.2 — amd64
Kernel version: 5.0.0–32-generic
Hard Disk: 13GB (dinámico)
RAM: 5'5 GB
Video Memory: 64MB
Language: Spanish, es_ES
Keyboard config: es_ES
Ubuntu login: user: drupal, password: drupal1$
Ubuntu machine name: drupal-workshop
VirtualBox 6.0.6 Guest Additions for Linux
```

### Environment
```bash
Apache web server: Apache/2.4.29 (Ubuntu)
MySQL server: 5.7
Access root: sudo mysql (direct access)
Access user: drupal_workshop, password: drupal1$
Login by prompt: mysql -udrupal_workshop -pdrupal1$
Database name: testdatabase
PHP 7.2.24
Drupal Core: 8.7.10
Composer (Global): 1.9.1
Drush: Drush Commandline Tool 9.7.1
Drupal Console version 1.9.4
```


## Tools and resources

### Tools
```bash
VSCodium: version 1.39.2
Firefox — 65.0
Chrome — 78.0.3904.87
Docker — version 19.03.5
Docker-Compose — docker-compose version 1.24.1
DDEV — ddev version v1.11.2
Text Editors: Gedit, VIM
Database client (graphic)  - MySQLWorkbench 6.3.8 (pre-configurated connection)
Screen Caption / Image edition: Shutter
```
### XDebug
```bash
zend_extension=xdebug.so
xdebug.remote_autostart = 1
xdebug.remote_enable = 1
xdebug.remote_handler = dbgp
xdebug.remote_host = 127.0.0.1
xdebug.remote_log = /tmp/xdebug_remote.log
xdebug.remote_mode = req
xdebug.remote_port = 9000
xdebug.max_nesting_level = 300
xdebug.idekey = VSCODE
```

### VSCodium
```bash
phpcs - 1.0.7
Debugger for Chrome - 4.12.1
PHP DocBlocker - 2.0.1
empty-indent 0.2.0
PHP Debug - 1.13.0
PHP Intelephense - 1.2.3
Composer 0.7.1
Twig Language 2 - 0.9.0
Drupal 8 Snippets - 0.0.2
Drupal 8 Javascript Snippets - 0.0.2
Drupal 8 Twig Snippets - 1.0.2
```

### Drupal Modules
```bash
admin_toolbar
devel
devel_generate
kint
webprofiler
```