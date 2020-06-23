---
layout: post  
title: Drupal Backend Workshop in Drupal Day Spain 2019 
permalink: /blog/drupal-backend-workshop-in-drupal-day-spain-2019
published: true  
date: 2019-11-26 
author: davidjguru
categories: [Drupal Community]  
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
In advance: 
- Yes, I know Docker and Docker-Compose.
- Yes, I know how to use Docker and Docker-Compose. 
- Yes, I know pre-cooked Docker-based solutions like Lando or DDEV.
- This workshop is not for you or me.
- This workshop is free (freedom). 
- This workshop is for people with different environments (Windows, Linux, Mac), knowledge and experience (including case = zero).
- This workshop requires no time wasted on system and environment configurations and adjustments. 
- This workshop is for people not involved with Drupal to practice quickly and be motivated to use Drupal in their daily life.

Therefore, I concluded that it was more optimal to use VirtualBox directly. Even if it seems old or vintage or an ugly solution ¯\_( ツ )_/¯ . But as long as we are not contradicted by the results of the experience, it is the most agile option.

Well, the system chosen to be able to carry out a simple and agile alignment of environments is that of Hardware Virtualization, and specifically VirtualBox (https://www.virtualbox.org+), a virtualization software prepared to run the virtual machine that I have prepared to practice. The deal is as follows: you agree to install this software on his computer and I make sure that from there you will have a Drupal clean and ready to practice with it.
    
## Installing VirtualBox
You will only need to initially download a suitable version of VirtualBox, the one that matches your operating system. You can go here and see the available list, selecting the one that matches your OS: [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads).
If you already know how it's going, I'll give you links to specific download sections:

* Latest versions of Ubuntu (18.04, 18.10, 19.04): [https://download.virtualbox.org/virtualbox/6.0.14/virtualbox-6.0_6.0.14-133895~Ubuntu~bionic_amd64.deb](https://download.virtualbox.org/virtualbox/6.0.14/virtualbox-6.0_6.0.14-133895~Ubuntu~bionic_amd64.deb)
* Debian 10: [https://download.virtualbox.org/virtualbox/6.0.14/virtualbox-6.0_6.0.14-133895~Debian~buster_amd64.deb](https://download.virtualbox.org/virtualbox/6.0.14/virtualbox-6.0_6.0.14-133895~Debian~buster_amd64.deb)
* Debian 9: [https://download.virtualbox.org/virtualbox/6.0.14/virtualbox-6.0_6.0.14-133895~Debian~stretch_amd64.deb](https://download.virtualbox.org/virtualbox/6.0.14/virtualbox-6.0_6.0.14-133895~Debian~stretch_amd64.deb)
* Windows, in general, in bulk: [https://download.virtualbox.org/virtualbox/6.0.14/VirtualBox-6.0.14-133895-Win.exe](https://download.virtualbox.org/virtualbox/6.0.14/VirtualBox-6.0.14-133895-Win.exe)
* And if you have a specific problem or need, here you have installation instructions [https://www.virtualbox.org/manual/ch02.html](https://www.virtualbox.org/manual/ch02.html) and here you have a user manual (RTFM!) available: [https://download.virtualbox.org/virtualbox/6.0.14/UserManual.pdf](https://download.virtualbox.org/virtualbox/6.0.14/UserManual.pdf).

## Importing Drupal.ova
Next, It's time to install the new virtual machine, a heavy file (about 5GB at the time of writing these lines) with .ova extension that once imported from your VirtualBox installation, will create a complete practice environment.
You can download it here: [https://drive.google.com/drive/folders/1DWTsw0Amzw2f-muGgK5OKo5HIhZZ0RQK?usp=sharing](https://drive.google.com/drive/folders/1DWTsw0Amzw2f-muGgK5OKo5HIhZZ0RQK?usp=sharing)
(if at the time of access you see the empty folder, is that must be importing a new version of the virtual machine, wait a little, just a pair of minutes).
And once downloaded, import it from the VirtualBox interface.

| ![Drupal.OVA wallpaper]({{ site.baseurl }}/images/davidjguru_drupal_8_virtualbox_wallpaper.png) |
|:--:|
| *Drupal.ova wallpaper within the Virtual Machine imported from VirtualBox.* |

## Characteristics, values and parameters (Config)
It has been built with a minimum structure but enough to practice with Drupal, starting with an Ubuntu operating system on which has been mounted a LAMP environment (Linux, Apache, MySQL and PHP) also configured at the level of Apache modules, VirtualHost, database and the installation of Drupal itself.

Similarly, if you want to include it in the training purposes, has been installed virtualization software based on Docker: Docker, Docker-Composer and DDEV for the generation of pre-cooked environments Drupal - Docker. You will be able to practice with Docker containers in this Virtual Machine.

The operating system consists of an Ubuntu distribution with a limited hardware but able to run it with relative ease. With an allocation of RAM that does not cause too many problems to the host system and that can be assigned in laptops with something of antiquity, as well as the video memory or the hard disk with dynamic reserve. Keyboard and language configured for a target accustomed to Spanish (sorry, the target is people from the spanish state, but you can change this config values) and you'll have the disk of extra features of VirtualBox already installed (you can open a second screen if you have an extra monitor connected, for example).

**In the next level we have a classic LAMP environment** (selected Apache before Nginx for training purposes), with MySQL as database engine, PHP at 7.2 and the three most frequent tools to work with Drupal on a daily basis: Composer, Drush and Drupal Console).
You will have access to the project folder called 'drupal.localhost' in the path: /var/www/html/drupal.localhost. 
And from there you can already use the Drush and Drupal Console commands (they are already registered as aliases from the .bashrc of /home).
Likewise, from the web browser you will be able to access Drupal (Apache rises only as a service when the system boots) directly in the address: drupal.localhost. 
![Drupal 8 Workshop first screen]({{ site.baseurl }}/images/davidjguru_drupal_8_workshop_vm_1.png)

The Drupal 8 website was created through Composer and Drush with the following instructions, **so the access data to Drupal will be: admin / admin**.

```bash 
composer create-project drupal-composer/drupal-project:8.x-dev drupal.locahost \
 - stability dev \
 - no-interaction \
&& cd drupal.localhost \
&& drush site-install standard \
--db-url='mysql://drupal_workshop:drupal1$@localhost/testdatabase'\
--site-name='Drupal Workshop' \
--account-name=admin \
--account-pass=admin \
--account-mail=yourmail@mail.com \
--locale=en \
--yes
```
As tools, I have installed the version of VSCode that is compiled without telemetry (VSCodium does not send your usage data to Microsoft) but at the interface and extension level there are no significant differences with VSCode. Also two web browsers, the whole structure to run Docker and Docker Composer and in a complementary way DDEV to work with pre-cooked containers of fast deployment in local environments. Finally, two text editors (Gedit - visual- and Vim -prompt-) and a MySQLWorkbench graphical database client, for the reason of not scaring people who enter Drupal too much- with prompt and commands.

### System
```
SO: Ubuntu 18.04.2 — amd64
Kernel version: 5.0.0–32-generic
Hard Disk: 13GB (dinámico)
RAM: 5.5 GB
Video Memory: 64MB
Language: Spanish, es_ES
Keyboard config: es_ES
Ubuntu login: user: drupal, password: drupal1$
Ubuntu machine name: drupal-workshop
VirtualBox 6.0.6 Guest Additions for Linux
```

### Environment
```
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
```
VSCodium: version 1.39.2
Firefox — 65.0
Chrome — 78.0.3904.87
Docker — version 19.03.5
Docker-Compose — docker-compose version 1.24.1
DDEV — ddev version v1.11.2
Text Editors: Gedit, VIM
Database client (graphic)  - MySQLWorkbench 6.3.8
- with a pre-configurated connection to the database - 
Screen Caption / Image edition: Shutter
```
### XDebug
```
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
```
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
```
admin_toolbar
devel
devel_generate
kint
webprofiler
```

### Extra
I have given Drupal a "HelloWorld"-style custom module created in ```/web/modules/custom```, with the name *"hello_world"*, already installed, with a routing defined to /hello-world, that is: ```drupal.localhost/hello-world```, where it shows a message **"Hello World First Route"**.

![Drupal 8 Workshop second screen]({{ site.baseurl }}/images/davidjguru_drupal_8_workshop_vm_2.png)


In a complementary way it has a pair of breakpoints already placed in the answer of that controller, to observe directly the mechanics of debugging in Drupal from VSCodium:


![Drupal 8 Workshop third screen]({{ site.baseurl }}/images/davidjguru_drupal_8_workshop_vm_3.png)