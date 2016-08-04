# keen/dashboards

[Original URL](https://github.com/keen/dashboards)

> Building an analytics dashboard? Don't start from scratch. Grab one of our Bootstrap-based templates and admire your data in minutes. Begin with a layout: Add charts to each chart-stage HTML...

Building an analytics dashboard? Don't start from scratch. Grab one of our Bootstrap-based templates and admire your data in minutes.

Begin with a layout:

[![Hero Thirds Example](https://camo.githubusercontent.com/2a58c8a8796e09dbdd2bce6204f4d79373de0651/687474703a2f2f636c2e6c792f696d6167652f3376324831383055306b30512f53637265656e25323053686f74253230323031342d31302d32392532306174253230332e31322e3234253230414d2e706e67)](https://camo.githubusercontent.com/2a58c8a8796e09dbdd2bce6204f4d79373de0651/687474703a2f2f636c2e6c792f696d6167652f3376324831383055306b30512f53637265656e25323053686f74253230323031342d31302d32392532306174253230332e31322e3234253230414d2e706e67)

Add charts to each `chart-stage` HTML element:

```
<div class="col-sm-6 col-md-3">
 <div class="chart-wrapper">
 <div class="chart-title">
 Chart Title
 </div>
 <div class="chart-stage">
 <div id="grid-1-1">
 <!-- chart goes here! -->
 </div>
 </div>
 <div class="chart-notes">
 Notes about this chart (optional)
 </div>
 </div>
</div>
```

And voilà!

[![Sample Dashboard](https://camo.githubusercontent.com/a294bbeb78340c703f12a7eb1f8cebe1da1fe3dc/687474703a2f2f636c2e6c792f696d6167652f3154336130583430327230572f53637265656e25323053686f74253230323031342d31302d32392532306174253230332e33352e3034253230414d2e706e67)](https://camo.githubusercontent.com/a294bbeb78340c703f12a7eb1f8cebe1da1fe3dc/687474703a2f2f636c2e6c792f696d6167652f3154336130583430327230572f53637265656e25323053686f74253230323031342d31302d32392532306174253230332e33352e3034253230414d2e706e67)

An attractive, custom analytics dashboard that's ready to be shown to your team or your customers. No hours lost tweaking CSS or testing responsiveness on eight different mobile devices.

## [](https://github.com/keen/dashboards#the-templates)The Templates

These layout templates are composed of a minimal set of [Bootstrap v3.2](http://getbootstrap.com/) custom styles. They cover the most common use cases and layout configurations we've encountered so far.

- [Layouts](http://keen.github.io/dashboards/layouts/) for pre-built, responsive dashboard views
- [Examples](http://keen.github.io/dashboards/examples/) for specific domains, data models and popular integrations

## [](https://github.com/keen/dashboards#integrations)Integrations

These templates can work with any data source or charting library, but they're particularly streamlined to work with Keen IO's [visualization toolkit](https://github.com/keenlabs/keen-js). To see the Keen integration in action, create a [free project](http://keen.io/signup?s=gh-dashboards) and send some data to it. Then add some charts to your dashboard with just a few lines of code.

You can also use this pre-populated set of [demo data](https://github.com/keen/dashboards/tree/gh-pages/demo-data).

## [](https://github.com/keen/dashboards#usage)Usage

Ready to use one of these awesome layouts? Here's how to get started.

1. In the sidebar, is a _Download Zip_ link. Click it to download a copy of the code.

2. Check out the various [layouts](http://keen.github.io/dashboards/layouts/) and pick the one that best suits your needs. Find the template in the repository you downloaded at `folder/layouts/(name-of-template)`.

3. Start editing! In the destination folder will exist an `.html` file. Open it in your favorite text editor. There are three things you need to do to edit your dashboard:

  1. Setup: If you're a registered Keen IO user, navigate to [your keen project](http://keen.io/login?s=gh-dashboards) or if you don't have a user at first, you can simply use some demo data that we've prepared for you. You can access those by going to the repository and navigating to demo-data. There, you will see some javascript files with some code in them. We will simply paste those in the .html file.
  2. Some copypasta. When you navigate to the bottom of the .html file, you can see that there are a bunch of script tags. Just before the end of the body tag, we're going to add in the code from sample.html. Simply copy and paste the code just before you see `</body>`.
  3. Once you've done that we need to hook up the specific items within the template to the code that we've just pasted in to our file. In line 21 of sample.html, you will see a line of code: `document.getElementById('chart-01')`. That means that this _query_ will try to find inside the html file a node with an id of 'chart-01'. In these templates, you will see lines of that resemble something like:

    <div class="chart-stage"><_comment> This is where you need to put the id property in! </_comment>
     <img data-src="holder.js/100%x650/white"></div>

  Now we're going to change those lines so that it looks like this:

  ```
  <div class="chart-stage" id="chart-01"> <!-- This is where you need to put the id property in! -->
   <!-- Get rid of that img tag! -->
  </div>
  ```

You're finished! Congratulations on setting up your first chart! Repeat step three with the rest of the items in the template to complete your dashboard!

## [](https://github.com/keen/dashboards#contributing)Contributing

Contributions are 11,000,000% welcome! That's a lot!

Please file issues for any bugs you find or features you'd like to see. And if you're up for it, send in a pull request.

To develop, you'll need to first install dependencies using [Bower](http://bower.io/):

```
$ npm install -g bower
$ bower install
```

Note: Updates to the site backed by the **gh-pages** branch go live immediately once pull requests are reviewed and approved.

Note #2: This project is moving fast, so make sure and stay up to date. Here's what we suggest. Fork this repo, clone the fork, and add the original repo as a remote called `upstream`:

```
$ git clone https://github.com/username/dashboards.git
$ cd dashboards
$ git remote add upstream https://github.com/keen/dashboards.git
```

Pull from `upstream` frequently to keep your local copy up to date:

```
$ git pull upstream gh-pages
```

## [](https://github.com/keen/dashboards#support)Support

Need a hand with something? Send us an email to [contact@keen.io](mailto:contact@keen.io) and we'll get back to you right away! For technical questions, use the [`keen-io`](https://stackoverflow.com/questions/tagged/keen-io) tag on Stack Overflow.
