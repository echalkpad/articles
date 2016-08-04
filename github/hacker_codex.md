# Hacker Codex

[Original URL](http://hackercodex.com/guide/mac-osx-mavericks-10.9-configuration/)

> Last updated: June 27, 2015 Want to get started with some command-line tools on Mac OS X? This tutorial covers installing Xcode/CLT, Homebrew, and other useful initial configuration. If you...

<span class="post-date" title="2015-06-27T00:00:00-07:00">Last updated: June 27, 2015</span>

 Want to get started with some command-line tools on Mac 

<span class="caps">OS</span>

 X? This tutorial covers installing Xcode/

<span class="caps">CLT</span>

, Homebrew, and other useful initial configuration.

If you prefer to be in control of when updates are downloaded and installed, open System Preferences, go to the App Store preference pane, and uncheck all the boxes.

## Unhide the Library folder

Just like the last few releases, Mac 

<span class="caps">OS</span>

 X now hides the `~/Library` folder by default, but on Yosemite and Mavericks it is now easier to make it visible again.

With the Finder as the foremost application, press shift-command-H and then command-J, which will bring up a window that configures Finder view options. Check the "Show Library Folder" and close the window. Thanks to the Apple engineers that made this process more user-friendly.

## Bash profile setup

Since Mac 

<span class="caps">OS</span>

 X is a full 64-bit system, we'll save some headaches by letting our compiler know that all compilation should assume 64 bits. Also, since Mac 

<span class="caps">OS</span>

 X's default `PATH` is `/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin`, we'll want to change it so that certain Homebrew installations (e.g., [Python](http://hackercodex.com/guide/python-development-environment-on-mac-osx/)) will take precedence over stock 

<span class="caps">OS</span>

 X binaries. To make these changes, open `~/.bash_profile` ...

... and add:

```
# Set architecture flags
export ARCHFLAGS="-arch x86_64"
# Ensure user-installed binaries take precedence
export PATH=/usr/local/bin:$PATH
# Load .bashrc if it exists
test -f ~/.bashrc && source ~/.bashrc
```

Since the above directives will take effect on the next login, let's go ahead and source the file to ensure it takes effect for the current session:

With those first steps out of the way, now it's time to get the necessary compilation tools in place.

## Compiler

Installing development-related software in the past has required the compiler tool-chain that comes with Xcode. Thankfully, if you don't need or want Xcode, those compiler tools are now available separately, saving download time and about four gigabytes of disk space.

Alternatively, there are some reasons you might want the full version of Xcode:

- To compile the few tools that won't compile without the full version of Xcode
- To download and manually compile open-source Mac applications
- To develop your own Mac/iOS applications

Once you've decided whether you need Xcode or not, run the following command in the Terminal:

You will then be asked whether you want to install Xcode or the command line developer tools, with the latter being the default. Once you've installed one or the other, you can proceed to installing Homebrew.

## Homebrew

Sometimes you may need cross-platform software -- usually without a 

<span class="caps">GUI</span>

 and accessible only via the command line -- that isn't readily available via the Mac App Store. [Homebrew](https://github.com/mxcl/homebrew/) provides an easy way to install these packages, so let's fire up Terminal.app and install it:

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Follow the prompts to download and install Homebrew, entering your administrative password when prompted. Then run the following command to ensure that there aren't any potential problems with your environment. Some warnings (if any) are informational and not necessary actionable; it's just a good idea to at least read through and see if anything looks out of the ordinary.

The following command will update to the latest version of Homebrew and its formulae:

Let's use Homebrew to install some packages that I often find useful:

```
brew install bash-completion ssh-copy-id wget
```

You can run `brew info ssh-copy-id`, for example, if you want to know what those packages do.

Some Homebrew formulae recommend post-installation steps. For example, to activate bash completion, add the following to your `~/.bash_profile`:

```
if [ -f $(brew --prefix)/etc/bash_completion ]; then
 source $(brew --prefix)/etc/bash_completion
fi
```

There are many other useful Homebrew commands, all of which can be seen by running `man brew` in the terminal.

## Just the basics

These are obviously just the basics. If you have any interest in Python, have a look at my guide to setting up a [Python Development Environment on Mac 

<span class="caps">OS</span>

 X](http://hackercodex.com/guide/python-development-environment-on-mac-osx/).

What kinds of customizations have you made to your Mavericks setup? Leave a comment below!
