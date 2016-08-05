# Using React.js and Application Cache for a fast, synced app

[Original URL](http://www.tabforacause.org/blog/2015/01/29/using-reactjs-and-application-cache-fast-synced-app/)

> In many apps at scale, the choice between fast app load and up-to-date data arises at some point, on some level. You can cache aggressively, but you'll probably be serving stale data at some...

_In many apps at scale, the choice between fast app load and up-to-date data arises at some point, on some level. You can cache aggressively, but you'll probably be serving stale data at some point. You can retrieve fresh data, but you'll probably be sacrificing some load time. You can shoot for both, but you'll likely require more hardware, app complexity, or both (read: money)._

_Identifying what tradeoffs you're able to make depend on your specific app and business needs. This post is about how our team at Tab for a Cause answered this question using React.js and application cache._

## Where we started

Tab for a Cause lets you to give to charity every time you open a tab in your browser, and things have been great--in fact, we just hit a milestone of $100,000 raised for charity--but we had a problem.

Our app was too slow. We knew this. When users replace their new tab page, they demand speed and consistency, and we weren't delivering: the delay in page load was the top reason people cited for leaving Tab for a Cause.

We wanted our page to be useful as well as charitable, but as we added additional functionality to the page, our problems with page load amplified, because people had a stronger need to quickly access the content our app provided.

We were making a roundtrip server call and serving a page using Django's templating system. Our server response time of ~65ms wasn't miserable when the user was on fast internet and when our services were healthy. However, if you opened tabs at my parents' house* or if our database had a hiccup, it could be frustrating beyond belief.

With regret, I should also admit that we had launched our app without a formal frontend framework, using only JQuery. Given that our app is heavily interactive, there was much spaghetti. Which I would normally love, if it weren't of the code variety.

We needed to fix this.

_\_ I love you, Mom and Dad! Time Warner Cable, [not so much](https://www.battleforthenet.com/).*

## Identifying our requirements

In deciding how to tackle this problem, we had to prioritize our needs and decide on acceptable sacrifices. Here are the requirements we came up with:

1. **The page had to load quickly.** This was non-negotiable.
2. **Our page had to be served from a non-local URL.** We raise money for charity via advertising, and online ad networks do a handful of fraud checks to make sure you're showing their ads where they should be shown. Ad networks generally don't allow protocols other than http or https, which has always ruled us out from being a local page served by a browser extension.
3. **We wanted the page to have fresh data, but it didn't have to be instantaneous.** We sync user data across devices and wanted to keep that experience intact. We also show users feedback that requires server-side data; for example, we show people stats on how many other people they've recruited; we also occasionally run charity drives, during which a bar fills up to hit a "money raised" goal. These data updates would ideally happen in real time, though we were willing to accept some degree of stale data (like updating data after the page is displayed).
4. **We wanted less spaghetti code on the frontend.** Cleaning up our code wasn't the top priority, but it sure would be a bonus.

Let's walk through our thoughts in solving this problem

## First option: aggressive server-side caching

One option we briefly considered was bulking up our server-side caching. We already relied rather heavily on Django's [low-level cache](https://docs.djangoproject.com/en/1.7/topics/cache/#the-low-level-cache-api). It served our purposes, but every time we had to write logic for cache expiry or invalidation, I thought of this slide from [an excellent presentation](http://www.slideshare.net/csky/where-django-caching-bust-at-the-seams) on the challenges of caching in Django:

![Django low-level caching is quick but dirty.](https://s3-us-west-2.amazonaws.com/tabforacause-west/media/uploads/zinnia/django-caching-slide.png "Django low-level caching is quick but dirty.")

To get measurable gains from server-side caching, we would probably be looking at a multi-tiered tangle of caching: per-user full page caching, followed by modular caches of user data, each of which we would have to intelligently invalidate when data changed. We had already suffered some caching-related bugs, so we were not looking forward to the added complexity.

On top of that, network round-trip penalty would remain, which--for a new tab page--is noticeable on fast internet and crippling on slower internet. We knew that even if we got server response time down to <1ms, the page still wouldn't be fast enough for many of our users.

Nope, this wasn't going to work.

## Second option: application cache with our current page

"Application cache? Isn't he a [douchebag](http://alistapart.com/article/application-cache-is-a-douchebag)?"

No, don't be rude!

... okay, maybe he is a little bit. Before using appcache, it's wise to fully understand its quirks and [gotchas](http://alistapart.com/article/application-cache-is-a-douchebag). Our main concern was that appcache would reduce the transparency we'd have in debugging, since we wouldn't have server logs on buggy requests (this is a problem we continue to solve for). Another minor gotcha we didn't like is that after the app code changes, changes are applied after two page views: it takes a page view to prompt the browser to fetch resources, and another page view to use those new resources. This wasn't ideal, but it was tolerable for our use case. In general, our team was relatively unfazed by appcache's limitations; most were either not applicable to our app or easily solvable.

Alright, maybe we could work with appcache. Might there be a way to implement it without a major refactoring?

Our quick-and-dirty idea was to keep our current page as-is, with Django handling view templating and returning an HTML page. On any user data change, the browser would fetch a re-rendered page from the server and stick it in appcache.

Our game plan:

- <span>We would enable appcache for our current page, so it would load without a round trip to the server.</span>

- <span>When a user made any data change that we wanted to persist, our page would make an Ajax call to save the data to the database, as we already did.</span>

- <span>We would include a user-specific version string in the appcache manifest so that the manifest was unique to each user. When the user updated any data, we would create a new version for that user such that the contents of that user’s appcache manifest change and the browser would know to fetch the updated page resources. </span>

- On the client side, we would [check for an appcache update](https://developer.mozilla.org/en-US/docs/Web/HTML/Using_the_application_cache#Testing_for_updates_to_the_cache_manifest) when the user changed any data. The browser would fetch the user's manifest, see that the contents had changed due to the new version string, and re-fetch the contents of the page.
- <span>In theory, when the user visited the page the next time, the application cache would have an up-to-date page to serve, which was rendered server-side.</span>

On the bright side, this option would require minimal engineering investment.

A teeny downside: this option didn't work.

The main problem was that the browser's resource fetching was not fast enough. If you were to modify data on the new tab page (say, make a note in one of your sticky notes), and open a new tab within a few seconds, the appcache may not have fetched the new page with your changes and would display the old page without your sticky note additions. In terms of user experience, this would look like data loss--even if it was technically data delay--and this was unacceptable to us.

The problem would be worse when multiple devices were involved. If you make a change to your new tab page while on Device A, and open a tab in Device B, you are guaranteed to get old data. You wouldn't see new data until the following page load.

No good. Sorry, quick-and-dirty option.

## Third option: frontend templating with local storage and application cache

To do this more cleanly, we wanted to use appcache in conjunction with client-side templating, storing data in local storage. This seemed like a good option because it would be fast, it would eliminate the nastier aspects of appcache's "second page load" problem, and it would clean up our frontend (refactoring... yay?). As a bonus, our new tab page would then be accessible when offline.

We chose to use [React.js](http://facebook.github.io/react/index.html) for templating for a few reasons. The primary one is that we had some experience using it in other areas of our app. We also felt it had a shallower learning curve than Angular, our other serious consideration. And, oddly enough, as we had struggled for too long to build a frontend framework on our own with JQuery, our data had morphed into something that sort-of resembled React's "state", which would make the transition to React a little easier.

We also opted to use Facebook's [Flux](http://facebook.github.io/flux/docs/overview.html#content) patterning, because we agreed that one-way data flow made our code easier to reason about. Flux's dispatcher also made data syncing easier for us, as I'll describe below.

### How it works

On a new tab open, the browser fetches our page from appcache. Our React app fetches data from Flux stores (1), which in turn are pulling data from local storage (2). The React app mounts, so the page is loaded (3, 4). Then, our page makes an Ajax call to our app server (5), sending along all of the app's data--essentially an object holding all of our Flux stores. The server receives all of the user's local data and reconciles it with the user's data in the database (described more in "Data sync" below), returning the latest data to the app (6). The app receives the latest data from the server and updates each Flux store (7, 8). On Flux store update, stores emit a change event (3), and React components update their state (4). When the user changes anything, that initiates an action (11), updating the store data (7, 8); when the stores update and emit a change, we persist the data to local storage (9) and, if the user is online, to the remote database (10).

If you've read about Flux, this will look very familiar:

![Our Flux data flow](https://s3-us-west-2.amazonaws.com/tabforacause-west/media/uploads/zinnia/reactjs-flux-data-flow.png "Our Flux data flow")

_(Halfhearted apologies for the hideous diagram.)_

This data flow means we'll quickly show you your new tab page, then within a second or so, we'll update your page with any new data from the server. This new data might consist of changes to a widget (like notes in a sticky note) that you made on another device, or it might be the latest count of "money raised" in a charity campaign that's running.

The spectacular thing about using Flux patterning is that the sync events from remote data flow through the dispatcher exactly as user actions do, making debugging beautifully simple. Since stores are the source of truth for the state of our app, we can rest easy knowing that our app will respond consistently when the store data is modified from a remote data sync or from a user input. We are also able to maintain one-way data flow throughout the app, which makes reasoning about the code much simpler.

We had found our ideal tradeoff between app speed and data freshness.

### Data sync

I mentioned that we sync your data to the server on every page load. How did we implement this?

We handle sync by timestamping when "chunks" of data were last updated, then saving the modified_at timestamp on the client side (in Flux stores) and in the remote database. For instance, if you type in one of your sticky note widgets, set that widget's modified_at timestamp to now, save your note content to local storage and, if possible, the remote database. Then, the next time you open a tab, we'll send the widget's data to the app server, where it will compare the widget's client-side timestamp to the one in the database and return the most recent data.

For simplicity, we send and receive data as Flux store objects. This allows us to painlessly update the Flux store with data sent from the app, because we know it will be up-to-date and have the same data structure as our stores.

Our current sync process is certainly imperfect: in the case of a sync conflict, we'll simply take the latest data. For us, this is an acceptable corner case; after all, we're not Evernote. If it ever becomes unacceptable, it's solvable through smarter data merging and user messaging.

### Let's go faster! (or, prerendering)

Loading our appcached page is great, but before we display anything to the user, we still have to run through our React app code and render all of the components. For a sizable app, this can take a few hundred milliseconds to more than a second.

For a fast first-load experience, React provides a handy [renderToString method](http://facebook.github.io/react/docs/top-level-api.html#react.rendertostring) that allows you to send the DOM to the browser first (make the page appear) and attach all of your listeners second (make the page reactive). It's intended for server-side prerendering. In our case, we wondered if we could use it on the client-side--and we did.

Every time we persist data to local storage, we also render our React app to a string and save the string in local storage. Then, on page load, before we do anything else, we load the rendered app from local storage and attach it to an element. In other words, the page mounts the DOM with only 3 lines of Javascript! For our app, prerendering cut down perceived load time by about 400ms.

### "Damn": the challenges and downsides

Nothing is perfect. Here are a few things that weren't so fun in this refactoring.

**Goodbye, JQuery UI**

One speed bump in converting to React was giving up on pretty much all of our JQuery UI components, such as [draggable](http://jqueryui.com/draggable/). It was mildly irritating spending time to redo things we already had working. However, it proved easy enough to build our own or lean on the growing availability of [open source React components](http://react-components.com/).

**"Why, renderToString, why?"**

Another minor implementation challenge: if you've ever used React's renderToString method, you've probably seen this error:

```
React attempted to use reuse markup in a container but the checksum was invalid.
```

When React renders its app after there's already prerendered DOM, it expects the prerenderd DOM to be identical to the about-to-be-rendered DOM. That means you can't allow things like Date.now() and Math.random() to affect your DOM. To solve this, you'll probably have some nice one-on-one time with your favorite diff editor, comparing the two DOM strings.

**Less flexible store data structure**

Our design leaves the door open for a mismatch between our app and the structure of the app data that the server returns. After we push new code to production, your first page view will contain old app code loaded from appcache. However, the synced data returned from the app server will be structured however our new release structures it.

So, if in our release we decided to rename or move a piece of data in our store, your page would break on that first tab opened; the old app code wouldn't know how to handle it. Before the next tab, your browser probably fetched the latest app code and stuck it in appcache, so the page will work just fine.

To prevent breaking your new tab, we need to maintain a reliable internal API for our store data. That's a bit of a pain.

**Speaking of code pushes...**

If we mess up and break the app, every user who sees a broken page will see one extra broken page after we've fixed it. Application cache's pesky update-on-second-reload strikes again.

## All's well that ends well

Switching to React and Flux was a joy. Our team finds ourselves loving frontend development again, and our changes have made it a hell of a lot easier for new engineers to approach the code base.

On the user experience side, our new tab is now consistently fast. For users with excellent internet, this release doesn't make too much of a difference; but for others, it is the difference between finding our app unusable and loving it.

Because Tab for a Cause raises money for charity from banner ad impressions, a faster page load increases the number of ads we show before users navigate away from the page. This release has increased our ad impressions (and consequently our money raised) by around 12%.

Of course, a fast app doesn't make a good app; it simply makes a not-immediately-terrible app. For us, it provides the fundamental base for more exciting things to come.

--–

_Was this interesting? Want to work with a fun, motivated team? [We're hiring](http://www.tabforacause.org/jobs/)! Also, if you're around San Francisco this week, I'll be at the [React.js Rejects Meetup](http://www.meetup.com/ReactJS-San-Francisco/events/219642482/) on Friday--let me know if you want to meet up._

_Thanks to Ti Zhao and Josiah Gaskin for their feedback on this post._
