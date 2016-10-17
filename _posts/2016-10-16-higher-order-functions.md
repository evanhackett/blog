---
layout: post
title: "Higher-Order Functions"
date: 2016-10-16 18:40:18
image: '/assets/img/'
description: Functional Programming Foundations - 1
tags:
- Functional Programming
- Algebra
- FP
categories:
twitter_text:
---

Lately my interest in functional programming has been growing and growing. I decided to start blogging about all the cool FP stuff I have been learning about, but I didn't know where to start. I decided to think back to what originally got me interested in functional programming and start from there. I plan to blog more about FP stuff because I really want to share the deeper "cooler" stuff, but for now I am starting with the basics: higher-order functions!

The first time I saw (or at least the first time I was aware) higher-order functions was during my interview with Hack Reactor. The interviewer had me implement map, reduce, and other commonly used higher-order functions. I had actually never used these functions before, but more importantly I had never written or used a higher-order function. I essentially had to learn right then and there how these things work if I wanted a chance at getting accepted into Hack Reactor. Luckily I had a good interviewer who answered my questions and helped nudge me in the right direction. As I was implementing these functions (in javascript, a language I was very new with), my mind was being blown. I interviewed with other web development schools, and this interview was the sole reason I chose to go to Hack Reactor over the others. I actually learned a ton and got exposed to some really cool stuff just in that 1 hour interview. Pretty much as soon as the interview was over, I did some research online. I immediately recognized how powerful this abstraction technique of higher-order functions could be. This research into higher-order functions -- where they came from, what languages support them, how to use them effectively -- was what lead me to "functional programming". I had just barely heard of functional programming before in my intro to CS class in college. The instructor was teaching us recursion and talked about how some languages don't have loops, so you HAVE to use recursion. I always thought that sounded cool, but not practical. At this point I was having second thoughts, since the power of functions was becoming more and more clear to me. Perhaps these functional programming languages are worth learning after all! As I continued to learn more I realized many of my favorite programming language features originated in functional languages.

Ok, enough back-story. Lets get to the technical stuff.

What is a higher-order function? Simply, it is a function that either takes another function in as an argument, or returns a function as a result (or both). A great example from calculus would be the integral and derivative operators. When you differentiate or integrate, you are applying the derivative or integral "operator" to some other function. If we use more of a programming vocabulary, we might say you pass the function you want to derive to the derivative function, and it will return that functions derivative (another function). If you haven't studied calculus, don't worry, we will look at some non-math examples.
