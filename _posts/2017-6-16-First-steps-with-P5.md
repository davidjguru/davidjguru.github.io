---
layout: post
title: First steps with the P5.js library 
permalink: /blog/First-steps-with-P5
---



A few days ago I attended a talk given by Esperanza Moreno [@_mimina_](https://twitter.com/_mimina_){:target="_blank"}, architect, researcher and developer. She's developing applications that allows connect musical files (metadata) with renderings and musical remixes, a visual and auditory show called "Antropoloops", a quite powerful cultural project. [See their website](http://antropoloops.tumblr.com/){:target="_blank"}

## Introduction

During her talk while explaining Processing and P5, Esperanza was doing live practical exercises programming applications with elemental shapes, drawing with functions and showing their results in real time. I liked a lot of things about his talk and the used bookstores: didactics, examples and the enormous visual potential that could be used to teach programming with simple functions and interesting drawings.

 I did not know this Javascript library, so I was researching and found some resources and information available to start practicing. I went to the project homepage at [P5 js](https://p5js.org/){:target="_blank"} and started reading about the options...


## Overview

The set of utilities and functions of P5 is divided into two resource areas: on the one hand the libraries that are part of the core of P5 and on the other hand the "contributed" libraries created by third parties that are part of the community of P5. 
Really, a p5.js library can be any JavaScript code that extends or adds to the p5.js core functionality. There are two categories of libraries. Core libraries (DOM and Sound) are part of the p5.js distribution and can be downloaded inside the basic package, while contributed libraries are developed, owned, and maintained by members of the p5.js community. The contributed libraries includes some very interesting tools for working with geodata, arduino or generative literature, music sequencing ...these resources really add an very striking "expanded universe" of p5... [check out the area of contributed libraries, please](https://p5js.org/libraries/){:target="_blank"}.


## Basic Example

The library (core) has functions for processing images, elemental shapes, movements and sounds, so there is a very interesting way to investigate with it. I installed all the resources and started testing with canvas creation functions and basic shapes, with parameters, doing something like this, within the proposed initiation examples:

```javascript

function setup() {

  // Create the Canvas  
     createCanvas(windowWidth, windowHeight);

}

function draw() {

  // This is the basic function
  //for drawing forms


     ellipse(120, 120, 100, 100);
  // We're drawing a flat circle
  // with 120px of distance from left
  // and from the top, with a width 
  // and height of 100px.

}

```
And we're going to see the result on the web browser, loading the simple index.html file:

![First test with P5 js]({{ site.baseurl }}/images/P5_3.png)

Our first form created with p5: a static circle drawn on the web browser. What if we introduce some dynamism? I have tried to generate a more advanced form: a flower. It's really only a kind of a set of single-center ellipses and rotations, so lets play a bit.

Idea: generate some interactivity. First I will try to control mouse events by changing the fill color of the circle and causing it to be drawn based on the movements on the screen. I will execute changes in color and position based on clicks and movement.

Then I will generate a "flower" on the screen and relate the movement of the flower to the movement of this circle, to control the flower from the circle and draw in conjunction with the management of these two elements. There we go!


```javascript

function draw() {
  if (mouseIsPressed) {
    fill(0);
  } else {
    fill(255);
  }
  ellipse(mouseX, mouseY, 80, 80);
}

```


```javascript
function setup() {

  // Create the Canvas
  createCanvas(windowWidth, windowHeight);

  // Set the background
  // Note: if you load the background
  // inside the setup, it won't be updated
  // along the execution of every frame
     background(255, 204, 0);
}

function draw() {

// Go to draw
  if (mouseIsPressed) {
    fill(0);
  } else {
    fill(255);
  }

  // Creating a flat circle
  ellipse(mouseX, mouseY, 120, 120);

  // Try to draw a fix or static flower
  // at the Canvas, but under the rules
  // of the initial orders of the draw function
  // (not-dinamic flower, I mean)

  translate(580, 200);
  noStroke();
  for (var i = 0; i < 10; i ++){
      ellipse(mouseX, mouseY, 20, 80);
      rotate(PI/5);
  // It will create the flower
  // only a rotated ellipse with
  // ten "petals" 

  }
}

```
These were the first steps for me and the results...well, Well, it seems I still have a long way to go :-P

## Results

![First steps with P5.js]({{ site.baseurl }}/images/P5_1.jpg)

With the same script (called sketch.js) I played to change elements like the background (testing options) or another basics shapes. As you can view at the bottom of the draw() function, I tried to draw a flower. I think I'm in love with this library...I'll follow working with it. 

![Steps with P5.js and backgrounds]({{ site.baseurl }}/images/P5_2.png)

## Recipe:

* The P5 libraries. You can link them remotely via CDN with these addresses, putting this code inside your -head- zone from an index.html

   >  src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.5.11/p5.min.js"
   >  src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.5.11/addons/p5.dom.min.js"
   >  src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.5.11/addons/p5.sound.min.js"

Or you can download them (normal or compressed as .min.js) and enter them in your project folder: [P5.js Download zone](https://p5js.org/download/){:target="_blank"}

**(In my case):**

* Ubuntu 17.04 zesty
* Apache2 (classic)
* Vim
* Atom
* Mozilla Firefox (with Chrome I had some loading problems)

## wq!

Greetings. wq!    :-* 
