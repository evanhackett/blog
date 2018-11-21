---
layout: post
title: "Normal Order vs. Applicative Order Languages"
date: 2016-10-04 23:31:18
image: '/assets/img/'
description: A brief look at evaluation strategies
tags:
- Programming Languages
categories:
twitter_text:
---

Often times when evaluating programming languages, we consider things such as syntax, type checking rules, abstraction constructs, idioms, available tooling, etc. Indeed these are all important things to consider, but today I am going to talk about an often overlooked aspect of a language: the evaluation strategy.

There are 2 main types of of languages we are going to look at: normal-order, and applicative-order. Most of you have probably used applicative-order languages, so we will discuss applicative-order first.

<b>Applicative Order</b> - Languages that evaluate procedure arguments when the procedure is applied.

This means when a language calls a function passing in an argument, that argument is evaluated (reducing to a value) before the function starts to run. This is pretty standard stuff that most of you are familiar with, due to the fact that most main-stream languages use this form of "strict evaluation".

Here is a code example (in javascript syntax) to make it more clear:

{% highlight javascript %}
function lessThan10(a, b) {
  return a < 10 || b < 10;
}

function infiniteLoop() {
  while (true) {}
}

lessThan10(1, infiniteLoop())
{% endhighlight %}

In the above code, the function lessThan10 returns true if either argument is less than 10. infiniteLoop simply enters an infinite loop. Simple enough, right? Most of you can probably tell that when I call lessThan10 passing in 1 and a call to infiniteLoop() the code will immediately enter an infinite loop.

Now lets take a look at what happens when running the same code using a normal-order language.

<b>Normal Order</b> - Languages that delay evaluation of procedure arguments until the last possible moment (when the actual parameters are needed). This is a form of "lazy evaluation". Haskell and Miranda are examples of languages that use lazy evaluation.

Now things are a little more interesting. Instead of immediately running infiniteLoop() when the function is called, the program will enter the body of lessThan10 and short-circuit on the \|\| (or) operator. Since the program never needed to evaluate the formal parameter b, we never called infiniteLoop! This means the function successfully returns true. Essentially we are passing the expression to the function, without yet reducing that expression to a value.

## You've been using laziness all along

Almost every language I know of has a form of delayed evaluation that you have probably used countless times: the conditional (aka "if") statement. 

"if" statements are special in that you can't directly simulate them with functions. Here is an example of a function that implements a conditional.

{% highlight javascript %}
function cond(predicate, consequent, alternative) {
  if (predicate) {
    return consequent;
  }
  return alternative;
}

cond(5 < 10, "5 is less than 10", "5 is NOT less than 10");
// evaluates to "5 is less than 10" as expected.

{% endhighlight %}

What's the problem? Well, since our eager language evaluates the arguments immediately, we will run into problems if we try to use this like an "if" statement.

{% highlight javascript %}
function factorial(x) {
  return cond(x == 0, 1, x * factorial(x-1));
}

factorial(5); // infinite loop.
{% endhighlight %}

Calling our factorial function with 5, we would expect it to recurse down to the base case where x is equal to 0, and then our cond function would return 1 and stop recursing. However, since all arguments to cond are evaluated, the third argument to cond (the call to factorial(x-1)) will evaluate before cond itself. This means it will never stop recursing!

If we really wanted a proper cond function, we need to delay the evaluation of the consequent and the alternative. We can delay the evaluation by wrapping them with functions, and then calling those functions when we want to evaluate them.

{% highlight javascript %}
function cond(predicate, consequent, alternative) {
  if (predicate) {
    return consequent();
  }
  return alternative();
}

function factorial(x) {
  return cond(x == 0, () => 1, () => x * factorial(x-1))
}

factorial(5); // 120 as expected.
{% endhighlight %}

Notice how cond now calls consequent and predicate since now they are functions. This means when we call cond in factorial, we have to wrap our values in a lambda. Now everything works as expected! In this way, delayed evaluation can be simulated in an eager language.

This post was meant to be a brief introduction, and barely scratches the surface of these ideas. I hope to explore these ideas more deeply in a future post.

In case you are curious, I was introduced to these concepts from the classic book "Structure and Interpretation of Computer Programs". I'm sure there are other sources to get into these topics more deeply, but that is a good place to start if you want to learn more about programming languages. 

For those who want further reading on this topic specifically, here are some resources I found interesting:


[The Point of Laziness][harper] - Robery Harper's blog is a great resource for PL theory in general. He has strong opinions, so make sure to balance out what you are exposing yourself to in order to hear both sides of the argument.

[More Points for Lazy Evaluation][augustss] - A response post to Harper's which offers more pros and less cons of laziness.

[augustss]: http://augustss.blogspot.com/2011/05/more-points-for-lazy-evaluation-in.html
[harper]: https://existentialtype.wordpress.com/2011/04/24/the-real-point-of-laziness/
