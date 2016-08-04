# Scrapy Tips from the Pros: February 2016 Edition

[Original URL](https://blog.scrapinghub.com/2016/02/24/scrapy-tips-from-the-pros-february-2016-edition/)

> Welcome to the February Edition of Scrapy Tips from the Pros. Each month we'll release a few tips and hacks that we've developed to help make your Scrapy workflow go more smoothly. This...

Welcome to the February Edition of [Scrapy Tips from the Pros](https://blog.scrapinghub.com/category/scrapy-tips-from-the-pros/). Each month we'll release a few tips and hacks that we've developed to help make your Scrapy workflow go more smoothly.

This month we'll show you how to crawl websites more effectively following sitemaps and we'll also demonstrate how to add custom settings to individual spiders in a project.

## Crawling a Website with Scrapy SitemapSpider

Web crawlers feed on URLs. The more they have, the longer they live. Finding a good source of URLs for any given website is very important as it gives the crawler a strong starting point.

[Sitemaps](https://en.wikipedia.org/wiki/Sitemaps) are an excellent source of seed URLs. Website developers use them to indicate which URLs are available for crawling in a machine-readable format. Sitemaps are also a good way to discover web pages that would be otherwise unreachable, since some pages may not be linked to from any other page outside of the sitemap.

Sitemaps are often available at `/sitemap.xml` or in a different location specified in the robots.txt file.

With [Scrapy](http://scrapy.org/) you don't need to worry about parsing XML and making requests. It includes a [SitemapSpider](http://doc.scrapy.org/en/latest/topics/spiders.html#sitemapspider) class you can inherit to handle all of this for you.

### SitemapSpider in Action

Let's say you want to crawl Apple's website to price check different products. You would want to visit as many pages as possible so that you can scrape as much data as you can. Fortunately, Apple's website provides a sitemap at [apple.com/sitemap.xml](http://apple.com/sitemap.xml), which looks like this:

```
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
 <url><loc>http://www.apple.com/</loc></url>
 <url><loc>http://www.apple.com/about/</loc></url>
 ...
 <url><loc>http://www.apple.com/about/workingwithapple.html</loc></url>
 <url><loc>http://www.apple.com/accessibility/</loc></url>
 ...
</urlset>
```

Scrapy's generic [SitemapSpider](http://doc.scrapy.org/en/latest/topics/spiders.html#sitemapspider) class implements all the logic for parsing and dispatching requests necessary to handle sitemaps. It reads and extracts URLs from the sitemap and it will dispatch a single request for each URL it finds. Here is a spider that will scrape Apple's website using the sitemap as its seed:

```
from scrapy.spiders import SitemapSpider

class AppleSpider(SitemapSpider):
 name = 'apple-spider'
 sitemap_urls = ['http://www.apple.com/sitemap.xml']

 def parse(self, response):
 yield {
 'title': response.css("title ::text").extract_first(),
 'url': response.url
 }
 # ...
```

As you can see, you only need to subclass SitemapSpider and add the sitemap's URL to the `sitemap_urls` attribute.

Now, run the spider and check the results:

```
$ scrapy runspider apple_spider.py -o items.jl --nolog

$ head -n 5 items.jl 
{"url": "http://www.apple.com/", "title": "Apple"}
{"url": "http://www.apple.com/ae/support/products/iphone.html", "title": "Support - AppleCare+ - iPhone - Apple (AE)"}
{"url": "http://www.apple.com/ae/support/products/ipad.html", "title": "Support - AppleCare+ - iPad - Apple (AE)"}
{"url": "http://www.apple.com/ae/support/products/", "title": "Support - AppleCare - Apple (AE)"}
{"url": "http://www.apple.com/ae/support/ipod/", "title": "iPod - Apple Support"}
```

Scrapy dispatches a request for each URL found by SitemapSpider in the sitemap and then it calls the `parse` method to handle each response it gets. However, some pages in a website may vary in structure and so you might want to use multiple callbacks for different types of pages.

For instance, you can define a specific callback to handle the Mac pages, another one for the iTunes pages and the default `parse` method for all the other pages:

```
from scrapy.spiders import SitemapSpider

class AppleSpider(SitemapSpider):
 name = 'apple-spider'
 sitemap_urls = ['http://www.apple.com/sitemap.xml']
 sitemap_rules = [
 ('/mac/', 'parse_mac'),
 ('/itunes/', 'parse_itunes'),
 ('', 'parse')
 ]

 def parse(self, response):
 self.log("default parsing method for {}".format(response.url))

 def parse_mac(self, response):
 self.log("parse_mac method for {}".format(response.url))

 def parse_itunes(self, response):
 self.log("parse_itunes method for {}".format(response.url))
```

To do it, you have to add a `sitemap_rules` attribute to your class, mapping URL patterns to callbacks. For instance, the URLs matching the '/mac/' pattern will have its response handled by the `parse_mac` method.

So, the next time you write a crawler, make sure to use SitemapSpider if you want to have comprehensive crawls of the website.

For more features, check [SitemapSpider's documentation](http://doc.scrapy.org/en/latest/topics/spiders.html#sitemapspider).

## Customize Settings for Individual Spiders using custom_settings

The settings for Scrapy projects are typically stored in the project's settings.py file. However, these are global settings that apply to each of the spiders in your project. If you want to set individual settings for each spider, all you need to do is add an attribute called `custom_settings` to your spider class.

This is especially useful when you need to enable or disable pipelines or middlewares for certain spiders or to specify different settings for each one. For example, some spiders in your project might require [Crawlera](http://scrapinghub.com/crawlera/) (a smart proxy service) enabled while others don't. You can achieve this by adding `CRAWLERA_ENABLED = True` in `custom_settings` in the specific spiders.

### custom_settings in Action

Take a look at a simplified version of the spiders from a book catalog project. They need `custom_settings` to define where the book covers are going to be stored in the filesystem:

```
class AlibrisSpider(scrapy.Spider):
 name = "alibris-covers"
 allowed_domains = ["alibris.com"]
 start_urls = (
 'http://www.alibris.com/search/books/subject/Fiction-Science-Fiction',
 )
 custom_settings = {'IMAGES_STORE': 'imgs/alibris/sci-fi'}

 def parse(self, response):
 for book in response.css("div#selected-works ul.primaryList li"):
 yield {
 'image_urls': [book.css("img ::attr(src)").extract_first()],
 'title': book.css("p.bookTitle > a ::text").re_first("\s*((\w+\s?)+)\s*")
 }

class GoodreadsSpider(scrapy.Spider):
 name = "goodreads-covers"
 allowed_domains = ["goodreads.com"]
 start_urls = (
 'http://www.goodreads.com/genres/science-fiction',
 )
 custom_settings = {'IMAGES_STORE': 'imgs/goodreads/sci-fi'}

 def parse(self, response):
 for book in response.css("img.bookImage"):
 yield {
 'title': book.css("::attr(alt)").extract_first(),
 'image_urls': [book.css("::attr(src)").extract_first()]
 }
```

These spiders scrape metadata from books on a range of websites. Book covers are retrieved using [`ImagesPipeline`](http://doc.scrapy.org/en/latest/topics/media-pipeline.html#using-the-images-pipeline), which can be enabled and configured in the project global settings.py file:

```
ITEM_PIPELINES = {
 'scrapy.pipelines.images.ImagesPipeline': 1
}
IMAGES_STORE = '/some/path/to/images'
```

The [`IMAGES_STORE`](http://doc.scrapy.org/en/latest/topics/media-pipeline.html#std:setting-IMAGES_STORE) setting lets you define where the downloaded images will be stored in your filesystem. So if you want to separate the images downloaded by each spider into different folders, you just need to override the global `IMAGES_STORE` setting via `custom_settings` in each spider:

```
custom_settings = {'IMAGES_STORE': '/a/different/path'}
```

Alternatively, you can pass the settings as arguments for the spider using the scrapy -s command line option, but that adds the hassle of having to pass all custom settings from the command line:

```
$ scrapy crawl alibris -s IMAGES_STORE=imgs/alibris/
```

So, if you ever need different settings for some spider in your project, include them in the `custom_settings` attribute in the spider. Heads up, this feature is available for Scrapy >= 1.0.0.

If you are interested in learning how to download images using your spiders, [check the docs](http://doc.scrapy.org/en/latest/topics/media-pipeline.html) for more information.

## Wrap Up

And that's about it for our February tips. Check back in with us in March, follow us on [Twitter](https://twitter.com/ScrapingHub), [Facebook](https://www.facebook.com/ScrapingHub/), and [Instagram](https://www.instagram.com/scrapinghub/), and subscribe to our [RSS feed](https://blog.scrapinghub.com/feed/) to catch our next [Scrapy Tips from the Pros](https://blog.scrapinghub.com/category/scrapy-tips-from-the-pros/).

### Like this:

<span class="button">
  <span>Like</span>
</span>

 

<span class="loading">Loading…</span>

[]()

### _Related_
