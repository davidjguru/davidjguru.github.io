---
layout: post  
title: Random commands for your daily life with Drupal 
permalink: /blog/random-commands-for-your-daily-life-with-drupal 
published: true  
date: 2019-10-30 
author: davidjguru
categories: [Drupal]  
sitemap: true
---
| ![Picture from Unsplash, by @jamie452]({{ site.baseurl }}/images/davidjguru_drupal_8_random_comands_for_your_daily_life_with_drupal.jpeg) |
|:--:|
| *Picture from Unsplash, user Jamie Street @jamie452* |


Recently I returned to the habit of asking myself every morning "What did I do yesterday?" just at the beginning of the day, in time to find the connection with the sequence of tasks of the present day. 
Among the questions, to contextualize myself I used the command **history** and **history |grep whatever** again to extract by console the history of instructions of the previous day...and so I have been compiling some commands used daily. 
<!--more-->
As the result has been a little chaotic, I have played to order them in some way in blocks, trying to give an internal coherence (or something like that)...... I hope it can be useful to someone who is looking for some help for a specific situation. 
As it seemed excessive to me to explain each command/utility/tool, I have decided to summarize it in two blocks: first the technological stack on which I usually use these instructions and then a comment of a line on the instruction to better illustrate the case of day-to-day use.

Stack
---------
* Ubuntu 18.02.2 virtualized.
* Git as version control, version 2.17.1.
* Composer installed globally in my system, version 1.8.5.
* Apache as webserver for testing purposes, version 2.4.29.
* Drush per project, version 9.7.1.
* Drupal Console, version 1.9.3.


**Remember:** these are instructions used in a Linux environment, specifically in an Ubuntu 18.04.2. 

## Block One: Apache and Servers
For some reason that I don't understand well, for local environments built ad-hoc (outside Docker or DDEV), I tend to use Apache...maybe for coming from LAMP environments or for a special affection...for me it is very normal to have several environments with Apache as web server (for other things Nginx). 


```bash
// Get the current version of Apache.
apache2 -v

// Same but with settings
apache2 -V 

// Test if Apache is running or not.
sudo systemctl status apache2

// Stopping Apache.
sudo /etc/init.d/apache2 stop
// - or: -
sudo service apache2 stop

//  Review the Syntax from your Apache config.
sudo apache2ctl -t
```
Now talking 'bout Apache, I would to share just a simple example to get files not for your local path, but from a live web site. It's a simple way to mapping paths for images (for example) from a local deploy using the images in the live site. 

