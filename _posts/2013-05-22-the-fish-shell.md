---
layout: post
date: 2013-05-22 00:00:00
title: The Fish Shell
---

I switched to the fish shell a while ago and it's great. The sntax is sane and understandable, tab completions work pretty well, history completions save you a lot of time and my shell feels faster to me now.

It's super easy to create a custom shell prompt:

    function fish_prompt
      set_color red -o
      echo -n ">> "
      set_color cyan -o
      if test $PWD = $HOME
        echo -n "~"
      else
        echo -n (basename $PWD)
      end
      echo -n " "
      if git status >/dev/null ^/dev/null
        set_color red -o
        echo -n (git branch | grep '*' | cut -c3-)
        echo -n " "
        if not test (git status | tail -1) = "nothing to commit, working directory clean"
          set_color yellow -o
          echo -n "× "
        end
      end
    end

This displays the current working directory, then the current git branch if one exists, then an ×, if there are uncommitted changes in the git repository.
    
My right prompt displays the ruby version currently in use

    function fish_right_prompt
      set_color white -o
      rbenv version | cut -d ' ' -f 1
    end

All you need to do is create two files, name them `fish_prompt.fish`, `fish_right_prompt.fish` and place them in `~/.config/fish/functions`.

Adding your own tab-completions is also pretty easy. Here's a simple example:

    complete -f -c jekyll -a 'build doctor help import new serve'
    complete -f -c jekyll -s w -l 'watch'

The first line will tab-complete jekyll's `build`, `doctor`, `help`, `import`, `new` and `serve` commands. The second line will complete the `-w` or `--watch` option. Of course, Jekyll has many more commands and options and writing the whole thing would be much longer, but you get the idea. Put these lines in `.config/fish/completions` into a file named `jekyll.fish` and you'll be good to go.

I highly encourage you to at least read through [the introductory fish tutorial](http://fishshell.com/tutorial.html).
