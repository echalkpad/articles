# PostCSS Quickstart Guide: Instant Setup Options

[Original URL](http://webdesign.tutsplus.com/tutorials/postcss-quickstart-guide-instant-setup-options--cms-24536)

> Welcome to the "PostCSS Deep Dive: QuickStart Guide". Throughout these first posts of our series, we're going to look at getting you started with PostCSS in the quickest, most...

Welcome to the "PostCSS Deep Dive: QuickStart Guide". Throughout these first posts of our series, we're going to look at getting you started with PostCSS in the quickest, most efficient ways possible.

In this tutorial we're going to start with instant setup options, so you can be working with PostCSS in mere minutes from now. At present we have two instant setup options: CodePen and Prepros.

Let's see how you can use them both to start playing with PostCSS right away.

## PostCSS via CodePen

If you're brand new to PostCSS the fastest way to start using it is via [CodePen](http://codepen.io/).

CodePen now has pre-integrated support for PostCSS, along with the following plugins and packs:

This selection of plugins gives you support for future syntax, Sass-like functionality, comment stripping, and the ability to write transform code in shorthand.

Start by going to CodePen and [creating a new pen](http://codepen.io/pen/). Then click the little gear icon at the top left of the CSS window to bring up the settings panel.

![](https://cms-assets.tutsplus.com/uploads/users/53/posts/24536/image/cssgearicon.png) From the dropdown labelled **CSS Preprocessor** select the **PostCSS** option. You can also check the radio button labelled **Autoprefixer** to have that plugin included.

![](https://cms-assets.tutsplus.com/uploads/users/53/posts/24536/image/postcssoncodepen.png) After making this selection you should now see a small black button below the dropdown labelled **Need an add-on?**. Click that button and a panel will open up displaying a list of `@` rules. Copy and paste any of these `@` rules into your CSS panel to start using the corresponding PostCSS plugin.

![](https://cms-assets.tutsplus.com/uploads/users/53/posts/24536/image/atrulescodepen.png)

### Example of CodePen Usage

Let's see an example of how we might use the available PostCSS plugins with CodePen, starting with the _cssnext_ pack.

At the top of the CSS panel, add this code to specify you want to use cssnext:

```
@use cssnext;
```

With this line in place, you will now have the ability to use all the functionality described at <http://cssnext.io/features>. We're going to use the CSS [variable](http://cssnext.io/features/#custom-properties-var) and [color](http://cssnext.io/features/#color) functions to set a color on the body background.

First, we setup a `:root` section and define a CSS variable in it. Add this to your CSS panel:

```
:root {
 --body_bg_color: black;
}
```

Now we can use that variable in our CSS by adding this code below:

```
body {
 background: var(--body_bg_color);
}
```

At this point you should have just seen the background of your pen turn black. You can also click the **View Compiled** button at the top of your CSS panel to see the code that has been generated:

![](https://cms-assets.tutsplus.com/uploads/users/53/posts/24536/image/compiled.png) "View Compiled" showing code after processing with PostCSS Now let's say we find that hard black a little to stark for a design we're working on and we want to lighten it a bit. To adjust the color we can use the _future syntax_ that allows for color modification.

On the line that declares the `--body_bg_color` variable, change the value from `black` to:

```
--body_bg_color: color(black lightness(20%));
```

This color function lightens the black color by 20%. You should now see your pen's background has changed to a dark grey.

![](https://cms-assets.tutsplus.com/uploads/users/53/posts/24536/image/darkgrey.png) Any of the supported plugins can be used on CodePen in the same way:

1. Include the `@` rule for the plugin you want to use
2. Start using the plugin in your CSS according to its instructions (you'll find the links above)

Here's the (visually unremarkable) demo we've just built:

## PostCSS via Prepros

Prepros may not have the range of plugin support that CodePen has, but it does include seamless activation of both _Autoprefixer_ and _cssnext_. Prepros can be downloaded from <https://prepros.io>.

To get started, drag and drop a project that includes a CSS file into the interface. Then click on the CSS file's name to open up a panel of settings on the right hand side. From here, you can check the boxes labeled **AutoPrefix CSS** in order to enable Autoprefixer, and **Enable Cssnext** in order to use cssnext:

![](https://cms-assets.tutsplus.com/uploads/users/53/posts/24536/image/postcssprepros.png) You'll now be able to use all the functionality of the [cssnext](http://cssnext.io/features/) plugin pack, as well as having your CSS autoprefixed.

## Let's Recap

Okay, let's quickly sum up what we've covered above:

- To get your PostCSS feet wet, try CodePen or Prepros for instant setup
- CodePen offers ten plugins/packs you can use
- Activate PostCSS in CodePen's CSS settings, then use `@` rules to enable specific plugins
- Prepros offers the Autoprefixer plugin and cssnext pack
- Activate these two in the settings for any CSS file in a Prepros project

## Up Next: Task Runner Integration

CodePen and Prepros are two great ways to get up and running with PostCSS almost instantly. However, the downside is that you don't get to decide which plugins to use.

When you're ready to tap into the full power of PostCSS you'll want to choose and configure your own selection of plugins. Probably the most accessible way to do this is through setting up custom PostCSS projects via task runners such as [Gulp](http://gulpjs.com/) or [Grunt](http://gruntjs.com/).

You'll learn how just how to go about that in the next two tutorials, "Quickstart Guide: Gulp Setup" and "Quickstart Guide: Grunt Setup".
