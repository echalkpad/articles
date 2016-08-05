# Dead simple {for devs} python crawler (script) for extracting structured data from any website into CSV

On my [previous
post](http://blog.webhose.io/2015/08/12/tiny-basic-multi-threaded-web-crawler-in-python/) I
wrote about a very basic web crawler I wrote, that can randomly scour
the web and mirror/download websites. Today I want to share with you a
very simple script that can extract structured data from any
&lt;almost&gt; website.

Use the following script to extract specific information from any
website (i.e prices, ids, titles, phone numbers etc..). Populate the
“fields” parameter with the names and the patterns (regular expression)
of the data you want to extract. In this specific example, I extract the
product names, prices, ratings and images from Amazon.com.

    import sys, thread, Queue, re, urllib2, urlparse, time, csv
    ### Set the site you want to crawl & the patterns of the fields you want to extract ###
    siteToCrawl = "http://www.amazon.com/"
    fields = {}
    fields["Title"] = '<title>(.*?)</title>'
    fields["Rating"] = 'title="(\S+) out of 5 stars"'
    fields["Price"] = 'data-price="(.*?)"'
    fields["Image"] = 'src="(http://ecx.images-amazon.com/../images/I/.*?)"'
    ########################################################################
    dupcheck = set()
    q = Queue.Queue(25)
    q.put(siteToCrawl)
    csvFile = open("output.csv", "w",0)
    csvTitles = dict(fields)
    csvTitles["Link"] = ""
    writer = csv.DictWriter(csvFile, fieldnames=csvTitles)
    writer.writeheader()
    def queueURLs(html, origLink):
        for url in re.findall('''<a[^>]+href=["'](.[^"']+)["']''', html, re.I):
            try:
                if url.startswith("http") and urlparse.urlparse(url).netlock !=  urlparse.urlparse(siteToCrawl).netlock: # Make sure we keep crawling the same domain
                    continue
            except Exception:
                continue
            link = url.split("#", 1)[0] if url.startswith("http") else '{uri.scheme}://{uri.netloc}'.format(uri=urlparse.urlparse(origLink)) + url.split("#", 1)[0]
            if link in dupcheck:
                continue
            dupcheck.add(link)
            if len(dupcheck) > 99999:
                dupcheck.clear()
            q.put(link)
    def analyzePage(html,link):
        print "Analyzing %s" % link
        output = {}
        for key, value in fields.iteritems():
            m = re.search(fields[key],html, re.I | re.S)
            if m:
                output[key] = m.group(1)
        output["Link"] = link
        writer.writerow(output)
    def getHTML(link):
        try:
            request = urllib2.Request(link)
            request.add_header('User-Agent', 'Structured Data Extractor')
            html = urllib2.build_opener().open(request).read()
            analyzePage(html,link)
            queueURLs(html, link)
        except (KeyboardInterrupt, SystemExit):
            raise
        except Exception, e:
            print e
    while True:
        thread.start_new_thread( getHTML, (q.get(),))
        time.sleep(0.5)

Some notes:

-   I have set a user agent name, as some websites block crawling if no
    user agent is present
-   No external imports are required
-   You can define as many fields to extract as you’d like. The field
    name is the “key” in the “fields” parameter
-   As I use regular expressions to define where the content is, no DOM
    parsing is performed, so malformed HTML pages are none issue.
-   Each time you run the script it will overwrite the content in
    output.csv

Enjoy,

Ran

### Like this:

<span class="button"><span>Like</span></span> <span
class="loading">Loading...</span>

### *Related*


