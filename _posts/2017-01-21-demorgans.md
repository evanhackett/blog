---
layout: post
title: "Using De Morgan's Laws for Simpler Regex"
date: 2017-01-21 18:40:18
image: '/assets/img/'
description: Propositional logic and programming
tags:
- Logic
- Regex
- Regular Expressions
categories:
twitter_text:
---

Often times tools from propositional logic can be useful while programming. A good example of this came up when I was writing some password validation code. As is common, I needed to make sure the password was at least 8 characters, contained at least 1 uppercase, 1 lowercase, and 1 number. Pretty standard. Instead of doing it with for-loops and if-statements (or filters and predicates), I needed to do it the regular expressions. This was because the ORM for the data model had a built in validator that accepted regex objects. So all I had to do was construct the proper regex, hand it to the ORM's validator, and then whenever a corresponding model object tries to save, the ORM would run the validation on it. Good stuff. But I hadn't worked with regex very much, so I had a little trouble coming up with the regex I needed. De Morgan's Laws to the rescue!

Often times you write a regex that follows a general pattern: a valid string contains at least 1 of X, AND at least 1 of Y, AND at least 1 of Z. Writing a regex for this pattern can be surprisingly difficult. This is because there is no logical AND operator in a regex. Since there is an OR operator, we can transform this structure into an equivalent one that is easier to code a regex for by applying De Morgan's laws.

Here are De Morgan's laws:
{% highlight javascript %}
!(a && b) == (!a || !b)
!(a || b) == (!a && !b)
{% endhighlight %}

In english: "The negation of a conjunction is the disjunction of the negations; and
the negation of a disjunction is the conjunction of the negations."

Applying the laws, we can obtain the structure: an invalid string contains no X, OR no Y, OR no Z. Notice how the "valid" switched to an "invalid". As you can see, we are now matching on invalid strings instead of valid ones.



{% highlight javascript %}
function invalid(password) {
  // Matches anything that contains less than 8 chars OR no numbers OR no uppercase OR no lowercase.
  return /^(.{0,7}|[^0-9]*|[^A-Z]*|[^a-z]*)$/.test(password);
}  

if (invalid("abc123"))
  console.log("INVALID!") // will log since "abc123" doesn't contain an uppercase.

{% endhighlight %}

I'm not going to go into detail about the regex itself, since this post wasn't meant to be an introduction to regex. I just wanted to show a cool trick using stuff from propositional logic.

All that being said, I usually prefer to use a sequence of filters, since I generally don't like working with regex. So I might prefer to write code like this (depending on how complex the regex):

{% highlight javascript %}
function valid(password) {
  const chars = password.split("");

  const lowers = chars.filter(isLower);
  const uppers = chars.filter(isUpper);
  const numbers = chars.filter(isDigit);

  // will return 0 (false) if any array is empty
  return lowers.length && uppers.length && numbers.length;
}
{% endhighlight %}

Even though it is more code, I probably can code things like that quicker than if I tried to use regex. That being said, the De Morgan's law trick is pretty useful!
