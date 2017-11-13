---
layout: post
title: "Generating the Mandelbrot Set with Javascript - Part 1"
date: 2015-05-30 13:52:00
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

Have you ever wondered how some of those amazing fractal images are created? Through a series of blog posts, I’m going to explain how to create amazing pictures using the Mandelbrot set. I will be using the javascript programming language, but any language will do as long as you have a way to draw pixels to a canvas. In this post, part 1, I will be going over prerequisite material needed to understand the Mandelbrot set.

So, what is the Mandelbrot set anyway? Let’s look at the [wikipedia definition][definition]:

“The Mandelbrot set is the set of complex numbers 'c' for which the sequence ( c, c² + c, (c²+c)² + c, ((c²+c)²+c)² + c, (((c²+c)²+c)²+c)² + c, ...) does not approach infinity.”

That doesn’t exactly clear things up does it? Here is a more descriptive definition also found on the wikipedia page:

“… the set of values of c in the complex plane for which the orbit of 0 under iteration of the complex quadratic polynomial $$z_{n+1}=z_n^2+c$$ remains bounded.”

Ok, now we are getting somewhere. I’m going to cover this one piece at a time. There are a couple math words/concepts we need to define. “set”, “complex plane”, “orbit of 0 under iteration”, “complex quadratic polynomial”, “remains bounded”. Each of these pieces are actually pretty simple on their own. Lets break it down.

<b>Set</b> - A set is a collection of distinct objects. Think of a list of objects, where all objects are unique (there are no duplicates). A related programming concept would be the keys in a hash table (such as objects in javascript). The keys in a hash table form a set, since they are a collection of unique elements.

<b>Complex Plane</b> - The complex plane is a lot like the Cartesian plane from high school algebra. You know, a standard x,y graph. The difference is instead of an x-axis and a y-axis, the complex plane has a real-axis and an imaginary-axis. The purpose of the complex plane is to graph complex numbers in a geometrically meaningful way. Just like how a traditional Cartesian graph (x-y graph) could graph real numbers. So, what is a complex number?

<b>Complex Number</b> - A complex number is any number of the form $$a + bi$$, where $$a$$ and $$b$$ are real numbers, and $$i$$ is the imaginary unit. The imaginary unit is defined as $$i^2 = -1$$. In other words, i is the square-root of negative 1. This might seem impossible, since no single number squared could ever equal a negative number. Well, the truth is, no real number squared could ever equal a negative number. But nothing is stopping mathematicians from inventing a new type of number that indeed can square itself and equal a negative. Mathematicians call these numbers “imaginary numbers”. Now lets relate back to the definition of the complex plane. Remember, complex numbers take up the form $$a + bi$$ (think of b as a scalar to the imaginary unit i). The complex plane’s 2 axes are the real axes (relating to a) and the imaginary axes (relating to b).  So the complex number $$3 + 7i$$ would be graphed as the point 3 units to the right on the the horizontal (real) axis, and 7 units up on the vertical (imaginary) axis.

We are half way there already. Lets take another look at the first half of the definition from above: “…the set of values of c in the complex plane…”. We should now have a good understanding of what this first part means. Basically, we have a set of complex numbers which we will be graphing on the complex plane. But the Mandelbrot set is not composed of all complex numbers, only some. That is where the next part of the definition comes in.

Stay tuned for [part 2][part2], where I will cover how the Mandelbrot set is actually generated, and how beautiful pictures can be made.

[definition]: http://en.wikipedia.org/wiki/Mandelbrot_set
[part2]: {% post_url 2015-09-10-generating-the-mandelbrot-set-with-javascript-part-2 %}
