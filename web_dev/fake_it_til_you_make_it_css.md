# Fake it 'til you Make it CSS

[Original URL](https://kyusuf.com/post/fake-it-til-you-make-it-css)

> In the past few years, I have been spoilt with high-speed broadband that makes browsing the web a stress-free task. But realistically, not everyone has fibre optic, and a lot of people consume web...

![](https://kyusuf.com/assets/images/content/fake-it/thumb.png)

In the past few years, I have been spoilt with high-speed broadband that makes browsing the web a stress-free task. But realistically, not everyone has fibre optic, and a lot of people consume web content through 3G. Where am I going with this? Well, I think we can all agree that there is nothing worse than a white page with content slowly populating, so why don't we fake it until the content makes it?

Although there are ways to provide the most important CSS first, as [described in Critical CSS](https://www.smashingmagazine.com/2015/08/understanding-critical-css/), we shouldn't forget about lazy loaded content or dynamic content that may just take a while to come through. There are various ways we can give the impression that content is loading, and I will go through a few of them in this post.

## Progress indicators

You may be questioning why simulating content loading is better than having nothing display at all. Well, let's take a look at a progress indicating tool that we frequently see: loading bars. Loading bars are essentially useless to an application, but they are, however, a very important part of a user's experience.

To me, one of the most frustrating things is that I often see on file uploading sites, is selecting a file, and having no indication that it's actually doing anything once I hit submit. It makes me feel like the action I have just taken was ignored - and it's the same for loading content. If I've clicked on a link to open a page and see nothing for 5 seconds, I begin to question whether I am going to get anything at all.

![Facebook progress indicator](https://kyusuf.com/assets/images/content/fake-it/facebook.gif) Facebook progress indicator

Facebook have tackled the issue by giving the impression of loading by having a glistening gradient animating across a placeholder avatar, name, date and paragraph text. Which brings me onto the next point, placeholder layouts.

## Placeholder layouts

More recently, I've noticed placeholder layouts popping up before any content, notably on Gumtree and Slack. Gumtree prioritises its content by loading in people's listings before Sponsored Links, and it does so by preserving the space with a wireframe placeholder where the content will go.

![Gumtree placeholder indicator](https://kyusuf.com/assets/images/content/fake-it/gumtree.gif) Gumtree placeholder layout

Another nice implementation of placeholder layouts is in Slack. Upon loading Slack, you see a more traditional progress indicator (spinning wheel), as well as the outlined layout.

![Slack placeholder indicator](https://kyusuf.com/assets/images/content/fake-it/slack.jpg) Slack placeholder layout

## Dominant colour lazy loading

A lot of what I have spoken about so far involves predictable content; Facebook, Gumtree and Slack all know what content is going to appear, and can create an accurate representation of it. But what if the content is more versatile in its appearance, like an image?

![Pinterest dominant colour lazy loading](https://kyusuf.com/assets/images/content/fake-it/pinterest.gif) Pinterest dominant colour lazy loading (GIF from [manu.ninja](https://manu.ninja/dominant-colors-for-lazy-loading-images))

A technique that Google and Pinterest use when lazy loading images is taking the dominant colour from an image, and displaying that until the full image can be displayed. Most implementations of this involve a little bit of JavaScript - luckily for you, there is [great guide over at manu.ninja](https://manu.ninja/dominant-colors-for-lazy-loading-images).

For more predictable images, you can achieve this effect by finding the dominant colour with a tool like [Color Thief](http://lokeshdhakar.com/projects/color-thief/), where you can simply upload an image and it will do the hard work for you. Once you have the colour, wrap your `img` in a `div` and apply the colour as a `background-color` in your CSS - I've got a demo of this at the end of this post.

## Reserving space

One of my pet hates is pages jumping around because of late loading content - especially annoying when it's because of an advert. One easy way of preventing this is by reserving space for an element.

If you know that your adverts take a while to load, in a similar way to the dominant colour trick above, wrap them in a `div`, but this time with a width and height - that way, any relative or static content around the ad will not move once it has landed.

It can be hard to predict the height of an element with responsive design, however, with things like images that have an obvious aspect ratio, there is [a fantastic guide](http://xtianmiller.com/notes/maintaining-ratio-of-elements-all-the-methods/) by [Christian Miller](https://twitter.com/xtianmiller) that goes over all of the methods used to maintain aspect ratios.

## Fallback font

Despite our best efforts, loading web fonts is always a little delayed. Whilst they make their way onto a user's screen, we should display the best and closest looking font as a fallback.

![FFFFALLBACK bookmarklet](https://kyusuf.com/assets/images/content/fake-it/fallback.gif) FFFFALLBACK bookmarklet

Using the [FFFFALLBACK bookmarklet](http://ffffallback.com/), you can find the closest font and play with font/text CSS properties until you get something that looks close. This by itself isn't enough, you'd also need a way of knowing once the font has loaded so you could undo the fallback font's styles.

## Demo

I've made a small demo to show how some of these techniques can be used. Undeniably, there will be a little more work involved than what I have here, but it gives you an idea of how you can make things look while loading.
