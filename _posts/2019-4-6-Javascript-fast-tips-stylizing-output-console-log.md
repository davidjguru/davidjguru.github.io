---
layout: post
title: JavaScript Fast Tips (II) - Styles in console output
permalink: /blog/javascript-fast-tips-stylizing-output-console-log
---
| ![Picture from Unsplash, by @danielwatsondesign]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_image_from_unsplash_colours.jpeg) |
|:--:|
| *Picture from Unsplash, user Daniel Watson @danielwatsondesign* |


The previous Tuesday I was practicing with JavaScript and using console.log() to show data (nothing serious, if it was important I had used breakpoints, of course :-P), and in an outflow of extensive information, I remembered a nice way to organize it visually: You could give color and shape to the information displayed through console.log().
<!--more-->

## Introduction
Then I remembered the first time I saw the Facebook console.log announcement (years ago) AFAIK my first experience of seeing a stylized console output. The key? the use of the %c directive as a parameter. Yes, you can use the %c directive to apply a CSS style to console output.  

| ![Javascript Warning through Console by Facebook]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_facebook_warning.png) |
|:--:|
| *STOP! - Javascript Warning through Console by Facebook* |

When we use the directive% c we are indicating that the parameter that we add next will be CSS guidelines that will have to be interpreted starting from the appearance of the directive.

## Example 1

```javascript      
console.log("%cBEWARE OF THE DOG", "color: red; font-size: 3rem; font-weight:bold;");
```
![Console Style example one]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_console_style_example_one.png)

Ok, but we can combine the style guidelines so that they apply only to certain parts of the text (not just the entire chain). Let's see.

## Example 2

```javascript
console.log("This is a partial %cStylized message", "color: green; font-style: italic; font-size: 4rem; background-color: yellow; padding: 2px");
```
![Console Style example two]({{ site.baseurl }}/images/davidjguru_drupal_8_javascript_console_style_example_two.png)


Get more info about console.table at [developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/Console/table){:target="_blank"}

## :wq!

Greetings. wq!    :-*