The next snippet will work as the [Stage File Proxy contrib module](https://www.drupal.org/project/stage_file_proxy)
```bash
# Rewrite install.php during install to see 
# if mod_rewrite is working
RewriteRule ^core/install.php core/install.php?rewrite=ok [QSA,L]

##FROM HERE.
# sites/default/files/ from your site in “pre” or live
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_URI} (.*)/sites/default/files/(.*)
RewriteCond %{REQUEST_URI} !=(.*)sites/default/files/(.*).css
RewriteCond %{REQUEST_URI} !=(.*)sites/default/files/(.*).js
RewriteRule   "^(.+)"  "https://livesite.com/$1"  [L,R=302]
##TO HERE.

  # Pass requests not referring directly 
  # to files to index.php
RewriteCond %{REQUEST_FILENAME} !-f
```
This will integrate the images into your local deployment without the need to move file weight folders in /sites/default/files/ or make slow copies of many files. Use the already live images for your local environment. 

Now, stuff non-apache related, just some more instructions, like a hotchpotch of commands:

```bash
// Wait a minute...are my ports open?
nmap localhost

// What is my current DNS Server?
sudo systemd-resolve --status | grep "DNS Servers"

// Show installed versions vs. available with Composer.
composer show -lo

// Increasing the cache size globally in Composer.
composer config --global cache-files-maxsize "2048MiB"

// Add the flag --profile to show details in feedback.
composer require drupal/field_group --profile
```


## Block Two: Version control

```bash
// Pulling and merging only a single file 
// from Git remote repo. 
git fetch 
git checkout origin/branch --path/to/file

// What files were changed between branches.
git diff --name-status anotherbranch

// Show me a summary of changes in project. 
git diff --summary

// Show me just a brief with all the commits
// in the current branch.
git log --pretty=oneline --abbrev-commit

```

## Block Three: Diagnostic of a Drupal
Now, I've included some useful commands to extract information quickly and to be able to interpret (or almost) the state of a drupal project. Some things that will be useful to know what is happening in a Drupal, to extract certain markers. 

```bash
// Getting quickly info by asking the configuration files 
// in config/sync. 
ls -lah config/sync/ | wc -l
ls -lah config/sync/paragraphs.paragraphs_type.* | wc -l
ls -lah config/sync/node.type.* | wc -l

// Just like drush en // drush pmu -BUT- executing
// itself drush cr
drupal module:install my_random_module
drupal moi my_random_module
drupal module:uninstall my_random_module
drupal mou my_random_module

// Importing partial configuration files with Drush.
drush cim --partial --source=only-few-configs/

// Importing partial config Single File with Drupal Console. 
drupal cis
 Enter file name or file absolute path to import:
 > /var/www/html/web/config/sync/views.view.researcher.yml

// Getting the last 20 problems from a Drupal site.
drush watchdog:show --count=20

// Get the total account of Views and its status.
drush views:analyze

// Getting all the modules in a Drupal Site. 
drush pm-list

// Getting all the modules only with status enabled. 
drush pm-list --status="enabled"

// Getting the disabled modules in Drupal.
drush pm-list --status="disabled"

// Getting Enabled Modules out of Core (Contrib / Custom).
drush pm-list --type=module --status=enabled --no-core

// Ask for an specific module status.
drush pm-list |grep fixed_blocks
// Or a family of related modules.
drush pm-list |grep views

// If you want something more specific using Drupal Console.
drupal debug:module
drupal dm
// By module.
drupal dm token
```

## Block Four: Getting data from database

```bash
// Get the largest tables from database sorted by size. 
mysql -uYOURMySQLUSER -p <<<"SELECT table_name AS 'Tables',
round(((data_length + index_length) / 1024 / 1024), 2) 
'Size in MB' FROM information_schema.TABLES 
base.WHERE table_schema = \"DATABASENAME\" 
ORDER BY (data_length base.+ index_length) DESC;" | head

// Example: Getting the five biggest tables in your database. 
MariaDB [drupal]> SELECT table_name AS "Tables",  
round(((data_length + index_length) / 1024 / 1024), 2) 
"Size in MB"  FROM information_schema.TABLES  
WHERE table_schema = "drupal" 
ORDER BY (data_length + index_length) DESC LIMIT 5;


// Get the total count of tables in your database.
mysql -uYOURMySQLUSER -pYOURMySQLPASSWORD -hYOURMySQLHOST 
-e "SELECT count(*) AS TOTAL_NUMBER_OF_TABLES
FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA 
= 'database_name';" database_name


// Paragraphs went crazy? count number of related tables. 
show tables like 'pa%';

```



## Summary
```bash

// Test if Apache is running or not.
sudo systemctl status apache2
// Init Apache. 
sudo service apache2 start
// Stopping Apache.
sudo /etc/init.d/apache2 stop
// Or:
sudo service apache2 stop
// Wait a minute...are my ports open?
nmap localhost

// What is my current DNS Server?
sudo systemd-resolve --status | grep "DNS Servers"

// Show installed versions of dependencies vs. those available with Composer.
composer show -lo

// Pulling and merging only a single file from Git remote repo. 
git fetch 
git checkout origin/branch --path/to/file

// What files were changed between branches.
git diff --name-status anotherbranch

// Show me a summary of changes in project. 
git diff --summary

// Show me just a brief with all the commits in the current branch.
git log --pretty=oneline --abbrev-commit




// Getting quickly info by asking the configuration files in config/sync. 
ls -lah config/sync/ | wc -l
ls -lah config/sync/paragraphs.paragraphs_type.* | wc -l
ls -lah config/sync/node.type.* | wc -l


// Just like drush en // drush pmu -BUT- executing by itself drush cr ;-) 
drupal module:install my_random_module
drupal moi my_random_module
drupal module:uninstall my_random_module
drupal mou my_random_module

// Importing partial configuration files with Drush.
drush cim --partial --source=only-few-configs/

// Importing partial configuration Single File with Drupal Console. 
drupal cis
 Enter file name or file absolute path to import:
 > /var/www/html/web/config/sync/views.view.researcher.yml

// Getting the last 20 problems from a Drupal site.
drush watchdog:show --count=20

// Get the total account of Views and its status.
drush views:analyze

// Getting all the modules in a Drupal Site. 
drush pm-list

// Getting all the modules only with status enabled. 
drush pm-list --status="enabled"

// Getting the disabled modules in Drupal.
drush pm-list --status="disabled"

// Getting Enabled Modules in Drupal out of Core (Contrib / Custom).
drush pm-list --type=module --status=enabled --no-core

// Ask for an specific module status.
drush pm-list |grep fixed_blocks
// Or a family of related modules.
drush pm-list |grep views

// And if you want something more specific using Drupal Console.
drupal debug:module
drupal dm
// By module.
drupal dm token


// Get a list of the largest tables from database sorted by size. 
mysql -uYOURMySQLUSER -p <<<"SELECT table_name AS 'Tables',
round(((data_length + index_length) / 1024 / 1024), 2) 
'Size in MB' FROM information_schema.TABLES 
base.WHERE table_schema = \"DATABASENAME\" 
ORDER BY (data_length base.+ index_length) DESC;" | head

// Example: Getting the five biggest tables in your database. 
MariaDB [drupal]> SELECT table_name AS "Tables",  
round(((data_length + index_length) / 1024 / 1024), 2) "Size in MB"  
FROM information_schema.TABLES  WHERE table_schema = "drupal" 
ORDER BY (data_length + index_length) DESC LIMIT 5;


// Get the total count of tables in your database.
mysql -uYOURMySQLUSER -pYOURMySQLPASSWORD -hYOURMySQLHOST 
-e "SELECT count(*) AS TOTAL_NUMBER_OF_TABLES
FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA 
= 'database_name';" database_name


// Paragraphs went crazy? show and count number of tables. 
show tables like 'pa%';
```