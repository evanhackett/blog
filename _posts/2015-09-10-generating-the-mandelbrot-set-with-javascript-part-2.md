---
layout: post
title: "Generating the Mandelbrot Set with Javascript - Part 2"
date: 2015-07-3 18:47:00
image: '/assets/image/'
description:
tags:
- fractals
- Mandelbrot-set
- javascript
- math
categories:
- Mandelbrot-set
twitter_text:
---

Welcome to part 2 of “Generating the Mandelbrot Set with Javascript”. In [part 1][part1] we learned about the Mandelbrot set equation, and what it’s pieces are. We learned about sets, the complex plane, and complex numbers. We still have a few math concepts to understand before we can fully comprehend what the Mandelbrot set actually is. After that, we will generate the graph using javascript and an HTML canvas!<!--more-->

At the end of part 1, we were working on understanding this definition:

“… the set of values of c in the complex plane for which the orbit of 0 under iteration of the complex quadratic polynomial
$$z_{n+1}=z_n^2+c$$ remains bounded.”

We should now have the vocabulary to understand the first half. Now lets tackle the second half:

“…for which the orbit of 0 under iteration of the complex quadratic polynomial $$z_{n+1}=z_n^2+c$$ remains bounded.”


<b>Complex quadratic polynomial</b> - A polynomial is simply an algebraic expression involving variables, coefficients, and arithmetic operations. Here are some examples:

$$x + 1$$\\
$$2x - 3$$\\
$$x^2 + 4$$\\
$$y^5 - 12$$

All of the above are polynomials. What makes a polynomial quadratic is when the degree of the highest exponent is 2. So the only quadratic polynomial in the example list I just gave would be $$x^2 + 4$$. That is because the highest exponent is equal to 2. A complex quadratic polynomial is simply a quadratic polynomial that has complex numbers as coefficients.

<b>Orbit of 0 under iteration</b> - We have a function $$f_{c}(z) = z^2 + c$$. First, we choose a number for c. Lets start with 1. Now we are going to plug in 0, and “iterate” to infinity. So we plug in 0, get the output, and then we plug in that output into the function again. Then we take that output and plug it into the function again. We repeat this process to infinity.

$$f_{1}(0) = 0^2 + 1$$\\
$$f_{1}(1) = 1^2 + 1$$\\
$$f_{1}(2) = 2^2 + 1$$\\
$$f_{1}(5) = 5^2 + 1$$\\
…

In the example above, I used a real number (1) for c to make it easier to see what is going on. C should be a complex number though.

<b>Remains bounded</b> - Here is where it all comes together. A function is bounded if there exists a number, M, in which the set of all numbers in that functions range is less than M. To see if the orbit under iteration remains bounded, we keep iterating to infinity, and we check whether the outputs are “tending towards” infinity. If they are clearly going up to infinity, we consider it unbounded. If the outputs are clearly staying below infinity (growing too slowly, or alternating between numbers), than it IS bounded. In the example above, when we iterated f1 to infinity, the outputs were clearly just going to explode (grow quickly) and tend toward infinity. What if we chose -1 instead? Once again we start by plugging in 0:

$$f_{-1}(0) = 0^2 -1$$\\
$$f_{-1}(-1) = (-1)^2 -1$$\\
$$f_{-1}(0) = 0^2 -1$$\\
$$f_{-1}(-1) = (-1)^2 -1$$\\
…

