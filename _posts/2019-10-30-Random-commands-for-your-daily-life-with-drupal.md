---
layout: post  
title: Random commands for your daily life with Drupal 
permalink: /blog/random-commands-for-your-daily-life-with-drupal 
published: true  
date: 2019-10-30  
categories: [Drupal]  
sitemap: true
---
| ![Picture from Unsplash, by @jamie452]({{ site.baseurl }}/images/davidjguru_drupal_8_random_comands_for_your_daily_life_with_drupal.jpeg) |
|:--:|
| *Picture from Unsplash, user Jamie Street @jamie452* |


Recently I returned to the habit of asking myself every morning "What did I do yesterday?" just at the beginning of the day, in time to find the connection with the sequence of tasks of the present day. 
Among the questions, to contextualize myself I used the command "history" again to extract by console the history of instructions of the previous day...and so I have been compiling some commands used daily. 
<!--more-->

## Block One: Apache and Servers

```bash
// Test if Apache is running or not.
sudo systemctl status apache2
// Stopping Apache.
sudo /etc/init.d/apache2 stop
// Wait a minute...are my ports open?
nmap localhost

// What is my current DNS Server?
sudo systemd-resolve --status | grep "DNS Servers"

// Show installed versions vs. available with Composer.
composer show -lo
```


## Block Two: Version control

```bash
// Pulling and merging only a single file from Git remote repo. 
git fetch 
git checkout origin/branch --path/to/file

// What files were changed between branches.
git diff --name-status anotherbranch

// Show me a summary of changes in project. 
git diff --summary


```

## Block Three: Diagnostic of a Drupal

```bash
// Getting quickly info by asking the configuration files in config/sync. 
ls -lah config/sync/ | wc -l
ls -lah config/sync/paragraphs.paragraphs_type.* | wc -l
ls -lah config/sync/node.type.* | wc -l


// Just like drush en // drush pmu -BUT- executing itself drush cr
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