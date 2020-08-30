---
layout: post
title: Infrastructure as code with Terraform for Drupal
permalink: /blog/infrastructure-as-code-with-terraform-for-drupal
published: true
date: 2020-08-30
author: davidjguru
categories: [Drupal & DevOps]
sitemap: true
---
| ![Picture from Unsplash, by @gmmoreno]({{ site.baseurl }}/images/davidjguru_terraform_and_drupal_main.jpg) |
|:--:|
| *Picture from Unsplash, user [Gustavo Moreno, @gmmoreno](https://unsplash.com/@gmmoreno)* |

During this summer holiday, strange and so different, I tried to keep myself entertained with a thousand technical things (in addition to the daily needs of home and family) that I had on the TO*DO list. For example, I've been testing the Julia programming language to familiarize myself with the syntax and its approach. Or I've been studying the internal architecture of arrays built in C language within PHP, and I have also used tools I wanted to know, like in this case: Terraform.
<!--more-->
  
Terraform ([https://www.terraform.io](https://www.terraform.io/)) is an open-source infrastructure as code software tool I had on my list to use since one year ago, but multiple reasons and other tools came ahead and until this summer I couldn't get started. Essentially, Terraform allows the remote provisioning of infrastructure through the configuration of specific files adapted to each supplier (AWS, Google, Digital Ocean, Azure, etc) and each provisioning need. Ok.  

In this case I would like to perform provisioning tests on Digital Ocean droplets from a local Ubuntu-based environment to prepare tests in possible remote development environments. Let's see.
 


  
  ---------------------------------------------------------------------------------
  
  **Table of Contents**  
  <!-- TOC -->  
  [1- Installing Terraform](#1--installing-terraform)  
  [2- Configuring Terraform](#2--configuring-terraform)  
  [3- Preparing the Provider](#3--preparing-the-provider)  
  [4- Defining the Execution Goals](#4--defining-the-execution-goals)  
  [5- Destroying resources](#5--destroying-resources)  
  [6- :wq!](#7--wq)  
  <!-- /TOC -->
  
  -------------------------------------------------------------------------------

## 1- Installing Terraform 

Add the GPG key from HashiCorp:  
```
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
```

Now we're adding the official Linux repository:  
```
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
```

At last, update the info and install Terraform from the repository:  
```
$ sudo apt-get update
$ sudo apt-get install terraform
```

Verifying the installation: In order to check your Terraform installation, you can use these commands:  
```
$ terraform
$ terraform -help
$ terraform -version
```
And you will get some king of feedback from your prompt, something like:  
```
drupal@drupal-workshop:~$ terraform -version
Terraform v0.13.1
```
## 2- Configuring Terraform

### Getting an access token
Get an access token from Digital Ocean for your connections: 

![Getting access token from Digital Ocean]({{ site.baseurl }}/images/davidjguru_terraform_and_drupal_two.jpg)  

And generates the new token, giving write permissions:  

![Ask for a token from Digital Ocean]({{ site.baseurl }}/images/davidjguru_terraform_and_drupal_three.jpg)

### Loading the token in an environment variable

You can set the token to a environment variable just for the current session with the command:  
```
export DO_PAT="YOUR_ACCESS_TOKEN"
```
 Or maybe you want load the value in a more persistent way in your system, so you can set the token in your bashrc config file: 
 
```
$ cd ~ 
$ vim .bashrc
export DO_PAT="YOUR_ACCESS_TOKEN"
:wq!
```

Confirm the value using "echo $DO_PAT":  
```
drupal@drupal-workshop:~$ echo $DO_PAT
YOUR_ACCESS_TOKEN
```
## 3- Preparing the Provider

Create a directory for storing the configuration files of this test project and change your position to the newly created folder:  
```
$ mkdir ~/terraform_test
$ cd ~/terraform_test
```
Now, we're going to define the provider declaration file, wich will be the register for data related to the external hosting provider (Digital Ocean in this example).  

First, creating a new file called provider.tf with the required plugin and its version: 

```
terraform {
  required_providers {
    digitalocean = {
      source = "digitalocean/digitalocean"
      version = "1.22.2"
    }
  }
}
```

Also we'll add some more data about required variables and values used for the future connection to the provider: 

```
variable "do_token" {}
variable "pvt_key" {}

provider "digitalocean" {
  token = var.do_token
}

data "digitalocean_ssh_key" "davidjguru" {
  name = "davidjguru"
}
```
Saving the new provider definition file (:wq!) and run the initialization order for Terraform, just running:
```
$ terraform init
```
And you will get all the provider resources ready-to-work in your local installation:  

![Running the Terraform init provider]({{ site.baseurl }}/images/davidjguru_terraform_and_drupal_four.png)  

Get [the file for the provider from here](https://gitlab.com/-/snippets/2009971).  

## 4- Defining the execution goals 

vim new-drupal-droplet.tf  

You can see the most common slugs for definition of resources in this zone of the Digital Ocean API documentation, here: [developers.digitalocean.com/new-size-slugs-for-droplet-plan-changes](https://developers.digitalocean.com/documentation/changelog/api-v2/new-size-slugs-for-droplet-plan-changes/).  

![Align the ssh public key name]({{ site.baseurl }}/images/davidjguru_terraform_and_drupal_five.png)  

Launching from console the next instruction: 
```
$ terraform plan -var "do_token=${DO_PAT}" -var "pvt_key=$HOME/.ssh/id_rsa"
```

Getting the feedback: 
```
data.digitalocean_ssh_key.davidjguru: Refreshing state...

------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # digitalocean_droplet.new-drupal-droplet will be created
  + resource "digitalocean_droplet" "new-drupal-droplet" {
      + backups              = false
      + created_at           = (known after apply)
      + disk                 = (known after apply)
      + id                   = (known after apply)
      + image                = "ubuntu-20-04-x64"
      + ipv4_address         = (known after apply)
      + ipv4_address_private = (known after apply)
      + ipv6                 = false
      + ipv6_address         = (known after apply)
      + ipv6_address_private = (known after apply)
      + locked               = (known after apply)
      + memory               = (known after apply)
      + monitoring           = false
      + name                 = "new-drupal-droplet"
      + price_hourly         = (known after apply)
      + price_monthly        = (known after apply)
      + private_networking   = true
      + region               = "nyc1"
      + resize_disk          = true
      + size                 = "s-1vcpu-1gb"
      + ssh_keys             = [
          + "27328439",
        ]
      + status               = (known after apply)
      + urn                  = (known after apply)
      + vcpus                = (known after apply)
      + volume_ids           = (known after apply)
      + vpc_uuid             = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------
```

So now, we're going to execute the plan:  
```
$ terraform apply -var "do_token=${DO_PAT}" -var "pvt_key=$HOME/.ssh/id_rsa"
```

This will create the new droplet:  
![Execution of the Terraform Plan]({{ site.baseurl }}/images/davidjguru_terraform_and_drupal_six.png) 




More info  
https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs/resources/droplet  

## 5- Destroying resources 

```
$ terraform plan -destroy -out=new-drupal-droplet-destroy.tfplan -var "do_token=${DO_PAT}" -var "pvt_key=$HOME/.ssh/id_rsa"
```
Getting the response:  
```
------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # digitalocean_droplet.new-drupal-droplet will be destroyed
```
And then, apply the new destroy plan:  

```
$ terraform apply new-drupal-droplet-destroy.tfplan
```

```
Apply complete! Resources: 0 added, 0 changed, 1 destroyed.
```

## 9- :wq! 