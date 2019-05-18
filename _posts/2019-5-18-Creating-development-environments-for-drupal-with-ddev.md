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

When we're using the directive -%c- we are indicating that the parameter that we'll add next will be CSS guidelines that will have to be interpreted starting from the appearance of the directive.

## Example 1
```javascript      
console.log("%cBEWARE OF THE DOG", "color: red; font-size: 3rem; font-weight:bold;");
```
![Console Style example one]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_console_style_example_one.png)


## Example 2
Ok, but we can combine the style guidelines so that they apply only to certain parts of the text (not just the entire string). Let's see.

```javascript
console.log("This is a partial %cStylized message", "color: green; font-style: italic; font-size: 4rem; background-color: yellow; padding: 2px");
```
![Console Style example two]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_console_style_example_two.png)

## Example 3
We can extend the CSS rules application as much as we want to style our console information.

```javascript
console.log(
        "%cPROTECT YA NECK!",
        "color:blue;font-family:system-ui;font-size:4rem;-webkit-text-stroke: 4px black;font-weight:bold"
      );

```
![Console Style example three]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_console_style_example_three.png)

## Example 4
We can also integrate CSS values within properties into objects that we can use by calling them from console.log().

```javascript
  let colors = {
    "gray": "font-weight: bold; color: #1B2B34;",
    "red": "font-weight: bold; color: #EC5f67;",
    "orange": "font-weight: bold; color: #F99157;",
    "yellow": "font-weight: bold; color: #FAC863;",
    "green": "font-weight: bold; color: #99C794;",
    "teal": "font-weight: bold; color: #5FB3B3;",
    "blue": "font-weight: bold; color: #6699CC;",
    "purple": "font-weight: bold; color: #C594C5;",
    "brown": "font-weight: bold; color: #AB7967;"
      }
  let backgrounds = {
     "black": "background: black; color: white; display: block;",
     "blue": "background: blue; color: white; display: block;",
     "red": "background: red; color: white; display: block;",
     "yellow": "background: yellow; color: white; display: block;",
     "green": "background: green; color: white; display: block;",
     "pink": "background: pink; color: white; display: block;"
}
// All the colors
  console.log('%cHello One', colors.gray);
  console.log('%cHello Two', colors.red);
  console.log('%cHello Three', colors.orange);
  console.log('%cHello Four', colors.yellow);
  console.log('%cHello Five', colors.teal);
  console.log('%cHello Six', colors.blue);
  console.log('%cHello Seven', colors.purple);
  console.log('%cHello Eight', colors.brown);

// All the backgrounds
  console.log('%cThis is the black background', backgrounds.black);
  console.log('%cThis is the blue background', backgrounds.blue);
  console.log('%cThis is the red background', backgrounds.red);
  console.log('%cThis is the yellow background', backgrounds.yellow);
  console.log('%cThis is the green background', backgrounds.green);
  console.log('%cThis is the pink background', backgrounds.pink);
```
![Console Style example four]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_console_style_example_four.png)


## Example 5
Let's play a little with the styles.

```javascript
  var styles = [
      'background: linear-gradient(#D33106, #571402)'
      , 'border: 1px solid #3E0E02'
      , 'color: white'
      , 'display: block'
      , 'text-shadow: 0 1px 0 rgba(0, 0, 0, 0.3)'
      , 'box-shadow: 0 1px 0 rgba(255, 255, 255, 0.4) inset, 0 5px 3px -5px rgba(0, 0, 0, 0.5), 0 -13px 5px -10px rgba(255, 255, 255, 0.4) inset'
      , 'line-height: 40px'
      , 'text-align: center'
      , 'font-weight: bold'
  ].join(';');

  console.log('%c This is just a special log message', styles);
```

![Console Style example five]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_console_style_example_five.png)


## Example 6
Now, we're going to create an external function to print Style in console, encapsulating the calling to console.log().

```javascript
function myCustomLog(message, color="black") {
  switch (color) {
    case "info":
      color = "Green";
      fontsize = "2rem";
      break;
    case "success":
      color = "Blue";
      fontsize = "3rem";
      break;
    case "warning":
      color = "Orange";
      fontsize = "4rem";
      break;
    case "error":
      color = "Red";
      fontsize = "5rem";
      break;
    default:
      color = color;
      fontsize = "1rem";
     }

  console.log(`%c${message}`, `color:${color}; font-size:${fontsize};`);
}

myCustomLog("Calling to my custom log without a color value, using default");
myCustomLog("Take more Info...", "info");
myCustomLog("Now, a Success! message", "success");
myCustomLog("This is a Warning!!", "warning");
myCustomLog("Gets an Error!", "error");
```
![Console Style example six]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_console_style_example_six.png)

Well, here we come with this quick post about applying CSS styles to console.log(). If you are interested, I have created a folder in a Gitlab repository and a specific branch for these and other console tips.
 [Here](https://gitlab.com/davidjguru/master-javascript/tree/js_Tips_and_Tricks_for_Console/js_Tips_and_Tricks_for_Console_Folder){:target="_blank"}
 In the file called "tips_tricks_console.js" you will find the examples.

## :wq!

Greetings. wq!    :-*
