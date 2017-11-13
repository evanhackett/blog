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

Here is a code example (in a C-like syntax) to make it more clear:

{% highlight javascript %}
function lessThan10(a, b) {
  return a < 10 || b < 10;
}

function infiniteLoop() {
  while (true) {}
}

lessThan10(1, infiniteLoop())
{% endhighlight %}

In the above code, the function lessThan10 returns true if either argument is less than 10. infiniteLoop simply enters an infinite loop. Simple enough, right? Most of you can probably tell that when I call {% ihighlight javascript%}lessThan10{% endihighlight %} passing in {% ihighlight javascript%}1{% endihighlight %} and a call to {% ihighlight javascript%}infiniteLoop(){% endihighlight %} the code will immediately enter an infinite loop.

Now lets take a look at what happens when running the same code using a normal-order language.

<b>Normal Order</b> - Languages that delay evaluation of procedure arguments until the last possible moment (when the actual parameters are needed). This is a form of "lazy evaluation". Haskell and Miranda are examples of languages that use lazy evaluation.

Now things are a little more interesting. Instead of immediately running {% ihighlight javascript%}infiniteLoop(){% endihighlight %} when the function is called, the program will enter the body of {% ihighlight javascript%}lessThan10{% endihighlight %} and short-circuit on the {% ihighlight javascript%}||{% endihighlight %} (or) operator. Since the program never needed to evaluate the formal parameter {% ihighlight javascript%}b{% endihighlight %}, we never called {% ihighlight javascript%}infiniteLoop{% endihighlight %}! This means the function successfully returns {% ihighlight javascript%}true{% endihighlight %}. Essentially we are passing the expression to the function, without yet reducing that expression to a value.

This post was meant to be a brief introduction, and barely scratches the surface of these ideas. Look out for future posts on these topics where I will discuss the practical uses of lazy evaluation, advantages and disadvantages, and how to simulate laziness in non-lazy languages.

In case you are curious, I was introduced to these concepts from the classic book "Structure and Interpretation of Computer Programs". I'm sure there are other sources to get into these topics more deeply, but that is a good place to start if you want to learn more about programming languages.
