# How to search text files for patterns efficiently

[Original URL](http://xmodulo.com/search-text-files-patterns-efficiently.html)

> For a system admin or a programmer, grep and the likes are probably the most popular tools when it comes to searching through complex configuration directories and large trees of source code for a...

For a system admin or a programmer, `grep` and the likes are probably the most popular tools when it comes to searching through complex configuration directories and large trees of source code for a particular text string or pattern.

If `grep` is one of your favorite tools, chances are that you will like [ack](http://beyondgrep.com/) even more. `ack` is a Perl-based command-line utility similar to `grep`, but designed to improve the search speed and capabilities of `grep`. Especially if you are a programmer, I strongly recommend replacing `grep` with `ack`. The usage of `ack` is heavily optimized for code search, so a programmer like you can perform complex search on source code trees with fewer keystrokes.

## Features of ack

To highlight some of the powerful features of `ack`:

- Search the current working directory by default.
- Search sub-directories recursively by default.
- Ignore metadata directories such as .svn, .git, CSV directories.
- Ignore binary files (e.g., pdf, image, coredumps) and backup files (e.g., foo~, *.swp).
- Print line numbers in search results, which helps find the code later.
- Can search for a specific file type (e.g., Perl, C++, Makefile) (which may have multiple suffixes).
- Highlight search results by color.
- Support Perl's advanced regular expression which is more expressive than GNU regular expression used by `grep`.

As for search speed, `ack` is generally faster than `grep`. The speed of `ack` comes from its built-in file type filters; `ack` maintains a list of recognizable file types, and skips unknown/unnecessary file types during search. It also avoids inspecting superfluous metadata directories.

## Install ack on [Linux](http://xmodulo.com/recommend/linuxguide)

While `ack` is available as a standard package on major Linux distros (e.g., `ack-grep` package on Debian-based system, and `ack` package for Redhat-based system), the version of `ack` shipped with the distros still remains 1.x, while `ack` 2.0 with more features has already become available.

So I am going to download and install `ack` from the official site.

Conveniently, `ack` is available as a single-file Perl script from the official site, which incorporates all dependent Perl modules in it. So you don't need to install any extra Perl modules to run the script.

To intall `ack` on your Linux system, go to the [official site](http://beyondgrep.com/install/) to download the latest `ack`. As of this writing, the latest version is 2.12.

$ wget <http://beyondgrep.com/ack-2.12-single-file><br>
$ sudo mv ack-2.12-single-file /usr/local/bin/ack<br>
$ sudo chmod 0755 /usr/local/bin/ack

Note that on Debian-based systems, there is a separate package, also called `ack` (Kanji code converter). So if you happen to use that package, you will have to rename `ack` to avoid naming conflicts.

## Usage Examples of ack

1. Search for "eat" as a word recursively from the current directory. Things like "feature" or "eating" will not be matched.

$ ack -w eat

1. Search for a string with special characters: '$path=.' All metacharacters (e.g., '$' or '.') need to be matched as a literal.

$ ack -Q '$path=.' /etc

1. Search for 'about' in all directories other than 'downloads' directory.

$ ack about –ignore-dir=downloads

1. Search only PHP files which contain 'protected'. Then group search results by file name, and print file name for each search group.

$ ack –php –group protected

[![](http://farm4.staticflickr.com/3683/12058907134_64cac79f7f.jpg)](http://www.flickr.com/photos/xmodulo/12058907134/)

1. Get a list of the names of Makefiles which contain 'CFLAG'. The files named *.mk, makefile, Makefile, GNUmakefile will all be considered.

$ ack –make -l CFLAG

1. Highlight a matched string while still showing the entire (growing) log file.

$ tail -f /var/log/syslog | ack –passthru 192.168.1.10

To get a list of filetypes recognized by `ack` for filtering, run the following:

$ ack –help-type

[![](http://farm8.staticflickr.com/7376/12058810103_a5bc1386ae_z.jpg)](http://www.flickr.com/photos/xmodulo/12058810103/)

### ![](http://xmodulo.com/images/rss_small.png) Subscribe to Xmodulo

Do you want to receive **Linux FAQs, detailed tutorials and tips** published at Xmodulo? Enter your email address below, and we will deliver our Linux posts straight to your email box, for free. Delivery powered by Google Feedburner.
