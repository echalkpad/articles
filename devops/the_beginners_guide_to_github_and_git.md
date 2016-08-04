# The Beginner's Guide to Github and Git

[Original URL](https://webdesignviews.com/beginners-guide-github-and-git/)

> Paula Borowska | January 10, 2016 | CodingIn short, Git is a version control software. It means it helps manage changes to a project without overwrites and loss of work. Therefore, if something gets...

[Paula Borowska](https://webdesignviews.com/author/paulaborowska/ "Posts by Paula Borowska") | January 10, 2016 | [Coding](https://webdesignviews.com/category/coding/)

In short, Git is a version control software. It means it helps manage changes to a [project](https://webdesignviews.com/tips-focusing-attention-project-work/) without overwrites and loss of work. Therefore, if something gets messed up, broken or overridden Git is going to help you get back the otherwise lost work.

It was developed by Linus Torvalds who also created Linux. He created [Git](https://git-scm.com/) to help develop the core of Linux. Git is pretty old actually which is why its main use is in the terminal. It's possible to use the online software Github without using Git or the commend lines in terminal, but it doesn't have the same flexibility as using both in the terminal. In this tutorial, we will be doing exactly that.

## Setting Up a Github Account and Installing Git

First things first we need to get you set up with both a [Github](http://github.com) account and installing Git on your own computer. Setting up a Github account is pretty simple. You'll have to head to the Github Join page and fill out the free account. They have paid options too for private repositories but for the purpose of this tutorial you won't need that at all.

As far as installing Git goes, it too is fairly easy – there is a reference guide for it on Git's website. They have a walkthrough for [Linux](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git#Installing-on-Linux), [Mac](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git#Installing-on-Mac), and [Windows](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git#Installing-on-Windows).

![Github](https://webdesignviews.com/media/2016/17353/github.jpg)

## Creating a Repository

Now the fun parts, getting acquainted with the terminal commands. The first thing we'll do is create a repository, or a repo, folder for a project. Repos are where your applications will live. First you need to pick its location locally. I'm sure you know it's best to keep all projects in one place. You'll need to locate the directory where all your projects lie. There are three commands you need to know in order to do so.

```
pwd
```

This command let's you check what path you're currently in.

```
cd ..
```

cd stands for change directory. This command lets you move up one directory from the current one. Basically it's moving out of the folder you're in and into it's enclosing one.

```
cd [folderName]
```

Lets you go into a desired directory. You can do this one folder, directory, at a time with the above example. Or you can do it all at once with the following trick if you know exactly where you want to go: cd [folderName]/[folderName]/[folderName]/[folderName]

Now onto creating a repo.

``

```
git init
```

Do this in your desired location only as this command initializes an empty Git repository. When you run this common in the terminal you'll get feedback: 'Initialized empty Git repository'. This means you've been successful.

### Branches, Local and Remote

Branches are a way to develop features outside of your current workflow. What happens is that the code you've been working off your master branch is being copied so you can create your own thing, like a new feature without messing up the master branch. Branches come in especially handy when there are multiple developers working on the same project but not necessarily on the same thing at once. Master is the default branch created for any project.

``

```
git checkout -b [branchName]
```

This command creates a new branch for you and switches you into it. ``

```
git checkout [branchName]
```

If you'd like to switch back to master, or any other branch for that matter you'd use the above command. Git checkout master would be used to switch into the master branch. ``

```
git push origin [branchName]
```

In order for others to keep track of your work you need to push up a branch to the server, in this case, Github. The above command does exactly that.

### Checking on Your Status

``

```
git status
```

This command will show you everything that has happened in your repository, or that nothing in fact has. It's useful to know what branch you're currently on and what files have been modified. By modified I mean files that have been changed, added or removed.

### Pulling Previous Changes

``

```
git pull
```

You use this command to update your local repo. Say I've made a change to our repo, you'd pull in order to get those updates to your own computer. It's good practice to pull often so that everything is updated. You'll also avoid many merge issues if you pull often but more on those later.

### Adding Files to Your Commit

``

```
git add [fileName]
```

While you're making changes to your project, you may end up creating new files too. In order for Git to keep track of them, you need to run the command: git add. You can add them manually by specifying the name of the file you'd like to add or try a shortcut. ``

```
git add *
```

The above command will add everything you've newly created to your repo. It's easier to do this once you've added a bunch of files but you have to make sure you don't actually add something you did not want to.

### Committing Your Files to Your Prep

``

```
Git commit
```

Once you've made all the necessary changes, it's time to save your work. Commits do exactly that. You can simply run git commit and it will do the trick for you. But it's best practice to commit with messages so that you know exactly what changes you are making. ``

```
git commit -m “your commit message here”
```

The message goes between the quotes. -m means you're adding a message to your command. You can make as many commits as you'd like. I'd actually recommend it so it's easier to keep track of what you're doing. Making bigger commits, when you do them less often, means there is a lot more that could go wrong within the code. It's more cautionary that way.

### Pushing Your Changes to the Repo

When you commit, it still stays on your computer locally. You need to push them to the remote repository in order to give access to them for everyone else involved in the project. We went over already how to push up your branch, now let's talk about your actual changes.

``

```
git push
```

The simple command of git push pushes your changes to the remote repo.

### Merging Branches

Once your feature is developed on a branch you should merge it with master so that it's going to be included in it from now on. First you need to change into your master then run the merge command. (You can merge between other branches as well but for the sake of this tutorial, let's keep going with master.)

``

```
git checkout master
git merge [branchName]
```

A good practice before merging is to run the following command: ``

```
git diff [sourceBranch] [targetBranch]
```

This command will display the list of differences between your branches so that you can make sure you're about to merge the right stuff.

### What are Merge Conflicts?

Sometimes things will go wrong and you'll be overriding something with your merge. When that happens the terminal will tell you. Your file will change to show the points of friction in your code. It will look something like this:

``

```
How old am I?
< <<<<<>>>>>> [branchName]
```

What the above code means is that the line where is says 24 years old conflicts with the original 23 years old. HEAD means it's the current code whereas the code below ======= is your branch's code, whatever the branch is named.

What you do then is simply delete the form the arrows and pick one of the options it gave you, either the original, HEAD code or your branch's code.

I'd change the code to look as follows:<br>
``

```
How old am I?
24 years old
```

Then you commit again. It's just the nature of the game. If you're smart about how you use your commits and pulls you'll run into merge issues less often.
