# How to Use Incron to Monitor Important Files and Folders

[Original URL](https://www.linux.com/learn/how-use-incron-monitor-important-files-and-folders)

> I've seen it happen: a Linux server is taken over by a rootkit and no one was the wiser...at least not until some errant behavior occurred or something outside of the company reported an...

I've seen it happen: a Linux server is taken over by a rootkit and no one was the wiser...at least not until some errant behavior occurred or something outside of the company reported an oddity. After some serious digging, you find out the rootkit has modified a few files or directories and the damage has been done.

What if you knew of a tool that could monitor files for change and then report the changes within /var/log/syslog or take some action when a file was modified? There is such a tool.

Incron is similar to cron, but instead of running a command based on time, it can trigger commands when a file/directory event occurs (e.g., a modification to a file or to a file's permissions). This makes it an outstanding tool to use for monitoring folders like /etc/apache2 or /usr/bin.

I'll walk you through the process of installing and using Incron to monitor such a directory, so that you can always keep tabs on those crucial files and folders.

## Installation

I'll be installing Incron on the Ubuntu 16.04 platform. With one quick command, you can have the tool ready to go. Here's how:

1. Open up a terminal window

2. Issue the command _sudo apt-get install incron_

3. Type your sudo password and hit Enter

4. Type _*_ y* when/if prompted

5. Allow the installation to complete

## Initial Setup

Incron is similar to cron, in that you'll use the _incrontab_ command to create jobs for watching files/folders. Unlike cron, however, you must first specify who can actually use the tool (smart thinking by the developers). To set this up, you must add users to the _/etc/incron.allow_ file. Say you want to enable the root user to use Incron. For this, you would open up _/etc/incron.allow_ and add the following line:

```
root
```

List all of the users you want to give access to Incron, one user per line, in this file. Close the file and those users can now execute the _incrontab_ command without error.

## Incrontab Configuration

As you may have surmised, using _incrontab_ is similar to using _crontab_. You would edit your incrontab file with the command _incrontab -e_. This command will open up the _incrontab_ file for editing. How you use Incron now starts to veer away slightly from Cron. let's see how.

The format of the _incrontab_ looks like:

```
<path> <mask> <command>
```

Let's break that down.

- <path> -- This is the path to the directory you want to watch. Do note the Incron is not capable of watching subdirectories. Only files within the path will be monitored. If you need subdirectories monitored, you must give them their own entry.

- <mask> -- This is one of several options:

  - _IN_ACCESS_ File was accessed (read)

  - _IN_ATTRIB_ ** Metadata changed (permissions, timestamps, extended attributes, etc.)

  - _IN_CLOSE_WRITE_ ** File opened for writing was closed

  - _IN_CLOSE_NOWRITE_ File not opened for writing was closed

  - _IN_CREATE_ File/directory created in watched directory

  - _IN_DELETE_ File/directory deleted from watched directory

  - _IN_DELETE_SELF_ Watched file/directory was itself deleted

  - _IN_MODIFY_ ** File was modified

  - _IN_MOVE_SELF_ Watched file/directory was itself moved

  - _IN_MOVED_FROM_ ** File moved out of watched directory

  - _IN_MOVED_TO_ ** File moved into watched directory

  - _IN_OPEN_ ** File was opened

- <command> -- This is the command that will run should an event be triggered. In place of a command, you can always use wildcards. The wildcards will report basic information in syslog. The available wildcards are:

  - $$ Prints a dollar sign

  - $@ Add the watched filesystem path

  - $# Add the event-related file name

  - $% Add the event flags (textually)

  - $& Add the event flags (numerically)

## Usage

Let's set up a simple incrontab, using wildcards, and see how this works. First, let's assume we've added the user olivia into the incron.allow file. We'll create the folder /home/olivia/TEST and then run the command _incrontab -e as user_ _olivia_. With the editor open, add the following line:

```
/home/olivia/TEST IN_MODIFY echo "$$ $@ $# $% $&"
```

Save the file and do the following:

1. Open up a second terminal window and issue the command _tail -f /var/log/syslog_

2. In the first terminal window, create the file /home/olivia/TEST/test

3. Add some text to the file and save it

If you check the tail running on /var/log/syslog, you should see entries as shown in Figure 1.

Although that clearly indicates to you that the contents of the TEST folder were modified, we can make this a bit more useful. Say, for example, you want to have your web server automatically shut down if the contents of /etc/apache2/apache2.conf are modified (it's drastic, but illustrates the tool perfectly). For this, you would need to edit the root user's Incrontab file.

Here's what you would want to do. Open up a terminal window and issue the command sudo su. Enter your sudo password and then issue the command _incrontab -e_. With the Incrontab editor open, add the following:

```
/etc/apache2/apache2.conf IN_MODIFY /usr/sbin/service apache2 stop
```

Save and close the file. You can test this by opening the _/etc/apache2/apache2.conf_ file and making some minor change. Save the edited file and your web server should immediately stop.

You could also have Incron watch multiple folders/files and employ the same action. Suppose you want to watch both the _/etc/apache2/apache2.conf_ file and the _/var/www/html/index.html_ file for changes. You could create two incrontab entries like so:

```
/etc/apache2/apache2.conf IN_MODIFY /usr/sbin/service apache2 stop

/var/www/html/index.html IN_MODIFY /usr/sbin/service apache2 stop
```

Should either file be modified, the web server will stop.

This, of course, is only a simple example of how you can make use of Incron. Get creative and see just how far you can bend the will of Incron to meet your needs. This is Linux, after all...it is ready be used in ways other platforms only dream of.
