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

Lately my interest in functional programming has been growing and growing. The only thing I like more than learning is teaching, so I decided to start a blog series on FP. I thought long and hard about the best place to start (lambda calculus, mathematical functions, algebra basics, etc.) and ultimately I decided to think back to what originally got me interested in functional programming and start from there. I am excited to get to the deeper and "cooler" stuff, but I thought it would be fun to revisit my history with FP and kick off this series with what started it all (for me): higher-order functions!

The first time I saw higher-order functions was during my interview with Hack Reactor. The interviewer had me implement map, reduce, and other commonly used higher-order functions. I had actually never used these functions before, but more importantly I had never written or used any higher-order function at all! I essentially had to learn right then and there how these things work if I wanted a chance at getting accepted into Hack Reactor. Luckily I had a good interviewer who answered my questions and helped nudge me in the right direction. As I was implementing these functions (in javascript, a language I was very new with), my mind was blown due to the powerful abstraction technique I was learning. I interviewed with other web development schools, and this interview was the sole reason I chose to go to Hack Reactor over the others. I actually learned a ton and got exposed to some really cool stuff just in that 1 hour interview. I knew this was going to be unlike the other schools if I already learned a bunch of cool stuff just during the interview process! Pretty much as soon as the interview was over, I did some research online. I immediately recognized how powerful this abstraction technique of higher-order functions could be. This research into higher-order functions (where they came from, what languages support them, how to use them effectively) was what lead me to "functional programming". I had just barely heard of functional programming before in my intro to CS class in college. The instructor was teaching us recursion and talked about how some languages don't have loops, so you HAVE to use recursion. I always thought that sounded cool, but not practical. At this point I was having second thoughts, since the power of functions was becoming more and more clear to me. Perhaps these functional programming languages are worth learning after all! I also recalled reading Paul Graham essays (TODO: link) on programming languages, specifically on the value of lisp. I recalled Eric Raymond mentioning lisp (TODO: link), as well as Peter Norvig (TODO: link), on "how to learn programming" type of articles you can find on their sites. All of a sudden it hit me that functional programming languages are almost universally recommended by experienced programmers, and for good reason. As I continued to learn more I realized many of my favorite programming language features originated in functional languages. I was hooked.

Ok, enough back-story. Lets get to the technical stuff.

What is a higher-order function? Simply, it is a function that either takes another function in as one of its argument, or returns a function as a result (or both). A great example from calculus would be the integral and derivative operators. When you differentiate or integrate, you are applying the derivative or integral "operator" to some other function. If we use more of a programming vocabulary, we might say you pass the function you want to derive to the derivative function, and it will return that function's derivative (another function). If you haven't studied calculus, don't worry, we will look at some non-math examples.

Lets start with a simple text cipher. Today I'm feeling Python for my language of choice.

{% highlight python %}
def encrypt(string, key):
    encrypted_string = ""
    for char_code in string.encode('ascii'):
        if char_code > 31 and char_code < 127:
            encrypted_code = (char_code + key) % 126
            if encrypted_code < 32:
                encrypted_code += 32
            encrypted_string += chr(encrypted_code)
    return encrypted_string

print(encrypt("abcd", 7)) # "hijk"
print(encrypt("hijk", -7)) # "abcd"
{% endhighlight %}

This is essentially a Caesar Cipher. We shift each character over some amount according to a key. Shifting "abcd" by the key 7 results in "hijk". Shifting "a" by 1 would give us "b". The modulus 126 is for wrapping around the 127 ascii values. Since ascii starts at 0, we don't want to go over 126. The if statements are just so we only mess with printable characters. Check out an ascii table (TODO: add link) if you aren't familiar.

So we have this cool encrypt function we are using to encrypt text.

 It nice and all, but Caesar ciphers are a little boring. Lets create another encrypt function!

{% highlight python %}
def encrypt2(string, key):
    encrypted_string = ""
    for i, char_code in enumerate(string.encode('ascii')):
        encrypted_code = char_code ^ ord(key[i])
        encrypted_string += chr(encrypted_code)
    return encrypted_string

print(encrypt2('abcd', 'test')) # some crazy symol that I can't copy/paste into my text editor
print(encrypt2(encrypt2('abcd', 'test'), 'test')) # 'abcd'
{% endhighlight %}

This is a simple XOR cipher. It takes in a string as a key and XORs each character we are encrypting with the corresponding character from the key. Once again we can get back the original by applying the encryption again. This time instead of using the negative of the key (to shift backwards in the Caesar cipher), we just use the exact same key again. Read more about XOR encryption if you are interested in how this works (TODO: add link).
