# Parsing an RSS News Feed with a Bash Script

[Original URL](http://www.linuxjournal.com/content/parsing-rss-news-feed-bash-script)

> I am involved in several free software projects, including one or two where I maintain the website. For one of those projects, we currently are updating the website. Ours is probably similar to other...

![](http://www.linuxjournal.com/files/linuxjournal.com/ufiles/imagecache/large-550px-centered/u1000009/bash-148836_960_720_0.png) I am involved in several free software projects, including one or two where I maintain the website. For one of those projects, we currently are updating the website. Ours is probably similar to other free software projects. We use a hosting service for several key services, including news, but we run our website on a web server that we own. In our case, we run most of our project on SourceForge and run the website on a third-party service, so the news and website are on different systems.

Not surprisingly, our project uses an RSS feed to pull news items from SourceForge to display on the project website. Previously, our website used a PHP script to cache a copy of the RSS file, then parse it every time a visitor arrived at our front page. That worked well, but was somewhat fragile if the remote news server was slow or temporarily unreachable.

As we work on the website update, however, I want to improve how we display news items. I want to automate the news service to pull down a copy of the RSS feed, parse it and save the news items to a local file that the website can just include. That reduces complexity on the website, and it means the front page will load faster every time.

Initially, I adapted our previous PHP website code into a PHP script that I could run from the command line. That did the job, but I thought there had to be a better way. I wondered if I could parse the RSS feed from a Bash script instead--and, it turns out that you can! It's easy!

## A Brief RSS Primer

You may know that RSS is just a form of XML, which itself is a simple markup language. XML elements are values set off by tags. A tag is any markup that begins with the less-than symbol (<) and ends with the greater-than symbol (>). Tags are one of three types:

1. Start tags, to mark the start of a value (such as `<item>`).

2. End tags, to mark the end of a value (`</item>`).

3. "Empty element" tags, which might contain values as attributes (`<feedburner:info uri="linuxjournalcom" />`).

In RSS feeds, most values are set off by start and end tags, like this link to _Linux Journal_:

```go
<link>http://www.linuxjournal.com/</link>
```

RSS feeds have a well defined format. After some header information to describe the news website or "channel", the RSS feed displays separate news items inside `<item>...</item>` elements. Each news item contains a title, link, publication date, full description and other fields. A sample RSS file might look like this:

```go
<xml version="1.0" encoding="UTF-8" ?>
<rss xmlns:dc="http://purl.org/dc/elements/1.1/" version="2.0">
 <channel>
 <title>RSS title</title>
 <link>http://www.example.com/</link>
 <description>RSS description</description>
 <language>en</language>
 <item>
 <title>News item title</title>
 <link>http://www.example.com/link/to/news/item/</link>
 <guid isPermaLink="true">identifier-5f4b02697d2006f72648ebd0d9c6ce96</guid>
 <description>Full news item text.</description>
 <pubDate>Fri, 01 Jul 2016 17:41:07 +0000</pubDate>
 </item>
 </channel>
</rss>
```

Although in most news feeds, the RSS data is transmitted without the indents or newlines. For example, the above sample RSS would more likely look like this:

```go
<xml version="1.0" encoding="UTF-8" ?><rss xmlns:dc="http://purl.org/dc/elements/1.1/" version="2.0"><channel><title>RSS title</title><link>http://www.example.com/</link><description>RSS
description</description><language>en</language><item><title>News item
title</title><link>http://www.example.com/link/to/news/item/</link><guid
isPermaLink="true">identifier-5f4b02697d2006f72648ebd0d9c6ce96</guid><description>Full
news item text.</description><pubDate>Fri, 01 Jul 2016 17:41:07
+0000</pubDate></item></channel></rss>
```

When pulling news items from an RSS feed, I'm concerned only with a few elements:

- The title of the news item (`<title>`).

- The link to the full news item (`<link>`).

- The date the news item was published (`<pubDate>`).

- The full text of the news item (`<description>`).

And conveniently, all of those values are marked by start and end tags. That's the key assumption to parsing an RSS feed with a Bash script. For an RSS parser, I just want to grab values surrounded by start and end tags.

## The "Read" Statement

You may not think Bash can parse data files, but it can with some clever thinking. Bash, like other UNIX shells before it, can read lines one at a time from a file via the built-in `read` statement.

By default, the `read` statement reads a line of data and splits it into fields. Usually, `read` splits fields using space and tab, with newlines ending each lines, but you can change this behavior by setting the Internal Field Separator (IFS) value and the end-of-line delimiter.

To parse an RSS feed using `read`, set the IFS to a greater-than symbol (>) and the delimiter to a less-than symbol (<). Each time Bash reads a line, it reads up to the next < (the start of a tag) then splits that data at each > (the end of a tag). This sample code reads the input and splits the data into the `TAG` and `VALUE` variables:

```go
local IFS='>'
read -d '<' TAG VALUE
```

Let's explore how this works. Consider this simple input:

```go
<title>RSS title</title>
```

The first time `read` parses this line, it stops at the first < symbol. Since < is the first character of the input, that means Bash finds an empty string. The split `TAG` and `VALUE` strings are also empty, which may seem odd, but that's okay, because an RSS parser isn't paying attention to empty tags anyway.

The next time Bash reads the input, it gets `title>RSS title` and stops at the next < symbol. Then `read` splits the line at the > symbol, which leaves `TAG` with `title` and `VALUE` with `RSS title`.

The third time `read` parses the input, it reads the remaining text and stops at the end of the file. Bash splits the string `/title>` into `TAG` containing `/title` and an empty `VALUE`.

## A Simple Parser

With this `read` magic, it's easy to parse an RSS file with Bash. Let's start with a simple RSS parser that displays tags between `< >` and values between `{ }`, so you can see where tags and values begin and end. First, you'll need a Bash function called `xmlgetnext` to parse the data using `read`, since you'll be doing this again and again in the script:

```go
xmlgetnext () {
 local IFS='>'
 read -d '<' TAG VALUE
}
```

Note that you define IFS locally, while leaving `TAG` and `VALUE` as global variables. Ordinarily, variables and their values in a Bash function are shared between the function and its caller, so you need to declare only IFS as local. You want the calling script to see the values of `TAG` and `VALUE`.

Then, you loop using `while` as you parse the RSS input file and print the resulting `TAG` and `VALUE`. The Bash script is very brief:

```go
#!/bin/sh
xmlgetnext () {
 local IFS='>'
 read -d '<' TAG VALUE
}

cat $1 | while xmlgetnext ; do
 echo "<$TAG>{$VALUE}"
done
```

Now, let's use the sample RSS file from earlier in the article. From that input, the simple parser should print this:

```go
<>{}
<xml version="1.0" encoding="UTF-8" ?>{}
<rss xmlns:dc="http://purl.org/dc/elements/1.1/" version="2.0">{}
<channel>{}
<title>{RSS title}
</title>{}
<link>{http://www.example.com/}
</link>{}
<description>{RSS description}
</description>{}
<language>{en}
</language>{}
<item>{}
<title>{News item title}
</title>{}
<link>{http://www.example.com/link/to/news/item/}
</link>{}
<guid isPermaLink="true">{identifier-5f4b02697d2006f72648ebd0d9c6ce96}
</guid>{}
<description>{Full news item text.}
</description>{}
<pubDate>{Fri, 01 Jul 2016 17:41:07 +0000}
</pubDate>{}
</item>{}
</channel>{}
```

## Parsing an RSS News Feed

Remember that the RSS feed displays separate news items inside `<item>...</item>` elements. Also, each news item contains a title, link, publication date and full description as `<title>...</title>, <link>...</link>, <pubDate>...</pubDate>` and `<description>...</description>`, respectively. To parse this via a Bash script, you look for each `<title>`, `<link>`, `<pubDate>` and `<description>` tag, then write the associated html code when you find the ending `</item>` tag.

Most websites display news items using the article's title as the header, such as an `<h3>` header, and the full article text in the article body. If the feed does not already surround the article text with `<p>` or `<div>` markup, you will need to provide that. You also may prefer to include the article's publication date and link back to the original article for more details. With these assumptions and the `xmlgetnext` function, it's easy to parse an RSS news feed in Bash:

```go
#!/bin/sh
xmlgetnext () {
 local IFS='>'
 read -d '<' TAG VALUE
}

cat $1 | while xmlgetnext ; do
 case $TAG in
 'item')
 title=''
 link=''
 pubDate=''
 description=''
 ;;
 'title')
 title="$VALUE"
 ;;
 'link')
 link="$VALUE"
 ;;
 'pubDate')
 pubDate="$VALUE"
 ;;
 'description')
 description="$VALUE"
 ;;
 '/item')
 cat<<EOF
<article>
<h3><a href="$link">$title</a></h3>
<p>$description
<span class="post-date">posted on $pubDate</span></p>
</article>
EOF
 ;;
 esac
done
```

The script zeroes out the title, link, pubDate and description variables when the parser reaches the first `<item>` tag and creates the HTML code when the parser reaches the next `</item>` tag. In between, the script stores the title, link, pubDate and description values it finds in the RSS feed's article. Running this script against the sample RSS file, you should see this output:

```go
<article>
<h3><a href="http://www.example.com/link/to/news/item/">News item title</a></h3>
<p>Full news item text.
<span class="post-date">posted on Fri, 01 Jul 2016 17:41:07 +0000</span></p>
</article>
```

You can make a few improvements based on this script. For example, the publication date isn't very nice to read. Let's display this in a more human-friendly format and enclose the date in a `<time>...</time>` HTML element so screen readers can better understand it. For that, you'll need to convert the publication date into an ISO-8601 timestamp and create a "display" date in "MM/DD/YY HH:MMam" format:

```go
#!/bin/sh
xmlgetnext () {
 local IFS='>'
 read -d '<' TAG VALUE
}

cat $1 | while xmlgetnext ; do
 case $TAG in
 'item')
 title=''
 link=''
 pubDate=''
 description=''
 ;;
 'title')
 title="$VALUE"
 ;;
 'link')
 link="$VALUE"
 ;;
 'pubDate')
 # convert pubDate format for <time datetime="">
 datetime=$( date --date "$VALUE" --iso-8601=minutes )
 pubDate=$( date --date "$VALUE" '+%D %H:%M%P' )
 ;;
 'description')
 description="$VALUE"
 ;;
 '/item')
 cat<<EOF
<article>
<h3><a href="$link">$title</a></h3>
<p>$description
<span class="post-date">posted on <time
datetime="$datetime">$pubDate</time></span></p>
</article>
EOF
 ;;
 esac
done
```

This script produces HTML that is both easier for a human to understand and for a screen reader to interpret:

```go
<article>
<h3><a href="http://www.example.com/link/to/news/item/">News item title</a></h3>
<p>Full news item text.
<span class="post-date">posted on <time datetime="2016-07-01T12:41-05:00">07/01/16
12:41pm</time></span></p>
</article>
```

If the full article text contains HTML tags, RSS will convert the HTML < and > to < and >. An RSS parser will need to undo those changes. You can do this with an inline edit via `sed`, the stream editor:

```go
#!/bin/sh
xmlgetnext () {
 local IFS='>'
 read -d '<' TAG VALUE
}

cat $1 | while xmlgetnext ; do
 case $TAG in
 'item')
 title=''
 link=''
 pubDate=''
 description=''
 ;;
 'title')
 title="$VALUE"
 ;;
 'link')
 link="$VALUE"
 ;;
 'pubDate')
 # convert pubDate format for <time datetime="">
 datetime=$( date --date "$VALUE" --iso-8601=minutes )
 pubDate=$( date --date "$VALUE" '+%D %H:%M%P' )
 ;;
 'description')
 # convert '<' and '>' to '<' and '>'
 description=$( echo "$VALUE" | sed -e 's/</</g' -e 's/>/>/g' )
 ;;
 '/item')
 cat<<EOF
<article>
<h3><a href="$link">$title</a></h3>
<p>$description
<span class="post-date">posted on <time
datetime="$datetime">$pubDate</time></span></p>
</article>
EOF
 ;;
 esac
done
```

And with that, I have everything I need to update my project's website so it can be more efficient:

```go
wget --quiet -O $rsslocalfile $rssfeedurl
./parse.sh $rsslocalfile > $newslocalfile.html
```

Now I can automate this hourly job to retrieve a copy of the RSS feed, parse it, and save the news items to a local file that the website can incorporate. That reduces complexity on the website, with only a little extra work by parsing the RSS news feed with a Bash script.
