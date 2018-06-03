---
layout: post
title: "Shell Setup"
date: 2017-12-07 23:31:18
description: Using One File to Configure Multiple Shells
---

I started playing around with different shells (such as fish, zsh, etc.) and I realized that my bash_profile file was setting different environment variables than my zshrc. To further complicate things, fish has its own way of doing things. In this post I describe how I configured my shells to have a more maintainable setup.

The first task was to establish a common file for setting environment variables. This way I wouldn't have to keep various shell setup files up to date every time I add something to my PATH, for instance. After doing some research on the various ways to achieve this, I concluded the ~/.profile file was the ideal place to put the shared setup code. I stripped out the setup from my .bash_profile, .bashrc, and .zshrc and placed what I wanted in .profile. Then I had to add code to the bash and zsh setup files to load the .profile file, since .profile will not be loaded automatically (if other init dotfiles are present, .profile doesn't load automatically).

{% highlight bash %}
if [ -f ~/.profile ]; then
   . ~/.profile
fi
{% endhighlight %}

Now my configuration is consolidated in a single file which automatically loads on bash or zsh startup!

The next problem was getting this to work with fish. Since fish has a different way of setting things up, the easiest way is to parse .profile from within a bash shell, then execute the fish shell once the settings are configured.

I found the following from this [StackExchange][StackExchange] question.

First create /usr/local/bin/fishlogin with contents

{% highlight bash %}
#!/bin/bash -l
fish "$@"
{% endhighlight %}

Make it executable
{% highlight shell_session %}
$ chmod +x /usr/local/bin/fishlogin
{% endhighlight %}

Set it as your default shell
{% highlight shell_session  %}
$ chsh -s /usr/local/bin/fishlogin $USER
{% endhighlight %}

Now my "shell" is set to the executable bash script. This starts up bash, bash loads my .profile, and then fish is fired up. This all happens so fast that when I open up a new terminal I immediately see fish, with no evidence of bash firing up first.

Now all my environment variables and other settings can be configured in a single file, and I can switch shells without having to keep settings between shells in sync!

There is one problem remaining though. Fish seems to be setting PATH in another place as well, because the order in PATH is different in fish vs zsh for me. I searched all the dotfiles in my home directory, but I couldn't find anything else messing with my path. Since fish has multiple config files in different places, I decided to search for all of them.

{% highlight bash %}
find / -name config.fish
{% endhighlight %}

After looking in all of the find results for something that messes with path, I identified the culprit. It was a file called /usr/local/Cellar/fish/2.7.1/share/fish/config.fish

In this file there is a function called "__fish_load_path_helper_paths" which messes with path. Here is the relevent section:

{% highlight bash %}
# OS X-ism: Load the path files out of /etc/paths and /etc/paths.d/*
set -g __fish_tmp_path $PATH
function __fish_load_path_helper_paths
    # We want to rearrange the path to reflect this order. Delete that path component if it exists and then prepend it.
    # Since we are prepending but want to preserve the order of the input file, we reverse the array, append, and then reverse it again
    set __fish_tmp_path $__fish_tmp_path[-1..1]
    while read -l new_path_comp
        if test -d $new_path_comp
            set -l where (contains -i -- $new_path_comp $__fish_tmp_path)
            and set -e __fish_tmp_path[$where]
            set __fish_tmp_path $new_path_comp $__fish_tmp_path
        end
    end
    set __fish_tmp_path $__fish_tmp_path[-1..1]
end

#test -r /etc/paths
and __fish_load_path_helper_paths </etc/paths
for pathfile in /etc/paths.d/*
    __fish_load_path_helper_paths <$pathfile
end
set -xg PATH $__fish_tmp_path
set -e __fish_tmp_path
functions -e __fish_load_path_helper_paths
{% endhighlight %}

I grepped for __fish_load_path_helper_paths to see where it was called. Since it is only called in that section, I decided to comment out this entire block of code.

After resetting fish, I checked my PATH and it matches exactly with the path in zsh! Mission accomplished!

As is the case with some of my other blog posts, this post serves as a way for me to document my own setup in case I ever need to do this again. That being said, I hope someone else finds this useful!


[StackExchange]: https://superuser.com/questions/446925/re-use-profile-for-fish
