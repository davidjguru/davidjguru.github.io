---
layout: post
title: N Linux commands for everyday life
permalink: /blog/n-linux-commands-for-everyday-life
published: false
date: 2020-09-18
author: davidjguru
categories: [Drupal & Commands]
sitemap: true
---
| ![Picture from Unsplash, by @lazycreekimages]({{ site.baseurl }}/images/davidjguru_terraform_and_drupal_main.jpg) |
|:--:|
| *Picture from Unsplash, user [Michael Dziedzic, @lazycreekimages](https://unsplash.com/@lazycreekimages)* |

During this summer holiday, strange and so different, I tried to keep myself entertained with a thousand technical things (in addition to the daily needs of home and family) that I had on the TO*DO list. For example, I've been testing the Julia programming language to familiarize myself with the syntax and its approach. Or I've been studying the internal architecture of arrays built in C language within PHP, and I have also used tools I wanted to know, like in this case: Terraform.
<!--more-->

Terraform ([https://www.terraform.io](https://www.terraform.io/)) is an open-source "infrastructure as code" software tool I had on my list to use since one year ago, but multiple reasons and other tools came ahead and until this summer I couldn't get started. Essentially, Terraform allows the remote provisioning of infrastructure through the configuration of specific files adapted to each supplier (AWS, Google, Digital Ocean, Azure, etc) and each provisioning need. Ok.  

In this case I would like to perform provisioning tests on Digital Ocean droplets from a local Ubuntu-based environment to prepare tests in possible remote development environments. Let's see.
 


  
  ---------------------------------------------------------------------------------
  
  **Table of Contents**  
  <!-- TOC -->  
  [1- Know your system (42 examples)](#1--know-your-system-42-examples)  
  + [1.1- Getting information from the system (19 examples)](#11--getting-information-from-the-system-19-examples)  
  + [1.2- Shutting down the system (7 examples)](#12--shutting-down-the-system-7-examples)  
  + [1.3-  Managing networks (16 examples)](#13---managing-networks-16-examples)  
  [2- Using files and folders (50 examples)](#2--using-files-and-folders-50-examples)  
  + [2.1- Basic usage (15 examples)](#21--basic-usage-15-examples)  
  + [2.2- Moving between directories (12 examples)](#22--moving-between-directories-12-examples)  
  + [2.3- Looking for resources (6 examples)](#23--looking-for-resources-6-examples)  
  + [2.4- Making copies and compressed files (17 examples)](#24--making-copies-and-compressed-files-17-examples)  
  [3- Configuring Terraform](#3--configuring-terraform)  
  [4- Preparing the Provider](#4--preparing-the-provider)  
  [5- Defining the Execution Goals](#5--defining-the-execution-goals)  
  [6- Getting a graphical review](#6--getting-a-graphical-review)  
  [7- Destroying resources](#7--destroying-resources)  
  [8- Read more](#8--read-more)  
  [9- :wq!](#9--wq)  
  <!-- /TOC -->
  
  -------------------------------------------------------------------------------


# 1- Know your system (42 examples)
**Note:** Commands to getting information about your own Linux system. 

## 1.1- Getting information from the system (19 examples)
1. **:~$** cat /etc/*release **# How to know what Linux distro is.**
1. **:~$** arch **#​ Will show your system hardware architecture.**   
1. **:~$** uname -r **# Will show the kernel version in use.**     
1. **:~$** dmidecode -q **# Will show all the hardware elements present in your system.**  
1. **:~$** hdparm -i /dev/hda **#​ Will show the features of the selected hard disk.**   
1. **:~$** cat /proc/cpuinfo **# ​Will show information about your CPU.**   
1. **:~$** cat /proc/meminfo **# Will verify the RAM memory comsuption in your system.**        
1. **:~$** free -m **# Will show the current status and the use of RAM memory in your sistem.**       
1. **:~$** cat /proc/net/dev **#​ Will show the network adapters and its statistics.**       
1. **:~$** cat /proc/mounts **#​ Will show the mounted filesystem in your system.**     
1. **:~$** lspci -tv **#​ Will show all the present PCI devices in your system.**    
1. **:~$** lsusb -tv **# ​ Will show all the connected USB devices to your system.**        
1. **:~$** top **#​ Will show the Linux Task using the max value of CPU.**    
1. **:~$** date **# Will show the current date in your system.**  
1. **:~$** cal 2020 **#​ Will show the whole calendar for the introduced year.**     
1. **:~$** cal 03 2020 **#​ Like the previous, but only will show the month of the selected year.**    
1. **:~$** man ping **# Will show pages from the help manual for a command, ping in this example.**  
1. **:~$** vmstat -s **# Get a report about the memory comsuption in your system.**  
1. **:~$** free -m **# Get a short report about memory comsuption.**  

## 1.2- Shutting down the system (7 examples)
1. **:~$** reboot **# Just another command to restart your system by prompt.**        
1. **:~$** last reboot **#​ Show a list with the last restarts.**        
1. **:~$** logout **# It will close your current sesion.**         
1. **:~$** halt **# Just other way for shutdown your system by prompt.**        
1. **:~$** shutdown -h 18:55 & **#​ It will planificate the shutdown in the marked time.**          
1. **:~$** shutdown -c **# Stops a planified shutdown of the system.**         
1. **:~$** shutdown -r now **# It will restart your system just now.**          

## 1.3-  Managing networks (16 examples)
1. **:~$** ifconfig eth0​ **# Will show an Ethernet network card configuration.**    
1. **:~$** ifup eth0​ **# Will enable an eth0 interface.**   
1. **:~$** ifdown eth0​ **# Will disable an eth0 interface.**    
1. **:~$** ifconfig eth0 192.168.0.1 netmask 255.255.255.0​ **# Will config an IP direction.**      
1. **:~$** dhclient eth0​ **# Will enable an eth0 interface with dhcp mode.**      
1. **:~$** route -n​ **# Will show a route table.**      
1. **:~$** route add -net 0/0 gw I ​ GATE_NAME​ **# Will config an access gate.**       
1. **:~$** route del 0/0 gw ​ IP_ADRESS **# Will disable an static IP.**    
1. **:~$** hostname​ **# Will show the system hostname.**       
1. **:~$** ip link show​ **# Will show the current status of the interfaces.**    
1. **:~$** mii-tool eth0​ **# Will show the link status of 'eth0'.**    
1. **:~$** ethtool eth0​ **# Will show statistics from the eth0 network card.**    
1. **:~$** netstat -tup​ **# Will show all active network connections.**    
1. **:~$** netstat -tupl​ **# Will show all the listening services in networking and their PIDs.**    
1. **:~$** iwlist scan​ **# Will show all the wireless networks.**   
1. **:~$** iwconfig eth1​ **# Will show the configuration of a wireless network card.**     


# 2- Using files and folders (50 examples)  
**Note:** Basic commands when you're working with files and folders. 

## 2.1- Basic usage (15 examples)  
1. **:~$** mkdir name_folder **# Will create a new folder with name.** 
1. **:~$** mkdir folder1 folder2 **# Will create two folders in a same instruction.**    
1. **:~$** du -ma | sort -nr | head -n 20 **# List 20 largest files and folders in current directory.**   
1. **:~$** rmdir my_folder  **# It will delete the my_folder directory, only if empty.**  
1. **:~$** rm -rf my_folder **# It will delete the my_folder directory and all its content.**  
1. **:~$** mv old_folder new_folder **# It will move or rename a folder with another.**  
1. **:~$** cp file_1 **# Copy the file_1.**    
1. **:~$** cp file_1 file_2 **# It will copy file_1 file_2 will copy the files simultaneously.**  
1. **:~$** cp -a folder1 **# It will copy a whole folder .**  
1. **:~$** cp -a folder1 folder2 **# It will copy the folders simultaneously.**  
1. **:~$** ln -s file link_tofile **# It will create a symbolic link origin/destiny.**    
1. **:~$** ln file link_tofile **# It will create a fisical link origin/destiny.**  
1. **:~$** file dump_file_database.sql **# Returns the (true) type of file.**  
1. **:~$** rsync -rtv /custom/hello/ /drupal/hello/ **# Copy recursive with timestamps and verbose**  
1. **:~$** rsync -avzh --progress --stats /home/drupal/ABCD/ /home/drupal/workspace/EFGH/ **# Copy recursive with links and symbolic links, compressing, verbose, with human info and reporting progress.**


## 2.2- Moving between directories (12 examples)
1. **:~$** cd /home/user​ **# Moves to the destiny folder.**
1. **:~$** cd .. **# Moves back to the previous level.**
1. **:~$** cd ../..**# Moves back two levels.**
1. **:~$** cd​ **# Moves to the main folder of this unity.**
1. **:~$** cd ~user **# Goes to the personal folder of the marked user.**
1. **:~$** cd – **# Goes just to the former directory you was .**
1. **:~$** pwd **# Return the name of the current folder.**
1. **:~$** ls **# Show all resources in the current folder.**
1. **:~$** ls -l **# List all resources in the current folder with user, groups and permissions.**
1. **:~$** ls -a **# Show all resources in the current folder with the hidden files.**
1. **:~$** ls -la // ll **# Get a complete list of resources including hidden with all the data.**
1. **~$** tree **# Show all content in a folder in a recursive way. Requires sudo apt-get install tree .**

## 2.3- Looking for resources (6 examples)
1. **:~$** find / -name filename **# Will search the file or folder starting by the root of system.**  
1. **:~$** find / -user username **# Will look for files and folders owned by the username​.**  
1. **:~$** find /home/user -name \*.bin **# Will search all files with .bin extension in the folder.** 
1. **:~$** find /usr/bin -type f -mtime -5 **#: Will search files created or changed from last five days.​**  
1. **:~$** whereis firefox **# Will show the location of the binary file of firefox.**  
1. **:~$** which firefox **# Will show the whole path to the marked binary / executable file.**  

## 2.4- Making copies and compressed files (17 examples)  
1. **:~$** rsync -rtv /modules/custom/hello_world /workspace/drupal/hello_world  **# Sync folders.**  
1. **:~$** dump -0aj -f /tmp/home0.bak ​ /home​ **#Creates a backup copy of the /home directory.**  
1. **:~$** dump -1aj -f /tmp/home0.bak ​ /home​ **# Same as previous but generating incremental copy.**  
1. **:~$** find ​ /home/user​ -name ‘*.​ txt​ ’ | xargs cp -av –target-directory=​ /home/backup/
–parents **# Will locate and copy all files with .txt extension from origin to destiny.**  ​   
1. **:~$** mkisofs /dev/cdrom > file.iso​ **# Will create an ISO image from a CD.**   
1. **:~$** mkisofs /dev/cdrom | gzip > fileISO.gz​ **# Will create a compressed image ISO from a CD.**  
1. **:~$** cdrecord -v dev=/dev/cdrom file.iso​ **# Will burn a CD with an ISO image.**  
1. **:~$** gzip -dc fileISO.gz | ​ cdrecord dev=/dev/cdrom **# Will burn a CD with a compressed iso image.**  
1. **:~$** tar -Puf backup.tar /home/user​ **# Will create an incremental backup.**  
1. **:~$** tar -czvf compressed.tar.gz /folder/to/compress/ **# Will compress a folder tar.gz format.**  
1. **:~$** tar -xzvf file.tar.gz **# Will decompress a compressed file with tar.gz extension.**  
1. **:~$** tar -cvf compressed.tar /dir/to/compress/ **# Will compress folder with tar format.**  
1. **:~$** tar -xvf compressed.tar **# Decompress (or untar) a compressed file with tar format.**  
1. **:~$** gzip -9 index.php **# Will compress a file using gzip format.**  
1. **:~$** gzip -d index.php.gz **# Decompress a file with gzip format.**  
1. **:~$** zip file.zip /folder **# Will compress a folder using zip format.**  
1. **:~$** unzip file.zip **# Unzip a compressed file with zip format.**  

# 3- Groups, users and permissions.  

# 4- Managing packages (26 examples)  

## 4.1- .DEB -Debian, Ubuntu, Mint, and other derivatives- (4 examples)  
1. **:~$** dpkg -i ​ package.deb​ **# Will install or will update a .deb package.**     
1. **:~$** dpkg -r ​ package.deb​ **# Will delete a certain .deb package in your system.**  
1. **:~$** dpkg -s ​ package.deb​ **# Will show information about the .deb package installed in your system.**     
1. **:~$** dpkg -l​ **# Will show all the .deb packages installed in your sistem.**     

## 4.2- Using APT (8 examples)  
1. **:~$** apt-get install ​ packagename **# Will install or update a certaing package .deb.**  
1. **:~$** apt-cdrom install packagename​ **# Will install or update a .deb package from a cdrom.**  
1. **:~$** apt-get update​ **# Will update the installed packages list.**  
1. **:~$** apt-get upgrade​ **# Will update all the instaled packages.**  
1. **:~$** apt-get remove packagename **# Will delete the selected .deb package.**  
1. **:~$** apt-get check​ **# Will verify the correct management of the dependencies.**  
1. **:~$** apt-get clean​ **# Will clean the cache from the downloaded packages.**  
1. **:~$** apt-cache search php7.3 | grep php7.3 **# Looking for a package with a certain version.**  

## 4.3- .RPM -Red Hat, Fedora, SUSE, etc- (10 examples)  
1. **:~$** rpm -ivh package.rpm​ **# Will install a rpm package.**  
1. **:~$** rpm -ivh –nodeeps paquete.rpm​ **# Will install a rpm package ignoring dependencies.**  
1. **:~$** rpm -U package.rpm​ **# Will update a rpm package without change configurations in files.**       
1. **:~$** rpm -F package.rpm​ **# Will update a certain rpm package if is installed.**  
1. **:~$** rpm -e ​ package.rpm​ **# Will delete the marked rpm package.**  
1. **:~$** rpm -qa​ **# Will show all the rpm packages installed in the system.**  
1. **:~$** rpm -qi ​package **# Will show info about a specific package.**   
1. **:~$** rpm -q ​ package​ –changelog​ **# Will show the revision history of a rpm package.**  
1. **:~$** rpm –checksig ​ package.rpm​ **# Will verify the integrity of a rpm package.**   
1. **:~$** rpm -qa gpg-pubkey​ **# Will verify the integrity of all the rpm installed packages.**  

## 4.4- YUM -Red Hat, Fedora and others- (4 examples)  
1. **:~$** yum install package​ **# Will download and install a certain rpm package.**     
1. **:~$** yum localinstall ​package.rpm​ **# Will install a rpm package resolving all its dependencies.**   
1. **:~$** yum update package.rpm​ **# Will update all the rpm packages in the system.**  
1. **:~$** yum update package **# Will update a certain rpm package.**  



# 5- Working in projects (29 examples)  

## 5.1- Applying style reviews with phpcs (6 examples)  
1. **:~$** phpcs -i **# Will show all the installed coding standards.**  
1. **:~$** phpcs --standard=Drupal -e **# Will list all the sniffs inside a coding standard.**     
1. **:~$** phpcs --standard=Drupal ./HumanstxtSettingsForm.php **#​ Review a php file under phpcs.**   
1. **:~$** phpcs --standard=DrupalPractice ./HumanstxtSettingsForm.php  **# Same but with another coding standard.**   
1. **:~$** phpcs --report=diff /path/to/code **# Will generate a patch file from a style diff.**  
1. **:~$** phpcbf /path/to/code **# Will repair the file.**   

## 5.2- Building projects using [DDEV](https://ddev.readthedocs.io/en/stable/) (8 examples)  
1. **:~$** ddev version **# Check your DDEV installed version.**  
1. **:~$** ddev launch  **# Launch in browser your Drupal site.**  
1. **:~$** ddev logs **# Show logs mixing nginx and php-fpm. Works in a stopped container.**  
1. **:~$** ddev logs -f **# Same as former, but following logs in real-time.**  
1. **:~$** ddev ssh **# Connecting to the web container with ssh.**  
1. **:~$** ddev ssh -s db **# Connecting to the database container using ssh.**  
1. **:~$** ddev export-db --gzip=false > /temp/db.sql **# Returns a dump file in the selected folder.**  
1. **:~$** ddev import-db --src=./backups/dump_02062020.sql.gz **# Load a compressed dump file in the default DDEV database called 'db'.**


## 5.3- Version Control with Git (15 examples)  
1. **:~$** git remote show origin **#Returns the data from the remote repository named 'origin'.**  
1. **:~$** git pull origin your_branch --allow-unrelated-histories **# Repairs fatal: refusing to merge unrelated histories.**  
1. **:~$** git diff stash@{0} your_branch > getting_your_patch.txt **# Getting a patch between recent stashed code and last status of your_branch.**  
1. **:~$** git log --date=rfc -n 20 --oneline --format="%h %<(70,trunc)%s - [%<(20,trunc)%aN] - %ad" **# Getting the last 20 commits showing its hash, message, author and time.**    
1. **:~$** git log --oneline --decorate --graph --all  **# Show all the branches in the tree format history with pointers.**  
1. **:~$** git branch --sort=-committerdate **#Sort branches by commit date.**  
1. **:~$** git checkout - **# Move to a previous branch.**  
1. **:~$** git checkout @{-N} **# Move to N-previous branch.**  
1. **:~$** git status -sb **# Getting info from a repository using a shorten output.**  
1. **:~$** git config --get remote.origin.url **# Getting info about the remote URL direction of a repository.**
1. **:~$** git -C /path/your/project/ log **# Getting info from a repository without being in it.**  
1. **:~$** git -C /path/your/project/ status **# Execute some git commands also without being in it.**     
1. **:~$** git checkout develop README.md **# Execute merge over changes in the same file from different branches.**   
1. **:~$** git diff master develop README.md **# Show all the diff between the same file from two different branches.**  
1. **:~$** git push origin ccccc:master **# With commits a, b, c, d. Will push c, b a to remote.**  

## 5.4- Drupal CLI using Drush or Drupal Console (Drupal Console seems to be closed)  
1. **:~$** drupal cect application --module="managing_activities" --optional-config --remove-uuid --remove-config-hash **# Export the config of an specific content type as optional.**  
1. **:~$** drush -l sitename cr **#Clear cache only for a site in a multisite Drupal installation.**  
