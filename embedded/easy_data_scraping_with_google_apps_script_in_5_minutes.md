# Easy data scraping with Google Apps Script in 5 minutes

[Original URL](http://www.kutil.org/2016/01/easy-data-scrapping-with-google-apps.html)

> I'm using Google Apps Script for a lot of things - from automate tasks to data analysis. I have discovered, that there was repetitive use-case: scrape data from web and parse exact value from...

I'm using [Google Apps Script](https://developers.google.com/apps-script/)for a lot of things - from automate tasks to data analysis. I have discovered, that there was repetitive use-case: scrape data from web and parse exact value from HTML _source code_. If you are novice in programming, you probably know, that's difficult to write and use [regular expresion](http://www.w3schools.com/jsref/jsref_obj_regexp.asp). For me too :) I have written **Google Apps Script library**, which helps you to **parse data in 5 minutes.**

Let's create a small example. [In our company](http://appsatori.eu/en/) we have created a Google Apps application **[SignatureSatori](http://signaturesatori.com/)** to create and setup email signatures for all users in domain. Like a good **[growth hacker](https://en.wikipedia.org/wiki/Growth_hacking)**I benchmark the competitors how quickly get new users. There is a Google Apps Marketplace, which estimate number of users. I need save that numbers each day.

[![](http://3.bp.blogspot.com/-BbyhoNQWZUo/VqN4wIX9_8I/AAAAAAABZCQ/a63dG75Dy_0/s400/screen.png)](http://3.bp.blogspot.com/-BbyhoNQWZUo/VqN4wIX9_8I/AAAAAAABZCQ/a63dG75Dy_0/s1600/screen.png)

1) Create a new Google Apps Script and insert a new library (Resources -> Library)

<span>
  <strong>M1lugvAXKKtUxn_vdAG9JZleS6DrsjUUV</strong>
</span>

Parser library takes three parameters - input text and pattern which bounds desired text.

Parser // name of library

```
 .data(content) // input text
 .from(fromText) // from text pattern
 .to(toText) // to text pattern
 .build(); // run parser and return value
```

2) Now open desired web-page (e.g [Chrome Webstore](https://chrome.google.com/webstore/detail/signaturesatori-central-s/fejomcfhljndadjlojamaklegghjnjfn?hl=en) in our case). Click on specify HTML element by right mouse button and select Inspect element.<br>
[![](http://1.bp.blogspot.com/-jrmG52uZCnw/VqODx-wkA5I/AAAAAAABZCg/wk3vpXvtbug/s640/inspect.png)](http://1.bp.blogspot.com/-jrmG52uZCnw/VqODx-wkA5I/AAAAAAABZCg/wk3vpXvtbug/s1600/inspect.png)

<span>3) </span>

<span> Find the right part of HTML and copy <strong>fromText</strong> and <strong>toText</strong></span>

![](https://docs.google.com/drawings/d/1QIbvDBHytElCaaIYrwSdhz8Ds5bvaviCHHzwt8MOD-o/pub?w=967&h=295)

4) Now we have all required information to complete script<br>
function getData() { var url = "<https://chrome.google.com/webstore/detail/signaturesatori-central-s/fejomcfhljndadjlojamaklegghjnjfn?hl=en>"; var fromText = '

<span class="e-f-ih" title="';
     var toText = '">';</span>

```
 var content = UrlFetchApp.fetch(url).getContentText();
 var scraped = Parser
 .data(content)
 .from(fromText)
 .to(toText)
 .build();
 Logger.log(scraped);
 return scraped;
}
```

5) The last and the easiest step is copy parsed data into Spreadsheet<br>
function SAVE_DATA() { var sheet = SpreadsheetApp.openById(spreadsheetId).getSheetByName(sheetName); // insert Spreadsheet Id and Sheet name sheet.appendRow([ new Date(), getData() ]); }

6) If you want to log during scraping (e.g. if you want to debug wrong value), call .setLog() function before final .build() function:

```
Parser
 .data(content)
 .setLog()
 .from(fromText)
 .to(toText)
 .build();
```

[![](https://upload.wikimedia.org/wikipedia/en/f/f2/Google_Apps_Script.png)Completed code of Parser library](https://script.google.com/d/1Mc8BthYthXx6CoIz90-JiSzSafVnT6U3t0z_W3hLTAX5ek4w0G_EIrNw/edit?usp=drive_web) Enjoy! Are you interested in this topic? Follow me on **[Twitter](https://twitter.com/ivankutil)**, [Google+](https://plus.google.com/115748350099126853184/posts) or subscribe **[RSS](http://www.kutil.org/feeds/posts/default?alt=rss)**
