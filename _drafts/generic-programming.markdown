---
title:  "Generic Programming in Various Languages"
description: Work in progress
---

In this post I am going to do a quick overview of how to do generic programming (programming with "generics") in several different programming languages. To make things more interesting, I will not be showcasing any 2 languages that have too similar of an approach. For example, C++ and Java generics are very similar, so there is not much to be gained in exploring both of them. It is worth pointing out that generics are different in C++ and Java in terms of how they work under the hood, but that is not the point of this post. This post is from the perspective of the programmer, not the language implementor.

I will be discussing generic programming with:

1. C using void pointers and sizeof  #TODO: rewatch standford paradigms lectures on generic programming in C
2. Java built in generic support with type variables
3. SML with functors # maybe start with a basic example of polymorphic type systems (eg. list length function)
4. Haskell with ...type classes?
5. TODO: lisp dialect. read SICP section 2.4 and 2.5 and watch lecture 4B. for a potential solution. Wikipedia page on generic programming mentions: "Scheme syntactic abstractions also have a connection to genericity -- these are in fact a superset of templating a la C++."

TODO: add link and credit editors / authors for the book.
In the book "Generic Programming: Advanced Lectures", it is stated:

"In a sense, generic programming is about extending the boundaries of static type systems... most generic programs can be readily implemented in a dynamically checked language. (Conceptually, a dynamic lanuges offers one universal data type; programming a function that works for a class of data types is consequently a non-issue.)"
