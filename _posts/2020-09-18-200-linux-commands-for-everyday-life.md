---
layout: post
title: 200 Linux commands for everyday life
permalink: /blog/200-linux-commands-for-everyday-life
published: true
date: 2020-09-19
author: davidjguru
categories: [Drupal & Commands]
sitemap: true
---
| ![Picture from Unsplash, by @lazycreekimages]({{ site.baseurl }}/images/davidjguru_drupal_n_linux_commands_main.jpg) |
|:--:|
| *Picture from Unsplash, user [Michael Dziedzic, @lazycreekimages](https://unsplash.com/@lazycreekimages)* |

For a long time I had the idea of gathering somewhere centrally the most common Linux commands in these years of technology work. Looking back, I see that my relationship with the software in a professional way (I was previously a trainee) began in 2007, about thirteen years ago. Since then, I have always been within Linux environments and above all, in Ubuntu / Debian in particular. In some moments I have had to manage CentOS / RHEL and in some laptops I still have Fedora. But Debian / Ubuntu / L-Ubuntu were always my options (and still are).  
<!--more-->

So I have compiled several files exported from the history command, some interesting utilities and I have tried to gather them all in this post, mainly those that can be a useful, fast and simple solution in the day to day. 
 


  
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
  [3- Groups, users and permissions](#3--groups-users-and-permissions)  
  [4- Managing packages (26 examples)](#4--managing-packages-26-examples)  
  + [4.1- .DEB -Debian, Ubuntu, Mint, and other derivatives- (4 examples)](#41--deb--debian-ubuntu-mint-and-other-derivatives--4-examples)  
  + [4.2- Using APT (8 examples)](#42--using-apt-8-examples)  
  + [4.3- .RPM -Red Hat, Fedora, SUSE, etc- (10 examples)](#43--rpm--red-hat-fedora-suse-etc--10-examples)  
  + [4.4- YUM -Red Hat, Fedora and others- (4 examples)](#44--yum--red-hat-fedora-and-others--4-examples)  
  [5- Working in projects (29 examples)](#5--working-in-projects-29-examples)  
  + [5.1- Applying style reviews with phpcs (6 examples)](#51--applying-style-reviews-with-phpcs-6-examples)  
  + [5.2- Version Control with Git (15 examples)](#52--version-control-with-git-15-examples)  
  + [5.3- Docker Engine Related (Docker, Compose, Swarm)]()  
  + [5.4- Building projects using [DDEV](https://ddev.readthedocs.io/en/stable/) (8 examples)](#54--building-projects-using-ddevhttpsddevreadthedocsioenstable-8-examples)  
  + [5.5- Drupal CLI using Drush or Drupal Console](#55--drupal-cli-using-drush-or-drupal-console)
  [6- :wq!](#6--wq)  
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


# 2- Using files and folders (53 examples)  
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

## 2.3- Looking for resources (9 examples)
1. **:~$** grep -i "string_to_search" filename **# Search a specific string in a single file in case insensitive.**  
1. **:~$** grep "beginning*end" filename **# Search string with RegExp over a single file.**  
1. **:~$** grep  -iw "word" filename **# Now we're looking for a word in case insensitive.**
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

# 3- Groups, users and permissions (15 examples)

## 3.1- Users and Groups (8 examples)
1. **:~$** whoami **# Return what is your current user.**  
1. **:~$** groupadd new_group_name **# Creates a new user group in the system.**  
1. **:~$** useradd new_user_name **#Creates a new user in the system.**  
1. **:~$** useradd -c “Comment to ​new_user_name” -g group_name -d /path/to/new/home -s /bin/bash​ new_user **# Creates new user with more data.**  
1. **:~$** cat /etc/passwd **# Show all the users created in the system.**
1. **:~$** cat /etc/group​ **# Show all the groups from the system.**
1. **:~$** passwd ​ user_name **# Allows change the password for the user.**
1. **:~$** chage -E ​ 2020-12-31 user_name **# Set expiration date for a user's password.**

## 3.2- Permissions and ownership (7 examples)
1. **:~$** chown user filename **# Will change the owner of the file.**  
1. **:~$** chown -R user:group foldername **# Changes user and group for a whole folder in recursive mode.**  
1. **:~$** chmod -R 775 foldername **# Gives permissions 775 to a folder, recursive.**  
1. **:~$** ls -la / ll **# Shows a list with all the content in a location, with permissions.**  
1. **:~$** lsattr **# Will show all the special attributes of the content.**
1. **:~$** chattr +i filename **# Convert the file as inmutable. Can't be deleted o altered.**
1. **:~$** chattr +s filename **# Allows a file can be deleted in a safe way.**



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



# 5- Working in projects (54 examples)  

## 5.1- Applying style reviews with phpcs (6 examples)  
1. **:~$** phpcs -i **# Will show all the installed coding standards.**  
1. **:~$** phpcs --standard=Drupal -e **# Will list all the sniffs inside a coding standard.**     
1. **:~$** phpcs --standard=Drupal ./HumanstxtSettingsForm.php **#​ Review a php file under phpcs.**   
1. **:~$** phpcs --standard=DrupalPractice ./HumanstxtSettingsForm.php  **# Same but with another coding standard.**   
1. **:~$** phpcs --report=diff /path/to/code **# Will generate a patch file from a style diff.**  
1. **:~$** phpcbf /path/to/code **# Will repair the file.**   

## 5.2- Version Control with Git (20 examples)  
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
1. **:~$** git push origin ccccc:master **# With commits a, b, c, d. Will push c, b, a, to remote.**  

## 5.3- Docker Engine Related: Docker, Compose, Swarm (15 examples)
See more commands about Docker Engine at [www.therussianlullaby.com/docker-cheatsheet/](https://www.therussianlullaby.com/blog/docker-docker-compose-and-ddev-cheatsheet/).  
1. **:~$** systemctl is-active docker **# Checks if Docker is active or not.**  
1. **:~$** docker version **# Gets a whole report about your Docker Engine installation.**  
1. **:~$** docker image ls **# Gets a list of all the images.**  
1. **:~$** docker rmi $(docker image ls -q)  **# Deletes images by its ID.**  
1. **:~$** docker image prune **# Removes unusued images.**  
1. **:~$** docker exec idcontainer unixcommand  **# Executes a command inside a running container.**   
1. **:~$** docker exec -it IDCONTAINER /bin/bash **# Connect to the Prompt of a Container.**  
1. **:~$** docker cp db/dump.sql IDCONTAINER:/tmp/dump.sql **# Copying files from Local to Remote Docker Container.**  
1. **:~$** docker cp IDCONTAINER:/tmp/dump_test.sql ./db **# Copying files from Docker Container to local.**  
1. **:~$** docker system prune -f **# Remove unusued data and clean the Docker System.**  
1. **:~$** docker stats --all --format "table \t\t" **# Show stats about the running containers with formatted output.**  
1. **:~$** docker-compose logs -t -f ALIAS **# Get the Docker container log with direct connection.**  
1. **:~$** docker-compose stop && docker-compose up --build --force-recreate **# Stops, rebuilds and relaunchs.**  
1. **:~$** docker service create --replicas 3 --name my-service -p 8080:80 --network net-end my-app **# Swarm: Creates new service with params.** 
1. **:~$** docker service scale my-service=6 **# Swarm: updating service up to six nodes.** 

 
## 5.4- Building projects using [DDEV](https://ddev.readthedocs.io/en/stable/) (8 examples)  
1. **:~$** ddev version **# Check your DDEV installed version.**  
1. **:~$** ddev launch  **# Launch in browser your Drupal site.**  
1. **:~$** ddev logs **# Show logs mixing nginx and php-fpm. Works in a stopped container.**  
1. **:~$** ddev logs -f **# Same as former, but following logs in real-time.**  
1. **:~$** ddev ssh **# Connecting to the web container with ssh.**  
1. **:~$** ddev ssh -s db **# Connecting to the database container using ssh.**  
1. **:~$** ddev export-db --gzip=false > /temp/db.sql **# Returns a dump file in the selected folder.**  
1. **:~$** ddev import-db --src=./backups/dump_02062020.sql.gz **# Load a compressed dump file in the default DDEV database called 'db'.**


## 5.5- Drupal CLI using Drush or Drupal Console (10 examples)
1. **:~$** drupal cect application --module="managing_activities" --optional-config --remove-uuid --remove-config-hash **# Export the config of an specific content type as optional.**  
1. **:~$** drush -l sitename cr **# Clear cache only for a site in a multisite Drupal installation.**  
1. **:~$** drush migrate-fields-source your_migration_file **# Show the available fields in Drupal Migrations.**  
1. **:~$** drush migrate-import your_migration_file --limit="50 items" --feedback="5 items" **# Executes a migration process but only transferring 50 items with info every 5 items.**  
1. **:~$** drush config-delete 'module.config_settings' **# Deletes Configuration Objects.**  
1. **:~$** drush cim --partial --source=only-few-configs/ **# Importing partial configuration files.**  
1. **:~$** drush watchdog:show --count=20 **# Getting the last 20 problems from a Drupal site.**  
