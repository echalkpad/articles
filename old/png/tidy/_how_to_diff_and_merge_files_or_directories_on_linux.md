# How to diff and merge files or directories on Linux

[Original URL](http://xmodulo.com/diff-merge-files-directories-linux.html)

> There are many cases where you want to compare and/or merge two files or directories. For example, you may want to compare two distinct backup snapshots; merge two different versions of a document;...

There are many cases where you want to compare and/or merge two files or directories. For example, you may want to compare two distinct backup snapshots; merge two different versions of a document; diff two configuration files for troubleshooting, etc. While version control systems can handle this kind of situations easily, it is probably an overkill for average Linux users who are looking for simple diff and merge solution.

In this tutorial, I describe **how to diff and merge files and directories on Linux by using a GUI tool called Meld**.

[Meld](http://list.xmodulo.com/meld.html) is a **visual diff and merge tool** that can compare files, directories, and any version controlled projects. Meld offers two- and three-way diff and merge, and supports simple commands of major version control systems (e.g., SVN, Git, Mercurial, Bazaar).

## Install Meld on Linux

To install Meld on Debian, Ubuntu or Linux Mint:

$ sudo apt-get install meld

To install Meld on CentOS or RHEL, first [enable Repoforge repository](http://xmodulo.com/how-to-set-up-rpmforge-repoforge-repository-on-centos.html), and then run:

$ sudo yum install meld

To install Meld on Fedora, simply run:

$ sudo yum install meld

After installation, you can start Meld as follows.

$ meld

To start a comparison, click on the comparison icon at the top. Then you can choose between file/directory comparison or version control browser modes like the following.

## Compare and Merge Two Files

You can visually compare the difference between two files.

You can merge individual changes selectively in either direction, or apply all changes in one direction.

## Three-Way File Comparison

You can compare three different files or directories side-by-side.

## Compare and Merge Two Directories

You can compare two or three directories and apply the diff (i.e., copy or delete files) between them.

## Customize Meld Filtering Rules

You can customize Meld so that it can filter out uninteresting differences. The filtering rule can be defined for files as well as directories by regular expressions as shown below.

## Nautilus Integration

Meld provides Nautilus integration, so you can diff and merge files or directories directly in Nautilus file manager. To install Nautilus plugin for Meld on Ubuntu desktop, run the following commands.

$ sudo apt-get install meld nautilus-compare<br>
$ nautilus -q

Once you have installed Nautilus plugin for Meld, you will see an additional context menu called "Compare" when you right-click two files. The following screenshots show how Meld works on Nautilus.

<span>Download this article as ad-free PDF (made possible by <a href="https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&amp;hosted_button_id=PBHS9R4MB9RX4">your kind donation</a>): </span>

### Subscribe to Xmodulo

Do you want to receive **Linux FAQs, detailed tutorials and tips** published at Xmodulo? Enter your email address below, and we will deliver our Linux posts straight to your email box, for free. Delivery powered by Google Feedburner.

### Support Xmodulo

Did you find this tutorial helpful? Then please be generous and [support Xmodulo!](http://xmodulo.com/about)
