# How To Set Up Automatic Deployment with Git with a VPS

[Original URL](https://www.digitalocean.com/community/tutorials/how-to-set-up-automatic-deployment-with-git-with-a-vps)

> For an introduction to Git and how to install, please refer to the introduction tutorial. This article will teach you how to use Git when you want to deploy your application. While there are many...

For an introduction to Git and how to install, please refer to the [introduction tutorial](https://www.digitalocean.com/community/articles/how-to-install-git-on-ubuntu-12-04).

This article will teach you how to use Git when you want to deploy your application. While there are many ways to use Git to deploy our application, this tutorial will focus on the one that is most straightforward. I assume you already know how to create and use a repository on your local machine. If not, please refer to [this tutorial](https://www.digitalocean.com/community/articles/how-to-use-git-effectively).

When you use Git, the workflow generally is toward version control only. You have a local repository where you work and a remote repository where you keep everything in sync and can work with a team and different machines. But you can also use Git to move your application to production.

## Server Setup

Our fictitious workspace:

Your server live directory: _/var/www/[domain.com](http://domain.com)_

Your server repository: _/var/repo/site.git_

What should we do if we want to push to _site.git_ and at the same time make all the content available at _/var/www/[domain.com](http://domain.com)_?

## Creating Our Repository

Login to your VPS from command line and type the following:

```
cd /var
mkdir repo && cd repo
mkdir site.git && cd site.git
git init --bare
```

`--bare` means that our folder will have no source files, just the version control.

## Hooks

Git repositories have a folder called _'hooks'_. This folder contains some sample files for possible actions that you can hook and perform custom actions set by you.

[Git documentation](http://git-scm.com/book/en/Customizing-Git-Git-Hooks) define three possible server hooks: _'pre-receive'_, _'post-receive'_ and _'update'_. _'Pre-receive'_ is executed as soon as the server receives a _'push'_, _'update'_ is similar but it executes once for each branch, and _'post-receive'_ is executed when a _'push'_ is completely finished and it's the one we are interested in.

In our repository if you type:

```
ls
```

You will see a few files and folders, including the _'hooks'_ folder. So let's go to _'hooks'_ folder:

```
cd hooks
```

Now, create the file _'post-receive'_ by typing:

```
cat > post-receive
```

When you execute this command, you will have a blank line indicating that everything you type will be saved to this file. So let's type:

```
#!/bin/sh
git --work-tree=/var/www/domain.com --git-dir=/var/repo/site.git checkout -f
```

When you finish typing, press _'control-d'_ to save. In order to execute the file, we need to set the proper permissions using:

```
chmod +x post-receive
```

You can see on the [documentation](https://www.kernel.org/pub/software/scm/git/docs/) that _'git-dir'_ is the path to the repository. With _'work-tree'_, you can define a different path to where your files will actually be transferred to.

The _'post-receive'_ file will be looked into every time a push is completed and it's saying that your files need to be in _/var/www/[domain.com](http://domain.com)._

## Local Machine

Let's create our local repository. You should change the path and name to whichever you choose. If you are on a VPS, just type:

```
exit
```

And create your repo:

```
cd /my/workspace
mkdir project && cd project
git init
```

Then we need to configure the remote path of our repository. Tell Git to add a remote called _'live'_:

```
git remote add live ssh://user@mydomain.com/var/repo/site.git
```

Here we should give the repository link and not the live folder.

Let's assume that we have some great work ready in this folder. We should do the usual steps of adding the files and commit with a message:

```
git add .
git commit -m "My project is ready"
```

Just to remember, the dot after _'git add'_ means you are adding all files to stage. After _'git commit'_ we have _'-m'_ which means we will type a message. To complete, we just _'push'_ everything to the server. We use the _'live'_ alias that we used when setting the remote.

```
git push live master
Counting objects: 7, done.Delta compression using up to 4 threads.Compressing objects: 100% (7/7), done.Writing objects: 100% (7/7), 10.56 KiB, done.Total 7 (delta 0), reused 0 (delta 0)To ssh://user@mydomain.com/var/repo/site.git* [new branch] master -> master
```

Here we tell Git to push to the _'live'_ remote on the _'master'_ branch. To understand more about branches and how to use it you can read [this tutorial](https://www.digitalocean.com/community/articles/how-to-use-git-branches).

## Beta

What if you don't want to deploy everything in one step? Maybe you want to test it first and have a beta directory.

One of the ways to do that is create another repository. Let's log in again in our VPS and create our directory:

```
cd /var/www/
mkdir beta
```

To create our repository:

```
cd /var/repo
mkdir beta.git && cd beta.git
git init --bare
```

Again we should create the _'post-receive'_ file because we want to see our project in the beta directory:

```
cd hooks
cat > post-receive
```

Type the content of the file:

```
#!/bin/sh
git --work-tree=/var/www/beta --git-dir=/var/repo/beta.git checkout -f
```

Press _'control-d'_ to save.

Let's go back to our local repository:

```
exit
cd /my/workspace/project
```

So now we can set another remote pointing to our beta repository:

```
git remote add beta ssh://user@mydomain.com/var/repo/beta.git
```

With this, we can have a two step process. First we push to beta and check, and if everything is fine we push to live:

```
git add .
git commit -m "New version"
git push beta master
```

And later:

```
git push live master
```

## Going Live From the Server

Maybe you have a team working in the same project and you want that others can also decide that it's time to go live. To do this, we can link the beta and live repository on the server. Log in to your VPS and type:

```
cd /var/repo/beta.git
git remote add live ../site.git
```

So now you can push from beta to live on the server:

```
cd /var/repo/beta.git
git push live master
```

Congratulations! Your VPS is now set to automatically deploy with Git!
