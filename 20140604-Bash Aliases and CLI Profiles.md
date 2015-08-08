#The Problem

As web developers, we often find ourselves running many things in a Command Line Interface (CLI) while working on a project. The time it takes to start everything up can often be a huge pain, especially when we are continually running the same commands over and over. This post will show you how we can use bash aliases to make our commands more terse and reusable as well as show those of you who use iTerm how you can create Profiles to do the startup work for you and Window Arrangements to launch all your project’s Profiles with a single keyboard shortcut.

For the sake of example we will be using Rails, but this can apply to any project where you consistently find yourself sweating it out in a CLI, be it Gulp, Grunt, Sass, Jekyll, whatever!

> Disclaimer: This is OSx and bash only. Alias stuff should translate over to Linux easily, zsh in theory, but will be more complicated if you’re on a PC. 

> Also, OSx Terminal offers some of the features I will be talking about for iTerm, but not all of them. I recommend [iTerm](https://www.iterm2.com/) since it is free and has the extra features I will be describing in this post.


##Redundancy

Typically for a Rails app, I have at least three iTerm panes open. One for the server, one for the Rails console, and one ready to run commands in the directory. 

In order to “start up” for the day, I have to open three panes in an iTerm window and run commands to set each one up. Those three sets of commands for a rails app in the directory `~/Development/rails_app` are as follows:

```shell
# pane 1, rails server
> cd ~/Development/rails_app && rails s # '&&' runs a second command if first succeeds

# pane 2, rails console
> cd ~/Development/rails_app && rails c

# pane 3, in directory
> cd ~/Development/rails_app
```

After starting up my project, I manually open [Chrome Canary](https://www.google.com/chrome/browser/canary.html) (beta version of Chrome), navigate to `localhost:3000`, then manually open my project in Sublime Text.

In the technologically-saturated goo we call modern existence, there should not be any reason I should manually have to do this multiple times per day—and there isn’t. Combining bash aliases with iTerm Profiles and Window Arrangements can help us waste less time fiddling around in our easily-faded memories trying to rememeber where our files are located and what our setup looks like.

<hr>

#Bash Aliases

Aliases can be defined in many places, but we are going to separate them out into a new file to stay organized. First, we need to create a `.bash_aliases` file in our user directory. This is where we will store our aliases.

```shell
> touch ~/.bash_aliases
```

Now we need to link `.bash_aliases` up to our `.bash_profile` so we have access to our aliases. To do this, we need to modify the `.bash_profile` file in our root user directory (`~`). 

I am going to open the file with Sublime Text via the `subl` command which I have set up on my machine. My wager is on you having that too. If you don’t have it set up, you can either install it right now using the tutorial [Launching Sublime Text 3 From the Command Line](http://olivierlacan.com/posts/launch-sublime-text-3-from-the-command-line/), use `open` and have it open in TextEdit, or use nano/Vim/whatever to edit the file.

```shell
# open bash profile with sublime
> subl ~/.bash_profile

# open without sublime
> open ~/.bash_profile
```

You are now editing `~/.bash_profile`. If you don’t already have something similar, create some whitespace somewhere and write the following code:

```shell
if [ -f ~/.bash_aliases ]; then 
    . ~/.bash_aliases
fi
```

When we “instantiate a CLI session” (open a terminal window), this checks to see if our `.bash_aliases` exists; if it does, it loads it.


###Authoring Bash aliases

Now we can start creating our aliases. Let’s open the `.bash_aliases` file we created earlier.

```shell
# Opening .bash_aliases with Sublime Text
> subl ~/.bash_aliases
```

Think of an alias as a command-line variable. Now stop thinking about aliases. It is that simple. The first thing we will do is make it so we never have to type that command to open our aliases file again. To do this, we will use...an alias!

```shell
# in ~/.bash_aliases
alias aliases="subl ~/.bash_aliases" # open aliases file with Sublime Text
```

Save your `.bash_aliases` file, close it, open a new terminal window (needs to be a new session), and run the following command: 

```shell
> aliases
```

You should now be looking at your `.bash_aliases` file again in Sublime Text or whatever you specified in your alias definition. See? Simple.

###Change Directory Aliases

Let’s apply what we’ve learned to simplifying our Rails app workflow. Back in our `.bash_aliases` file we can define an alias to help us navigate to our rails\_app project.

```shell
# setting an alias for the path to our Rails app’s directory
alias rapp="~/Development/rails_app"
```

You may be thinking the following will work, but you are wrong.

```shell
> cd rapp
```

This will return `-bash: cd: rapp: No such file or directory`. In order to successfully use the alias, it cannot have non-aliases before it in a command. How might we get around this? Another alias of course!

```shell
# 'cd' needs to be an alias so we can chain with aliases
# 'cd aliasname' will not work, but 'to aliasname' will
alias to="cd "
```

The space after `cd` is important in our alias definition. This way it is not recognized as a direct copy of the command `cd`. We will get the same error as before without the space.

In a new CLI session, we can now successfully navigate to our alias directories using the alias `to`.

```shell
> to rapp
```

This is the _exact same thing_ as running `$ cd ~/Development/rails_app`. Easy peasy. But let’s not stop there. 


###Flexibile Aliases

We know we will be using aliases all the time now for every project since they’re so amazing, so it is best for us to not duplicate the `~/Development` path every time we create an alias. We’d have to make a change to every alias if we ever moved or renamed the directory. We can fix that with a variable.

```shell
# storing our development directory in a variable to simplify our definitions
DEV_DIR="~/Development/"

# setting an alias for the path to our Rails app’s directory
alias rapp=$DEV_DIR"rails_app"

# setting an alias for the path to a hypothetical second directory
alias happ=$DEV_DIR"happy_app"

# setting an alias for the path to our Development directory because we can
alias dev=$DEV_DIR
```

Now, if `~/Development/` becomes `~/Projects/Web Development/`, we only need to change one thing in our `.bash_aliases` file.


###Simplifying Aliases

We can also simplify more complicated commands with aliases. A perfect example in my case is opening urls with the Canary build of Chrome.

```shell
# open with chrome canary
alias chrm="/usr/bin/open -a /Applications/Google\ Chrome\ Canary.app --new"
```

Now in a new CLI session, we can run the following command to open `localhost:3000` in Chrome Canary:

```shell
> chrm http://localhost:3000
```


##Aliases Compounding

Here’s the current (condensed) state of our `.bash_aliases` file:

```shell
# 'cd' needs to be an alias so we can chain with aliases
alias to="cd "

# storing our development directory in a variable to simplify our definitions
DEV_DIR="~/Development/"

# setting an alias for the path to our Rails app’s directory
alias rapp=$DEV_DIR"rails_app"

# open with chrome canary
alias chrm="/usr/bin/open -a /Applications/Google\ Chrome\ Canary.app --new"
```

Using aliases, our three pane Rails commands just got a lot simpler.

```shell
# pane 1, rails server
> to rapp && rails s

# pane 2, rails console
> to rapp && rails c

# pane 3, in directory
> to rapp
```

This is great, but we are still manually opening Chrome and manually opening our code. To automate this step, let’s create another alias called `RAPPinit`. It will navigate to the `rails_app` directory, open it in Sublime Text and open `localhost:3000` in Chrome in one breath. We will run this in our third pane. Since our alias will have navigated us to the right directory, it will serve the same function.

```shell
# aliases are case-sensitive
alias RAPPinit="to rapp && subl . && chrm http://localhost:3000"
```

We can then run the following command in our third pane:

```shell
# pane 3, sublime, chrome and in directory
> RAPPinit
```

As you might imagine, nesting aliases inside of aliases can become very powerful. Automate all the things!


<hr>

#Further automation with iTerm

##Profiles

Both OSx Terminal and iTerm have “Profiles” which can run commands on load. I will be using iTerm.

The first thing we will do is go into our iTerm preferences and add a new profile for our first pane.

<figure>
  <img title="iTerm Profile Fig 1" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-01.jpg" />
  <figcaption>A new iTerm profile named "Rails App: Server"</figcaption>
</figure>

We can tell iTerm to run our first command `to rapp && rails s` when we load this profile. The “Send text at start” field will run whatever we put there on initialization. 

<figure>
  <img title="iTerm Profile Fig 2" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-02.jpg" />
  <figcaption>“Rails App: Server” navigating to ‘rails_app’ directory and starting Rails server</figcaption>
</figure>

Now we just repeat for our other two profiles.

<figure>
  <img title="iTerm Profile Fig 3" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-03.jpg" />
  <figcaption>“Rails App: Console” navigating to ‘rails_app’ directory and loading Rails console</figcaption>
</figure>

<figure>
  <img title="iTerm Profile Fig 4" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-04.jpg" />
  <figcaption>“Rails App: Init” running the ‘RAPPinit’ alias</figcaption>
</figure>

All three of these Profiles are now available in the Profiles menu in iTerm. If you click one, it will load the Profile and run the command we gave it in Preferences.

<figure>
  <img title="iTerm Profile Fig 5" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-05.jpg" />
  <figcaption>Accessible profiles in iTerm</figcaption>
</figure>

##Arrangements

Once we have opened all three Profiles in panes, tabs, or windows, we can save the layout as a “Window Arrangement” in iTerm, or as a “Group” in OSx Terminal. 

<figure>
  <img title="iTerm Profile Fig 6" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-06.jpg" />
  <figcaption>Save Window Arrangement of Profiles</figcaption>
</figure>

When you save a Window Arrangement, you will be prompted to name it.

<figure>
  <img title="iTerm Profile Fig 7" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-07.jpg" />
  <figcaption>Name the window arrangement of Profiles</figcaption>
</figure>

If you open the Window Arrangement (Window > Restore Window Arrangement), it will load all three Profiles in the same position as when you saved the Arrangement. 

If you want to delete or manage your Window Arrangements, you can find them in Preferences under “Arrangements”.

<figure>
  <img title="iTerm Profile Fig 8" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-08.jpg" />
  <figcaption>Manage Window Arrangements</figcaption>
</figure>

##Keyboard Shortcuts say what?

Now for the magic. We can *map a friggin keyboard shortcut* to the “Restore Window Arrangement” action! This is the only thing we have covered so far that OSx Terminal cannot do. To map the shortcut, we navigate to “Keys” in iTerm Preferences, create a new shortcut, and in the first dropdown select “Select Menu Item...” and in the second dropdown select our arrangement “Rails App” under “Window > Restore Window Arrangement”.

<figure>
  <img title="iTerm Profile Fig 9" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-09.jpg" />
  <figcaption>Assigning the shortcut of our choosing</figcaption>
</figure>

This shortcut will then be added to iTerm, allowing us to open our Arrangement of Profiles via keyboard shortcut at any time while inside iTerm. Let’s be clear:

>This means one keyboard shortcut will load my Rails server, Rails console, open my project in Sublime and open it Chrome. 

Ridonkulous.

<figure>
  <img title="iTerm Profile Fig 10" src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/111863/iterm-profile-10.jpg" />
  <figcaption>The stored shortcut</figcaption>
</figure>

If you made it through this post, wow. Writing this post was extremely simple in theory but ended up being fairly complicated in execution. Comment if you have questions and I’ll do my best to answer.

That’s it. No more nonsense. You have more time to work! Go!


<hr>
<small>I am Jake Albaugh and am going to write this bio in first person. These days, I write on CodePen because I care more about it and you than I do about my personal site. Read more articles via [my CodePen blog page](http://codepen.io/jakealbaugh/blog/). View my work on [my CodePen profile](http://codepen.io/jakealbaugh/). If you’re a hip millennial, “get at me” on my twitter [@jake_albaugh](http://twitter.com/jake_albaugh).</small>