---
layout: post
title: Development environments for Drupal with DDEV
permalink: /blog/creating-development-environments-for-drupal-with-ddev
published: true
date: 2019-05-18
author: davidjguru
categories: [Drupal & DevOps]
sitemap: true
---
| ![Picture from Unsplash, by @heapdump]({{ site.baseurl }}/images/davidjguru_drupal_8_ddev_image_from_unsplash_harddrive.jpg) |
|:--:|
| *Picture from Unsplash, user Patrick Lindenberg @heapdump* |


I always (or almost always) had worked on Drupal projects with the same stack of versions, so I only had one environment set up. Then, I started to have different versions (Drupal 6.x, Drupal 7 ... 8) at the same time for diverse projects and then I started playing with Docker. But now I have discovered ddev and a new game has started.

<!--more-->

## Introduction
I was reading in somewhere that there's two main kinds of developers: Those who works with a simple and unique stack locally (It requires a main environment with changes per project), and those who work with an encapsulated environment per each project, and it requires the use of tools for virtualization or containerization (and add another layer of complexity). 

Today I wanna talk about the second option: developers with a isolated environment per project, what kind of tools we can use for it and, of course, the role that DDEV has been ocupied in my life (and in my heart). 
But for this travel to the tool DDEV, we need remember (I'm sure you know about it) some previous concepts. 

Let's go.    

## OS Virtualization 
Originally, the problem was: **How to host various applications with different versions or diverse stacks in the same single server?** and there were three answers: 

1. Three physical machines.
1. A single physical machine and three virtual machines (Hardware Virtualization).
1. A single physical machine and three containers (Operating System Virtualization). 

And the last is the point for us: The Operating System Virtualization, the concept wich Docker is based and by extension DDEV as well. Docker was a kind of OS Virtualization based in LXC (Linux Containers). 
 
Let's see the basic scheme of the Docker Engine Virtualization Model: 

![Basic Scheme of Docker Engine Virtualization Model First Image]({{ site.baseurl }}/images/davidjguru_drupal_8_docker_engine_mockup_1.png)

Then, a container (or Docker Container) is a Logical Entity with a copy of the Process Table, the Network Interface and the File System Mount Point, related with the kernel of the OS Host where Docker is running. This is the reason why - *unlike in the case of virtualization via hardware where an OS Guest can be different from OS Host* - OS-Virtualization must maintain systems that share the same version of the kernel.

**Let's say in summary:**

1. The kernel of the Host's Operating System is shared across all the containers.
1. Each container is (initially) isolated from others existing containers. 
1. Docker virtualize the Operating System of the Host, not the Hardware.  

## Multi-Container applications with Docker
We now have a partial deployment performed through the equality 1 Application = 1 Docker container, but now in the next step, we see that a Drupal project = N applications = N Containers. **How to relate all this?** for that we entered into the use of Docker Compose, a tool to articulate the links and connections of all the Containers that we want to operate in an integrated way within the context of a project. 

Now defining the relationships between containers trought Docker Compose we could deploy applications from some containers.

![Development Structure based in Docker and Docker Compose]({{ site.baseurl }}/images/davidjguru_drupal_8_docker_engine_mockup_2.png)

**Concepts:**
1. Docker Engine: a set of software resources for work with Docker. Includes a server (Docker Daemon called dockerd, a Rest API and a client Docker CLI).  
1. Dockerfile: File with a description about how to build an Docker Image. 
1. Docker Image: Implementation of a Dockerfile as a template with the application.
1. Docker Hub: An external (and official) repository with availables Docker Images. [https://hub.docker.com/](https://hub.docker.com/){:target="_blank"}
1. Docker Container: A Logical Entity, a running instance of a Docker Image. 
1. Docker Compose:  A tool for defining and running multi-container Docker applications. 



## And now, enter DDEV
Well, that's how I was working lately until a good friend of mine (and sensei), [Pedro Cambra](https://twitter.com/pcambra){:target="_blank"} from [Cambrico](http://cambrico.net){:target="_blank"}, told me about DDEV and asked me to try it. From there everything for me has been joy :-D

DDEV Website: [https://www.drud.com](https://www.drud.com){:target="_blank"}

**What is DDEV?**

Well, as they explain: *Is an open-source, PHP development tool, built upon Docker. It can easliy create local hosting environments, and its server configurations can be version controlled. Originally meant for Drupal development, ddev easily can host Drupal, Wordpress, and GravCMS sites. Since it is based on Docker, ddev is compatible with Windows, Mac, and Linux.*

But for me, in my own words and in my bad English, DDEV is something like a pre-cooked solution to assemble Drupal projects and later, keep developing with them. Also release as Open Source Software under Apache License v2 in: [https://github.com/drud/ddev](https://github.com/drud/ddev){:target="_blank"}
You can see the Documentation here, DDEV Documentation: [https://ddev.readthedocs.io/en/stable/](https://ddev.readthedocs.io/en/stable){:target="_blank"}

**Advantages**

There are many operational advantages to using DDEV as a tool for building development environments, but I will summarize some of the main ones:

1. **Fast Setup:** Fast and Simple Setup. Once you have installed all the necesary stack (docker, ddev, etc.), It's extremely easy to initialize a new project based on PHP (Drupal in this case).
1. **Routing:** There is basic configuration between Containers provided by DDEV. This simplifies all the connections and access tasks between Containers or between user and application. 
1. **Sharing setups within a Team:** You can put the ddev configuration file per project under version control by git. And share it with your workmates. 
1. **Essentially,** you just have to worry about specifying some versions. DDEV is in charge of the rest. And with some more commands, you will not have to do anything more.  

**My Case**

I will explain myself taking as an example my current situation: At the moment, I have a company laptop with Windows 10, loaded with pre-configured corporate applications that I can not delete and that gives me problems when building a boot manager. So I've virtualized Ubuntu in virtualbox. In this OS Ubuntu 18.04 I have no resources associated with development...nothing! I don't have Apache, neither Composer, or Drush, or PHP...nothing...only the Docker Engine system mixed with DDEV. Just This. I have everything isolated in Containers, through DDEV. A DDEV structure (of multiple related containers) for each project.

So let's see a new iteration on the previous picture to understand what this construction entails for development in a local environment:

![Docker and DDEV Structure for Local Development]({{ site.baseurl }}/images/davidjguru_drupal_8_docker_engine_mockup_3.png)

## Setting up a Drupal 8 Site with DDEV

We are going to try to build a development environment and deploy a project based on Drupal 8 thanks to DDEV. Initially, we will start with OS Ubuntu and evidently, these steps should only be completed completely the first time (installation of Docker and DDEV).
The following iterations will be much faster with all the tools already installed and configured. Once you have the tools installed, steps 1, 2, 3, 4 and 5 will not be necessary anymore. 

**Remember:** everything we have seen here in this article is for local development (no production or live).

### 1- Preparing your sistem

```bash
sudo apt update
sudo apt install -y build-essential apt-transport-https ca-certificates jq curl software-properties-common file
```

### 2- Installing Docker

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y docker-ce
sudo chmod 666 /var/run/docker*
systemctl is-active docker
```

### 3- Installing Docker Compose

```bash
VERSION=$(curl --silent https://api.github.com/repos/docker/compose/releases/latest | jq .name -r)
DESTINATION=/usr/local/bin/docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/${VERSION}/docker-compose-$(uname -s)-$(uname -m) -o $DESTINATION
sudo chmod 755 $DESTINATION
docker-compose --version
```

### 4- Installing Linuxbrew

```bash
yes | sh -c "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install.sh)"
yes | test -d ~/.linuxbrew && eval $(~/.linuxbrew/bin/brew shellenv)
yes | test -d /home/linuxbrew/.linuxbrew && eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
yes | test -r ~/.bash_profile && echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.bash_profile 
echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.profile
```
### 5- Adding the vendor/package to brew & Installing DDEV 

```bash
brew tap drud/ddev && brew install ddev
```

### 6- Build a new Drupal project

```bash
mkdir projectname
cd projectname
ddev config --project-type php --php-version 7.3 
ddev composer create drupal-composer/drupal-project:8.x-dev --stability dev --no-interaction
ddev config --project-type drupal8
ddev exec drush site-install standard --site-name=projectname --account-name=admin --account-pass=admin --account-mail=mail@example.com --yes
ddev start
```

### 7- Install and enable some basic Drupal Modules for work

```bash
ddev composer require drupal/devel drupal/masquerade drupal/admin_toolbar
ddev exec drush en devel masquerade admin_toolbar webprofiler
ddev exec drush cr
sensible-browser http://projectname.ddev.local
```
### 8- Connect to Container and some commands

```bash
ddev ssh
ddev describe projectname
ddev list
```

## :wq!

What do you think about DDEV and its possibilities? You have to think that many unix commands shown above are totally scrollable to the ddev configuration file like hooks (pre and post formats), but I preferred to make it explicit here as an introduction.

Finally, I add some links of interest for you:

+ [Docker Simplified: A Hands-On Guide for Absolute Beginners](https://medium.freecodecamp.org/docker-simplified-96639a35ff36){:target="_blank"}
+ [DDEV, Docksal, and Lando: A Comparison](https://www.drupaleasy.com/blogs/ultimike/2018/03/ddev-docksal-and-lando-comparison){:target="_blank"}
+ [Docker Tutorial Series : Writing a Dockerfile](Docker Tutorial Series : Writing a Dockerfile){:target="_blank"}

Greetings. wq!    :-*

## PS; Post-Script: 50 Ways of Install Drupal
Thinking about putting together some of the various Drupal installation modes, I have created a repository for this. I have called this repository "50 ways to install Drupal", mainly for teaching purposes. It's a set of Installers for Drupal, a collection of craft scripts to launch various types of processes installing Drupal codebase.
[See the repo in gitlab](https://gitlab.com/davidjguru/50-ways-to-install-drupal){:target="_blank"}

I have started by building a script that performs all the steps above plus some additions (such as installing npm, Node, Angular and some more packages). 
It's a script to deploy a local development environment in Drupal - Headless projects with an uncoupled frontend and based on Angular.
[Angular & Drupal & DDEV](https://gitlab.com/davidjguru/50-ways-to-install-drupal/blob/master/installers/recipe_1_ADD_Angular_Drupal_DDEV){:target="_blank"} 

**Use:** Just for create Debian - based environments to work in Drupal - Headless projects (In this case, using Angular for the frontend). Launch your Terminal (Ctrl + alt + T), go to the folder and launch just: ```$ . recipe_1_ADD_Angular_Drupal_DDEV```

**Characteristics:**

+ **Search** for older versions of Docker and uninstall it.
+ **Stop** your Apache main server, to free the port 80.
+ **Update** your package list.
+ **Install** all the basic resources for Docker Engine in your host system: docker-ce, docker-compose, linuxbrew and ddev.
+ **Also install** a set of software packages in OS - Host: curl, jq, files, build-essential apt-transport-https ca-certificates software-properties-common
+ **Create** a new ddev environment with containers for Drupal, database, etc. (See 5 to see the container specifications).
+ **Build** a new Drupal-based project using ddev, Composer and Drush.
+ **Install and enable** some basic modules needs to work like devel, webprofiler, admin_toolbar and masquerade.
+ **Install** some more packages in the container: sqlite3, node, npm and Angular.
+ **Open** the new Drupal in Browser and goes to the container prompt.
+ **Ready to work**.


