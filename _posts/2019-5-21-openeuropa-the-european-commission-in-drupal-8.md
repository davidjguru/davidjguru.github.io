---
layout: post
title: OpenEuropa - The European Commission in Drupal 8
permalink: /blog/openeuropa-the-european-commission-in-drupal-8
published: true
date: 2019-05-21
categories: [Drupal]
sitemap: true
---
| ![Picture from Unsplash, by @peterlaster]({{ site.baseurl }}/images/davidjguru_drupal_8_openeuropa_from_unsplash_gargoyle.jpeg) |
|:--:|
| *Picture from Unsplash, user Pedro Lastra @peterlaster* |



“European Commission DIGIT initiative fostering open source adoption in consolidating the European Institutions web presence”...ummm what is this? I want to share -briefly- the process of installing a platform based on Drupal 8.
<!--more-->

## Introduction
Did you know that there is an initiative to standardize projects based on Drupal in the context of the European Union? It's called OpenEuropa and it's a set of tools and documentation ready to be download, installed and shared...released as OpenSource and part of the OpenEuropa Iniciative
[https://github.com/openeuropa](https://github.com/openeuropa){:target="_blank"}

This set of resources contains components such as a Task Runner based on Robo or a Visual Theme for Drupal and also a custom version of Drupal 8 called “Drupal Site Template” [https://github.com/openeuropa/drupal-site-template](https://github.com/openeuropa/drupal-site-template){:target="_blank"}
I have done some initial tests with this project and I want to leave here the installation guidelines. I took instructions from here and there and mixing them, I have managed to boot Drupal Site Template in my environments. 
Now I have a centralized recipe. I put it here, in case someone needs it. 


## Composing the environment

### Environment:

+ OS - Ubuntu 18.04
+ PHP - 7.3.5 (but the container will run with its own PHP 7.1). 

### Prerequisites:

+ Docker 
+ Docker Compose

### Installing Docker

```bash
sudo apt install -y build-essential apt-transport-https ca-certificates jq curl software-properties-common file
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y docker-ce
sudo chmod 666 /var/run/docker*
systemctl is-active docker
```

### Installing Docker Compose

```bash
VERSION=$(curl --silent https://api.github.com/repos/docker/compose/releases/latest | jq .name -r)`
DESTINATION=/usr/local/bin/docker-compose`
sudo curl -L https://github.com/docker/compose/releases/download/${VERSION}/docker-compose-$(uname -s)-$(uname -m) -o $DESTINATION`
sudo chmod 755 $DESTINATION`
docker-compose --version
```
## Installing OpenEuropa Drupal 8 Site

### First: Stop your Apache webserver

```bash
sudo /etc/init.d/apache2 stop
```

### Second: Clone the OpenEurope Drupal Project

```bash
git clone https://github.com/openeuropa/drupal-site-template.git
```

### Third: Run the project

```bash
cd drupal-site-template`
docker-compose up -d
docker-compose exec web composer install
```

![Docker inspect container to get the IP]({{ site.baseurl }}/images/davidjguru_drupal_8_openeuropa_3.png)


### Fourth: Get the ID of the web container and inspect it

```bash 
docker ps
docker container inspect IDCONTAINER
```
Tip: The IP from the container with /web inside (drupal_site_template_web_1) will have always the same ID. You only have to watch it once. 


### Fifth: Get the IP of the container and see it in browser

```bash
sensible-browser IPCONTAINER:8080
```

![Open Drupal in Browser]({{ site.baseurl }}/images/davidjguru_drupal_8_openeuropa_1.png)

### Sixth: Connect to the container

```bash
docker exec -it IDCONTAINER /bin/bash 
```

### Seventh: Run the installer / Task Runner

```bash
./vendor/bin/run toolkit:install-clean
```

### Eighth: Export configuration files from database to config/sync

```bash
./vendor/bin/drush cex
```

### Ninth: Visit your new project in

```bash
http://IPCONTAINER:8080/web 
```

### Happy Hacking! 

![Login Drupal OpenEuropa]({{ site.baseurl }}/images/davidjguru_drupal_8_openeuropa_2.png)

