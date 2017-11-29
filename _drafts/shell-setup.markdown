---
title: "Shell Setup" 
description: Work in progress
---

I started playing around with different shells (such as fish, zsh, etc.) and I realized that my bash_profile file was setting different environment variables than my zshrc. To further complicate things, fish has its own way of doing things. In this post I describe how I configured my shells to have a more maintainable setup.

The first task was to establish a common file for setting environment variables. This way I wouldn't have to keep various shell setup files up to date every time I add something to my PATH, for instance. After doing some research on the various ways to achieve this, I concluded the ~/.profile file was the ideal place to put the shared setup code. I stripped out the setup from my .bash_profile, .bashrc, and .zshrc and placed what I wanted in my .profile file. Then I had to add code to the bash and zsh setup files to load the .profile file, since that will not be loaded automatically (if other init dotfiles are present). 

{% highlight bash %}
if [ -f ~/.profile ]; then
   . ~/.profile
fi
{% endhighlight %}

Now my configuration is consolidated in a single file which automatically loads on bash or zsh startup!

The next problem was getting this to work with fish. Since fish has a different way of setting things up, the easiest way is to parse .profile from within a bash shell, then execute the fish shell once the settings are configured.

I found the following from this [StackExchange][StackExchange] question.

First create /usr/local/bin/fishlogin with contents

    #!/bin/bash -l
    fish "$@"

Make it executable

    chmod +x /usr/local/bin/fishlogin

Set it as your default shell

    chsh -s /usr/local/fishlogin $USER

Now my "shell" is set to the executable bash script. This starts up bash, bash loads my .profile, and then fish is fired up. This all happens so fast that when I open up a new terminal I immediately see fish, with no evidence of that bash fires up first.

As is the case with some of my other blog posts, this post mostly serves as a way for me to document my own setup in case I ever need to do this again. That being said, I hope someone else finds this useful!


[StackExchange]: https://superuser.com/questions/446925/re-use-profile-for-fish
