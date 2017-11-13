---
layout: post
title: "Hashing Passwords with bcrypt and Storing Them with Bookshelf.js Using Promises"
date: 2015-09-15 18:53:18
image: '/assets/img/'
description: Using Bluebird.js promises to get around some asynchronous funny business
tags:
- bcrypt
- Bookshelf.js
- Promises
- Node
- express
- javascript
categories:
twitter_text:
---

I recently ran into some issues trying to store passwords using the bookshelf ORM after asynchronously hashing them with bcrypt. I’m writing this post to potentially help others who might have this issue in the future, but also so I have a link for myself to refer to in case I ever have this issue again.

First a little background to explain the situation. I was working on an express app, using bookshelf.js as my ORM, and I was trying to hash the passwords for my user models before storing them in the database. Lets take a look at the original implementation:

{% highlight javascript %}
var db = require('../config');
var bcrypt = require('bcrypt-nodejs');

var User = db.Model.extend({
  tableName: 'users',
  hasTimestamps: true,
  initialize: function() {
    this.on('creating', function(model, attrs, options) {
      bcrypt.hash(model.get('password'), null, null, function(err, hash) {
        if (err) {
          console.log('Error in hash creation: ', err)
        } else {
          model.set('password', hash);
        }
      });
    });
  }
});

module.exports = User;
{% endhighlight %}

I have chosen to use the asynchronous version of bcrypt’s hash function because I don’t want my server to be rendered completely useless under a heavy load. After all, bcrypt  is designed to be slow (to make it difficult to brute force the password). Given that I am using the asynchronous version, I attempt to set the password on the user model inside of the callback to bcrypt.hash. What is wrong with this code?

Lets walk through the sequence of events. First I might create a new User somewhere else in my application:

{% highlight javascript %}
var bob = new User({username: 'bob', password: 'abcd'});
bob.save().then(function() {
  // do stuff
}); 
{% endhighlight %}

After calling .save(), a "creating" event is fired. This will trigger our creating handler inside of the initialize function of the user model. The idea is this handler will be invoked before anything is saved to the database. So we should end up storing the hashed password right? Wrong. The way Bookshelf works is it will wait for the creating event to return a value before saving anything into the database. Once a value is returned from the creating handler, the model is signaled to save data to the database. Since the creating handler returns undefined in this case, that still signals to the model that it is free to save to the DB. At some point later, the callback to bcrypt.hash will execute, and in that scope we will set the model’s password to be the hashed password (but the database will have the plaintext password). 

Before giving up and using the synchronous version of bcrypt's hash, there is another way! We can use promises! If the creating handler returns a promise, it won’t signal to the model to save  to the DB until the promise resolves. Let’s refactor our user model to a promise implementation using the Bluebird promise library.

{% highlight javascript %}
var db = require('../config');
var bcrypt = require('bcrypt-nodejs');
var Promise = require('bluebird');

var User = db.Model.extend({
  tableName: 'users',
  hasTimestamps: true,
  initialize: function() {
    this.on('creating', function() {
      var model = this;
      var promiseHash = Promise.promisify(bcrypt.hash);
      return promiseHash(model.get('password'), null, null)
        .then(function(hash) {
          model.set('password', hash);
        }); 
    });
  }
});

module.exports = User;
{% endhighlight %}

As you can see, our creating handler now returns a promise (using a promisified version of bcrypt.hash). Once the promise is resolved (after setting the password in our model to the hashed password) the model is then signaled that it is free to write to the database. We have successfully achieved our goal of asynchronous password hashing within the initialization of a Bookshelf model!