The outputs will clearly alternate back and forth forever. The outputs are not tending towards infinity (there exists a number, M, such that all the values of f-1 under iteration are less than M. This means f-1 remains bounded under iteration! -1 isn’t a complex number though, so it isn’t part of the Mandelbrot set even though it remains bounded.

Now, one last complication. We aren’t just looking at the outputs. We are looking at the magnitude of the complex number (the distance from 0 on the complex plane). So the complex number $$3 + 4i$$ would have a magnitude of 5. To get the magnitude, you picture the complex number as a right triangle, and use the Pythagorean theorem. So for $$3 + 4i$$, picture a triangle with side lengths 3 and 4. The hypotenuse would be 5, because $$3^2 + 4^2 = 5^2$$.

That is a lot to take in! Lets take another look at the definition we are trying to understand.

“… the set of values of c in the complex plane for which the orbit of 0 under iteration of the complex quadratic polynomial $$z_{n+1}=z_n^2+c$$ remains bounded.”

Ok, we got this. The Mandelbrot set is the set of all complex numbers whose magnitudes don’t tend toward infinity when you start at 0 and iterate through the function $$f_{c}(z) = z^2 + C$$.

That is pretty much it for the math and vocabulary. If you are still feeling a little lost, just keep reading, it will make a lot more sense when we code it up.

Ok, so it’s code time! If we are going to be drawing pictures, we need a canvas.

{% highlight html %}
<canvas id="canvas" width="800" height="800"></canvas>
{% endhighlight %}

Place that somewhere in the body of an html page. I’m going to go with 800 by 800 pixels, because that seems like a good place to start. Now we have a place to draw our graph. That is pretty much all we need as far as HTML goes.

Now it’s time to write some javascript. We will need to do the standard canvas setup stuff (get canvas, get context, get resolution, get offset).

{% highlight javascript %}
var canvas = document.getElementById('canvas');
var ctx = canvas.getContext('2d');

var RESOLUTION = canvas.width;
var LEFT_OFFSET = canvas.offsetLeft;
var TOP_OFFSET = canvas.offsetTop;
{% endhighlight %}


Now we can draw to the canvas. But first we need to figure out what what to draw, and where to draw it. Our drawing strategy will go like this:
Start at (0,0) and loop through every coordinate on the canvas.
Convert that cartesian coordinate to the corresponding complex number.
Check if that complex number is bounded
color the pixel according to the number of iterations it took to discover if the number was bounded

Step 4 is the interesting part. We can color/shade the pixel as a function of the number of iterations. If it took 5 iterations to discover the number was not bounded, we can color it black. If it took 100, we can color it grey. If it took 1000, we can color it white. This is what allows us to create beautiful, colorful, complex looking patterns. Here is our drawing function:

{% highlight javascript %}
var draw = function(canvas) {
  for (var r = 0; r < RESOLUTION; r++) {
    for (var c = 0; c < RESOLUTION; c++) {
      // convert from Cartesian coordinates to complex coordinates
      var complex = {r: c/RESOLUTION, i: -r/RESOLUTION};

      // test the point and return number of iterations
      var n = isBounded(complex);

      // The RGB values of each coordinate are a function of the number of iterations
      canvas.fillStyle = "rgb("+ n +","+ n +","+ n +")";

      // draw a 1 pixel by 1 pixel rectangle at position (c, r)
      canvas.fillRect(c, r, 1, 1);
    }
  }
};
{% endhighlight %}




So, once again, we loop through all the coordinates on our canvas, we check the number of iterations it takes to discover if the point was bounded, we set the color accordingly, and then we plot the point on our canvas.

How do we know when something is bounded or unbounded after only a certain number of iterations? Some badass mathematicians figured out that once the output of an iteration reaches 2 or more, the iterates will tend to infinity. If it stays below 2, it might be bounded. We will not iterate to infinity, since that would unfortunately require infinite time. So instead we pick a max number of iterations that we are satisfied with. It should be small enough to allow our program to run at a reasonable speed, but big enough to somewhat accurately determine if the point is bounded or not. The bigger the number, the more accuracy. The smaller than number, the more speed (since less iterations will be required for each point). So we iterate up to that number, checking to see if the magnitude of the complex number ever exceeds 2. If it does, we immediately break out of the iteration, because we know that since it exceeded 2 it must be unbounded. Lets take a look at the code.

{% highlight javascript %}
// defines the max number of times we iterate for a given number
var MAX_ITERATIONS = 1000;

// returns the number of iterations it took to see if the number is bounded
var isBounded = function(c) {
  // we are simulating an imaginary number by using 2 js numbers (1 for the 'real' part, and 1 for the 'imaginary' part)
  var m = {r: 0, i: 0}; // m for Mandelbrot :)

  for (var i = 0; i < MAX_ITERATIONS; i++) {
    // fc(z) = z^2 + C
    // (a+bi)*(a+bi) = a^2 - b^2 + 2ab
    var temp = m.r;
    m.r = (m.r * m.r) - (m.i * m.i) + c.r;
    m.i = 2 * temp * m.i + c.i;

    // Pythagorean theorem. Calculating if distance from origin is greater than 2
    // if: a^2 + b^2 > 2^2
    // since we are on the complex plane, 'a' is the real part, 'b' is the imaginary part
    if (m.r * m.r + m.i * m.i > 4) {
      // number tends to infinity, return # of iterations
        return i;
    }
  }
  // number might be bounded, return # of iterations
  return i;
};
{% endhighlight %}


We start by defining a global constant representing the max number of iterations for each point. I chose 1000 here, but feel free to experiment. Since we are looking at the behavior of 0 under iteration (whether it is bounded or not) of the polynomial  $$f_{c}(z) = z^2 + c$$, we start by plugging in 0 into the equation $$f_{c}(z) = z^2 + c$$, where c is the complex number we passed into the function. To iterate this process, we are using a for-loop that just loops MAX_ITERATIONS number of times, unless it breaks early. After each iteration of the equation (still inside the for-loop), we check the magnitude of the output. We use the pythagorean theorem to get the hypotenuse of the right triangle formed by the “length” and “width” (real and imaginary) part of the number. We then check if that number is greater than 2. If it is, we can immediately return, since we know the number is unbounded. Rather than simply returning true or false, we return the number of iterations. This is so we can color or shade the point as a function of the number of iterations. If the number’s magnitude never exceeded 2, we don’t break early. The for-loop will complete, and our function will return the MAX_ITERATIONS.

That’s it! We should now have a graph that looks something like this:

![Mandelbrot set screenshot](/assets/images/mandelbrot-before-recenter.png)

The origin (0,0) of our graph is at the upper left corner, and the point (1,1) is on the bottom right corner. If you want a more complete picture, you should try to get the origin in the center of the canvas, and you might need to zoom out a little. To do this, you will need to modify this line:

{% highlight javascript %}
// convert from Cartesian coordinates to complex coordinates
 var complex = {r: c/RESOLUTION, i: -r/RESOLUTION};
{% endhighlight %}


If you want to change the colors, try modifying this line:

{% highlight javascript %}
canvas.fillStyle = "rgb("+ n +","+ n +","+ n +")";
{% endhighlight %}


I’ll leave this as an exercise to the reader. If you want extra credit points, add the ability to zoom in on wherever the user clicks.

Thank you for reading, I hope you enjoyed our dive into the innards of generating the Mandelbrot Set! Be sure to check out my Mandelbrot Set generator where you can zoom in anywhere the user clicks: [here][deployed]

[part1]: {% post_url 2015-09-10-generating-the-mandelbrot-set-with-javascript-part-1 %}

[deployed]: http://evanhackett.com/MandelbrotSet/
