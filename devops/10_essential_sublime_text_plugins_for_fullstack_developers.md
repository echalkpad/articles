# 10 Essential Sublime Text Plugins for Full-Stack Developers

[Original URL](http://www.sitepoint.com/10-essential-sublime-text-plugins-full-stack-developer/)

> When I started with web development a few years ago, Vim was my first choice of text editor. It was easy to work with and I could get the basics done without much hassle. Also, many developers like...

When I started with web development a few years ago, Vim was my first choice of text editor. It was easy to work with and I could get the basics done without much hassle. Also, many developers like terminal based text editors because they get the same environment in both their local development machines and remote servers. In spite of the "[Vim vs Emacs](http://stackoverflow.com/questions/1430164/differences-between-emacs-and-vim)" debate out there, about a year ago I decided to try out a native text editor and Twitter was abuzz with one of them (no prizes for the guessing which one.)

The creators of Sublime Text say it's a text editor you'll fall in love with and, having worked with it for almost a year now, I must say I completely agree with them. It has an untimed trial, and a licence for [a single user costs $70](https://www.sublimetext.com/buy). If you spend most of your day working with a text editor, I would say it's a worthy investment!

What makes Sublime Text even better is its extensibility. So, here's a look at the plugins that make an already wonderful editor truly Sublime.

## 1\. [Package Control](https://sublime.wbond.net/installation)

One way of installing Sublime Text plugins is by downloading files and copying them to the packages directory. However, you should go through that process exactly once, because there exists a plugin called Package Control: a package manager, enabling you to install other plugins without leaving Sublime Text. It's like `apt-get` for Ubuntu, `pip` for Python and `npm` for node.js.

Follow the installation instructions here and you won't have to install another plugin manually ever again.

To verify that it's been correctly installed, press `Ctrl/Cmd + Shift + p` and type in 'package control' -- you should be able to view a list of options.

![Package Control](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/1404716305packagemgr.png)

## 2\. [Git](https://github.com/kemayo/sublime-text-git)

These days, more often than not, you are going to work with a version control software, and the most popular VCS is Git. Are you tired of saving your text files and switching back to the terminal to run a few Git commands. Wouldn't it be nice if you could execute Git commands from the text editor itself? Install the Git plugin and get more done in less time!

![Running Git Diff](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/1404716473gitsublime.png)

## 3\. [GitGutter](https://github.com/jisaacks/GitGutter)

Although you can run Git commands from within Sublime Text, why check the differences in a file from the last commit by running a separate command when you can view it in real time?

With GitGutter, you can see which lines have been added, deleted or modified in the gutter.

![Git Gutter Usage](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/1404716519Gitgutter.png)

## 4\. [Emmet](http://emmet.io/)

Emmet is a useful plugin that saves time by making you write less, thus increasing your productivity. Emmet is available for other text editors like Notepad++ and Eclipse.

There are [a lot of things](http://www.hongkiat.com/blog/html-css-faster-emmet/) that you can accomplish with Emmet, but I will just tell you my favorite here. Type `html:5` and press `Ctrl/Cmd + e`, and it is expanded to a basic HTML 5 page template. Simple!

![Emmet Demo](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/1404716567emmet.png)

## 5\. [AllAutocomplete](https://github.com/alienhard/SublimeAllAutocomplete)

Sublime Text's default autocomplete considers words that are present in the current file only. The AllAutocomplete plug-in, however, searches all open files to find matches while suggesting words.

![AllAutocomplete Demo](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/1404716612AllAutocomplete-demo.png)

## 6\. [Terminal](https://github.com/wbond/sublime_terminal)

Just in case you want to open a terminal in the directory of your current file, this plugin can be of use. However, by default, it sets `Ctrl/Cmd + Shift + t` as the shortcut for opening the terminal, which is also the shortcut to open the last closed file. You should change either of the shortcuts to be able to use both functionalities!

This is probably the most useful plugin for programmers. SublimeREPL lets you run an interpreter of a range of languages (NodeJS, Python, Ruby, Scala and Haskell to name a few) right inside Sublime Text. Let us run a Python interpreter and see if it works. Perform some `list` checks and computed 48 raised to the power 100.

![Running Python in Sublime Text](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/1404716655REPL.png)

## 8\. [ColorPicker](http://weslly.github.io/ColorPicker/)

Usually, if you want to use a color picker you probably open Photoshop or GIMP and use the built-in color picker there. The ColorPicker plugin lets you use a color picker within Sublime Text! After installation, just press `Ctrl/Cmd + Shift + c`.

![The Color Picker Plugin](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/1404716694colorpicker.png)

## 9\. [MarkdownPreview](https://github.com/revolunet/sublimetext-markdown-preview)

Although many developers prefer to create Markdown files in the cloud (GitHub Gists, StackEdit, Markable), this is for the 'old school' writers who prefer to keep their files locally. Although MarkdownPreview is primarily to preview Markdown files, you can go one step further and install [MarkdownEditing](https://github.com/SublimeText-Markdown/MarkdownEditing), which gives you proper color highlighting.

![MarkdownPreview Options](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/1404716754markdown.png)

## 10\. [DocBlockr](https://github.com/spadgos/sublime-jsdocs)

If you follow coding guidelines strictly, this is one plugin that makes your task easier. DocBlokr helps you in creating proper comments for your code, by parsing the functions, parameters, variables, and automatically adding the basic items. Start with "/**" and DocBlockr does the rest for you. For instance, check how DocBlockr makes my life easier by creating a format for me to fill based on my comment.

![DocBlockr Demo](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2014/07/1404716806DocBlockr.png)

With this, we come to the end of our list of plugins to boost your productivity. Did we miss out any important ones? Do let us know your favorites in the comments below.
