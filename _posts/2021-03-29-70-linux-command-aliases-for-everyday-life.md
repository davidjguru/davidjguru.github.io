---
layout: post
title: 70 Linux commands aliases for everyday life
permalink: /blog/70-linux-commands-aliases-for-everyday-life
published: true
date: 2021-03-29
author: davidjguru
categories: [Drupal & Commands]
sitemap: true
youtubeId: NTFFhcEus5o
---

| ![Picture from Unsplash, by @jjying]({{ site.baseurl }}/images/davidjguru_drupal_8_9_50_command_aliases_for_everyday_life_main.jpg) |
|:--:|
| *Picture from Unsplash, user [JJ Ying](https://unsplash.com/@jjying)* |

Following on from my previous post about "200 Linux commands for everyday life" that I published a few months ago -September 2020, [here](https://davidjguru.github.io/blog/200-linux-commands-for-everyday-life)-, so today I want to share another small article about little everyday utilities. The previous post had a more extensive (and intense) feedback than when I'm writing about Drupal, so this has invited me to think that maybe (just maybe) it could be useful content for someone.  
<!--more-->

This time, I have chosen to gather my favourite console "aliases" in one post. Aliases are a very dynamic way to add power to the command terminal and also a way to improve your fluency in the Linux terminal and therefore, your own productivity when working on projects. They are really just an exercise in replacing commands that you use frequently with a faster and simpler version of them, so that you can type them faster and more intuitively (especially if they include options and parameters). This way you can type less to request the same long instructions.  

Do you often type the same command with similar parameters? make it a console "alias"!...well, you can also create "functions" to do the same but in a much more dynamic and adaptive way, but I've left that for another article later on. Today: aliases!.  

-------------------------------------------------------------------------------
## Introduction
Just go to your `/home/user` folder and edit your .bashrc file in order to include these aliases. Then reload the bashrc file and you'll get the new aliases available. You can add the new aliases in a new file too, using a file named `.bash_aliases` and then load the new file from your original `.bashrc` file, just by adding the reference in order to set the aliases:  

```bash
# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

if [ -f ~/.bash_functions ]; then
    . ~/.bash_functions
fi
```

For example, if you wanna use reduced formats for usual instrucions, you can do something like this: 

```bash
alias yep='sudo apt install $1'
alias nop='sudo apt remove $1'
```

And so you can type by prompt using the new aliases:  

```bash
$ yep nmap
$ nop nmap
```

**Remember:**  
```bash
1. **:~$** cd ~    
2. **:~$** vim .bashrc // :wq! (after paste the content)      
3. **:~$** source .bashrc   
```

You can download the whole file `.bash_aliases` fullfilled with all the aliases from [here like a Github Gist](https://gist.github.com/davidjguru/019beabeac6245959564499db3e45084#file-bash_aliases). 

## Morning Opertures
```bash
1. alias whatsup='service --status-all'  
2. alias hello='sudo /etc/init.d/apache2 stop && cd workspace/project && ddev start && ddev launch'   
3. alias hi='sudo systemctl stop apache2'  
4. alias ports='nmap localhost'  
   **Are my ports open?**
5. alias dens="sudo systemd-resolve --status | grep 'DNS Servers'"
   **What is my current DNS Server?**
6. alias iad='systemctl is-active docker'  
7. alias bye='shutdown -r now'  
```
## Usual Instructions  
```bash
8. alias c='clear'  
9. alias h='history'  
10. alias hg='history | grep $1'  
    **Search using history command: $ hg somestring.**  
11.  alias wg='wget -c '  
12. alias al="echo ------------Your curent aliases are:------------¡';alias"  
    **Returns all your aliases collection.**
13. alias sup="sudo apt update && sudo apt upgrade -y"  
```
## Content in folders  

### Getting info from a position in a folder.
```bash
14. alias ll='ls -la'  
    **Debian doesnt have the -ll- alias created by default, like Ubuntu.**    
15. alias lf='ls -alF'  
16. alias la='ls -A'  
17. alias ls='ls -CF'  
18. alias lt='ls --human-readable --size -1 -S --classify'  
    **Sort resources by _file_ size.**   
19. alias lu='du -sh * | sort -h'  
    **Same as former but counting size from folders too.**   
20. alias lt='ls -t -1 -long'  
    **Gets an ordered list of files and folders by changes in time.**   
21. alias lc='find . -type f | wc -l'  
    **Gets a total recursirve count of existing files, no folders.**   
22. alias ld='ls -d */'  
    **Gets a list of existing directories from current folder.**   
```
### Info from Drupal: config files, types of content and existing paragraphs  
```bash
23. alias lsc='ls -lah config/sync/ | wc -l'   
24. alias lsn='ls -lah config/sync/node.type.* | wc -l'    
25. alias lsp='ls -lah config/sync/paragraphs.paragraphs_type.* | wc -l'  
```
## Files, folders and resources  
```bash
26. alias fh='find . -name '   
27. alias ..='cd ..'  
28. alias ....='cd ../..' 
``` 
### More Jump Down  
```bash
29. alias 1d="cd .."  
30. alias 2d="cd ..;cd .."  
31. alias 3d="cd ..;cd ..;cd .."  
32. alias 4d="cd ..;cd ..;cd ..;cd .."  
33. alias 5d="cd ..;cd ..;cd ..;cd ..;cd .."  
34. alias untar='tar -zxvf $1'  
    **Open a tar.gz formated folder.**  
35. alias tar='tar -czvf $1'  
    **Compressing a folder in tar.gz format.**  
36. alias mnt="mount | awk -F' ' '{ printf \"%s\t%s\n\",\$1,\$3; }' | column -t | egrep ^/dev/ | sort"  
37. alias df="df -Tha --total"   
38. alias exp='nautilus .'  
    **Opens the current directory in file explorer.**  
```
## Git Related Aliases  

### Basic info
```bash
39. alias gs='git status'  
40. alias gb='git branch'  
41. alias gr='git remote -v'  
```
### Getting info from 'Git log'  
```bash
42. alias gl='git log --oneline'  
    **Gets a log info view in a single line format.**    
43. alias glc='git log --format=format: --name-only --since=12.month | egrep -v '^$' | sort | uniq -c  | sort -nr | head -50'  
    **Get a list with the most changed files from 12 months ago.**  
44. alias gld='git log –oneline –decorate –graph –all'  
    **Show all the branches in the tree format history with pointers.**  
45. alias glp="git log -g --grep='PHP' -10 --pretty='%h - %s - %cn - %cd'"  
    **Gets info from log filtering by some fixed key in commit messages.**  
    **I am using some keys in message commits, [just like these.](https://gitlab.com/-/snippets/2096890)**  
46. alias glf='git for-each-ref --sort=-committerdate'   
    **Get commits list by DESC order.**
```
### Pushing to basic branches 
```bash
47. alias gpom='git push origin master'  
48. alias gpod='git push origin develop'  
```
## Drush Commands
**I'm using [DDEV-Local](https://ddev.readthedocs.io/en/stable/) for my Drupal local deploys.**  
```bash
49. alias cex='ddev drush cex'  
50. alias cim='ddev drush cim'  
51. alias cexy='yes|ddev drush cex'  
52. alias cimy='yes|ddev drush cim'  
53. alias dgm='ddev drush generate module'  
54. alias dws='ddev drush watchdog:show –count=20'  
    **Shows the last 20 problems in your Drupal installation.**  
55. alias ddc='ddev drush cr'  
56. alias dpc='ddev drush pmu $1 && ddev drush cr'
57. alias dec='ddev drush en -y $1 && ddev drush cr'
58. alias dpe='ddev drush pmu $1 && ddev drush cr && ddev drush en -y $1'
59. alias dva='ddev drush views:analyze'
60. alias dpl='ddev drush pml'
61. alias dle="ddev drush pm-list --status='enabled'"
62. alias dld="ddev drush pm-list --status='disabled'"
63. alias dlo='ddev drush pm-list --type=module --status=enabled --no-core'
```
## DDEV Explicit Aliases   
```bash
64. alias dsl='ddev start && ddev launch'   
65. alias ddy='ddev delete -Oy'  
    **Deletes the DDEV folder and resources in the current project folder.**  
66. alias did='ddev import-db'   
67. alias ddl='ddev list'  
```
## Reviewing Codestyle Aliases  
```bash
68. alias dcs1="ddev exec -d=/var/www/html vendor/bin/phpcs --standard=Drupal --extensions='php,module,inc,install,test,profile,theme,info,txt,md' web/modules/custom/"  
69. alias dcs2="ddev exec -d=/var/www/html vendor/bin/phpcs --standard=DrupalPractice --extensions='php,module,inc,install,test,profile,theme,info,txt,md' web/modules/custom/"  
70. alias dcsr='ddev exec -d=/var/www/html vendor/bin/phpcs –report=diff'  
    **With /path/to/code, Will generate a patch file from a style diff.**
```
## :wq!

### Recommended song: Vitamin C - CAN

{% include youtubePlayer.html id=page.youtubeId %}
