---
layout: post
title: Development environments for Drupal with DDEV
permalink: /blog/creating-development-environments-for-drupal-with-ddev
published: true
date: 2019-05-18
categories: [Drupal]
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
We now have a partial deployment performed through the equality 1 Application = 1 Docker container, but now in the next step, we see that a Drupal project = N applications = N Containers. How to relate all this? for that we entered into the use of Docker Compose, a tool to articulate the links and connections of all the Containers that we want to operate in an integrated way within the context of a project. 

![Development Structure based in Docker and Docker Compose]({{ site.baseurl }}/images/davidjguru_drupal_8_docker_engine_mockup_2.png)

**Concepts:**
1. Docker Engine: a set of software resources for work with Docker. Includes a server (Docker Daemon called dockerd, a Rest API and a client Docker CLI).  
1. Dockerfile: File with a description about how to build an Docker Image. 
1. Docker Image: Implementation of a Dockerfile as a template with the application.
1. Docker Hub: An external (and official) repository with availables Docker Images. [https://hub.docker.com/](https://hub.docker.com/){:target="_blank"}
1. Docker Container: A Logical Entity, a running instance of a Docker Image. 
1. Docker Compose:  A tool for defining and running multi-container Docker applications. 



## And now, enter DDEV
Well, that's how I was working lately until a good friend of mine, [Pedro Cambra](https://twitter.com/pcambra){:target="_blank"} from [Cambrico](http://cambrico.net){:target="_blank"}, told me about DDEV and asked me to try it. From there everything for me has been joy :-D
ddev is an open-source, PHP development tool, built upon Docker. It can easliy create local hosting environments, and its server configurations can be version controlled. Originally meant for Drupal development, ddev easily can host Drupal, Wordpress, and GravCMS sites. Since it is based on Docker, ddev is compatible with Windows, Mac, and Linux.

I will explain myself taking as an example my current situation: At the moment, I have a company laptop with Windows 10, loaded with pre-configured corporate applications that I can not delete and that gives me problems when building a boot manager. So I've virtualized Ubuntu in virtualbox. In this OS Ubuntu 18.04 I have no resources associated with development...nothing! I don't have Apache, neither Composer, or Drush, or PHP...nothing...only the Docker Engine system mixed with DDEV. Just This. I have everything isolated in Containers, through DDEV. A DDEV structure (of multiple related containers) for each project.

So let's see a new iteration on the previous picture to understand what this construction entails for development in a local environment:

![Docker and DDEV Structure for Local Development]({{ site.baseurl }}/images/davidjguru_drupal_8_docker_engine_mockup_3.png)

[Here](https://gitlab.com/davidjguru/master-javascript/tree/js_Tips_and_Tricks_for_Console/js_Tips_and_Tricks_for_Console_Folder){:target="_blank"}
 In the file called "tips_tricks_console.js" you will find the examples.

## Setting up a Drupal 8 Site on DDEV



## :wq!

Greetings. wq!    :-*
