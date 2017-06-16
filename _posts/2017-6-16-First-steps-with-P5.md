---
layout: post
title: First steps with the P5.js library 
---

A few days ago I attended a talk given by Esperanza Moreno [@_mimina_](https://twitter.com/_mimina_), architect, researcher and developer. She's developing applications that allows connect musical file (metadata) with renderings and musical remixes, a visual and auditory show called "Antropoloops", a quite powerful cultural project. [See their website](http://antropoloops.tumblr.com/)

During her talk while explaining Processing and P5, Esperanza was doing live practical exercises programming applications with elemental shapes, drawing with functions and showing their results in real time. I liked a lot of things about his talk and the used bookstores: didactics, examples and the enormous visual potential that could be used to teach programming with simple functions and interesting drawings.

 I did not know this Javascript library, so I was researching and found some resources and information available to start practicing. I went to the project homepage at [P5 js](https://p5js.org/) and started reading about the options...


The library has functions for processing images, elemental shapes, movements and sounds, so there is a very interesting way to investigate with it. I installed all the resources and started testing with canvas creation functions and basic shapes, with parameters, doing something like this, within the proposed initiation examples:

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
  //of the initial orders of the draw function
  // (not-dinamic flower, I mean)

  translate(580, 200);
  noStroke();
  for (var i = 0; i < 10; i ++){
      ellipse(mouseX, mouseY, 20, 80);
      rotate(PI/5);

  }
}

```
These were the first steps for me and the results...well, Well, it seems I still have a long way to go :-P

![First steps with P5.js]({{ site.baseurl }}/images/P5_1.jpg)

With the same script (called sketch.js) I played to change elements like the background (testing options) or another basics shapes. As you can view at the bottom of the draw() function, I tried to draw a flower. I think I'm in love with this library...I'll follow working with it. 

![Steps with P5.js and backgrounds]({{ site.baseurl }}/images/P5_2.png)


Greetings. 

