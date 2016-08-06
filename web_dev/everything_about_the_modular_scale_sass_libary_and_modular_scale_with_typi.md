# Everything about the Modular Scale Sass Libary and Modular Scale with Typi

[Original URL](http://zellwk.com/blog/modular-scale/)

> Previously, I shared the theory about adjusting your Modular Scale scale to size your headers for different devices. I also covered how you can do it with the Modular Scale plugin for the 4th method....

Previously, I shared the theory about adjusting your Modular Scale scale to size your headers for different devices. I also covered how you can do it with the Modular Scale plugin for the 4th method.

Today, I want to share more about the Modular Scale library so you can learn to integrate it into your project easily. I'm also going to share with you how to use Modular Scale with Typi.

## The Modular Scale Sass Library

It's quite common to see developers use the [Modular Scale calculator](http://modularscale.com) to calculate the `font-size` for their text elements when Modular Scale is used. They inevitably resort to copy-and-pasting values from the calculator to their CSS files, like this:

```
 { font-size: 3.157em; }
 { font-size: 2.369em; }
```

If the `font-size` has to change at a specific breakpoint, these developers usually copy-paste another value from the calculator into their CSS, like this:

```
 { font-size: 3.157em; }
 { font-size: 2.369em; }

@media all and (min-width: 25em) {
 { font-size: 4.209em; }
 { font-size: 3.157em; }
}
```

There are two downsides to copy-pasting these numbers.

First, **they look like magic numbers**. There's no easy way for a developer joining the project to know how the hell these numbers got into the stylesheet in the first place.

Second, **it's a lot of work to copy-paste numbers**. What if you had to change the scale? Or the base? I can already sense the dread...

The [Modular Scale Sass Library](https://github.com/modularscale/modularscale-sass) is a library that [Scott Kellum](http://scottkellum.com), [Adam Stacoviak](http://adamstacoviak.com) and [Mason Wendell](http://thecodingdesigner.com) developed to help ease the two pain points I mentioned.

To use the Modular Scale library, you first have to download it via `bower`, `npm` or hitting the download button on Github.

- `bower`: `bower install modular-scale --save`
- `npm`: `npm install modular-scale --save`

Then, you `@import` modular scale into your Sass file.

```
@import 'path-to-modular-scale/stylesheets/modular-scale';
```

Modular Scale gives you a `ms()` function to calculate the `font-size` you wanted instead of copy-pasting from the web calculator

It takes in a number, the nth-step you want your font-size to be. By step, I mean the font-size is the number nth on your scale, like this:

![Modular Scale Steps](http://zellwk.com/images/2016/everything-about-ms/modular-scale-steps.png) Modular Scale Steps Here's how you use the function:

```
 {
 font-size: (3);
}

 {
 font-size: 2.369em;
}
```

For those who can't view the image above, the 3rd step (`ms(3)`) means the plugin multiplies your base `font-size` with your ratio 3 times (`1em a_comprehensive_look_at_jquery_selectors.md a_practical_approach_to_open_source_network_security_monitoring.md a_primer_on_the_react_ecosystem_part_1_of_3.md a_primer_on_the_react_ecosystem_part_2_of_3.md data.json data_ml devops docker embedded exploring_rx_operators_flatmap.md gaming ghost.md github go got_any_rces.md guri_vr_virtual_reality_for_the_rest_of_us.md how_to_modularize_html_using_template_engines_and_gulp__zell_liews_blog_about_web_design_and_development.md html javascript linux mobile png programming react_storybooks_meets_create_react_app_—_kadira_voice.md remove_photo_backgrounds_with_cloudinary.md science social tidy understanding_native_javascript_array_methods_–_ben_frain.md url_to_filename.csv web_dev write_like_you_talk.md ratio a_comprehensive_look_at_jquery_selectors.md a_practical_approach_to_open_source_network_security_monitoring.md a_primer_on_the_react_ecosystem_part_1_of_3.md a_primer_on_the_react_ecosystem_part_2_of_3.md data.json data_ml devops docker embedded exploring_rx_operators_flatmap.md gaming ghost.md github go got_any_rces.md guri_vr_virtual_reality_for_the_rest_of_us.md how_to_modularize_html_using_template_engines_and_gulp__zell_liews_blog_about_web_design_and_development.md html javascript linux mobile png programming react_storybooks_meets_create_react_app_—_kadira_voice.md remove_photo_backgrounds_with_cloudinary.md science social tidy understanding_native_javascript_array_methods_–_ben_frain.md url_to_filename.csv web_dev write_like_you_talk.md ratio a_comprehensive_look_at_jquery_selectors.md a_practical_approach_to_open_source_network_security_monitoring.md a_primer_on_the_react_ecosystem_part_1_of_3.md a_primer_on_the_react_ecosystem_part_2_of_3.md data.json data_ml devops docker embedded exploring_rx_operators_flatmap.md gaming ghost.md github go got_any_rces.md guri_vr_virtual_reality_for_the_rest_of_us.md how_to_modularize_html_using_template_engines_and_gulp__zell_liews_blog_about_web_design_and_development.md html javascript linux mobile png programming react_storybooks_meets_create_react_app_—_kadira_voice.md remove_photo_backgrounds_with_cloudinary.md science social tidy understanding_native_javascript_array_methods_–_ben_frain.md url_to_filename.csv web_dev write_like_you_talk.md ratio`)

```
$ms-1: ms(-); 
$ms: ms(): // => 1em
$ms1: ms(); 
$ms2: ms(); 
$ms3: ms(); 
```

The default ratio used in Modular Scale is 1.618\. If you want to change this ratio, you can change the `$ms-ratio` setting:

```
$ms-ratio: 1.25;

$ms1: ms(); 
$ms2: ms(); 
$ms3: ms(); 
```

If you wanted to use two ratios ([see why here](http://zellwk.com/blog/responsive-modular-scale/)), you can set the ratio to a list of two numbers and Modular Scale will automatically help you calculate the size for the nth-step. This feature is superb because it's really difficult to calculate the steps and line them up in ascending order.

```
$ms-ratio: 1.333 1.618;

$ms: ms(); 
$ms1: ms(); 
$ms2: ms(); 
$ms3: ms(); 
```

![Steps for a double-ratio scale](http://zellwk.com/images/2016/everything-about-ms/double-ratio.png) Steps for a double-ratio scale Likewise, you can also change the base `font-size` of the Modular Scale by setting the `$ms-base` property. The default `font-size` used is `1em`.

```
$ms-base: 2em;

$ms: ms(); 
$ms1: ms(); 
$ms2: ms(); 
$ms3: ms(); 
```

Of course, you can also use two bases for your modular scale if you want. All you need to do is to set `$ms-base` to a list of two numbers:

```
$ms-base: 1em 2em;

$ms: ms(); 
$ms1: ms(); 
$ms2: ms(); 
$ms3: ms(); 
```

![Steps for a double-base scale](http://zellwk.com/images/2016/everything-about-ms/double-base-scale.png) Steps for a double-base scale Finally, Modular Scale gives you a `ms-respond` mixin that allows you to the change ratio of your Scale at different breakpoints. I've went in-depth into this on a [separate article](http://zellwk.com/blog/changing-modular-scale).

Phew, so there's everything you ever need to know about Modular Scale.

Note: At the time of writing, the stable version of Modular Scale v2.x.x. I know Scott is making a v3 for Modular Scale that's totally different from what I've explained above. I'll update this article (or maybe create a new one) when I have the time to play with v3.

Next up, let's go into the second part of the article where I'll explain how to use Modular Scale with Typi.

## Using Modular Scale with Typi

Typi is a library that makes responsive typography easy. If you're totally new to Typi, I suggest checking out the [basic tutorial](http://zellwk.com/blog/typi) before continuing.

If you familiar with Typi, you'll know that you need to create font-maps to hold your typography configurations, like this:

```
$h1: (
 null: 2.369em,
 small: 3.157em
)
```

You can use Modular Scale directly with Typi by writing the `ms()` function as the first value of each key. The above would be equivalent to:

```
 null: ms(),
 small: ms()
);
```

As you can can imagine, you can vary `$ms-base` and `$ms-ratio`, and Typi will output the `font-size` calculated by the Modular Scale plugin.

```
 null: ms(), 
 small: ms() 
);
```

Things however, change slightly when you want to change the Modular Scale ratio at different breakpoints. Typi (v2.2.2 onwards) allow you to do this with the help of a new map called `$typi-ms`. It looks like this:

```
$typi-ms: (
 null: 1.2,
 small: 1.333,
 large: 1.618
);
```

As you can imagine, `$typi-ms` uses the `$breakpoints` map to determine the media query produced for the `small` and `large` keys. This map suggests that the ratio used for the Modular scale should be:

- default (without breakpoints): 1.2
- small breakpoint: 1.333
- large breakpoint: 1.618

Once the `$typi-ms` map is created, you can modify your font-maps such that the `font-size` value is an integer that represents the nth-step of the scale instead of ms($nth-step).

```
$h1-map: (
 null: , 
 small: 
 large: 
);
```

When you use the `typi` mixin as usual, Typi will automatically calculate the correct font-size based on the `$typi-ms` map. The CSS produced for the above Sass maps is:

```
 {
 font-size: 1.44rem; 
}

@media all and (min-width: 600px) {
 {
 font-size: 2.36859rem; 
 }
}

@media all and (min-width: 800px) {
 {
 font-size: 6.85353rem; 
 }
}
```

Notice how the `font-size` produced allows you to change the ratio and the steps at the same time? This wasn't possible if you used the Modular Scale plugin directly.

Typi is also smart enough to "guess" the ratio and the step to use if you didn't provide it with the information at every breakpoint. For instance, if you have the following maps, Typi will automatically create the `large` breakpoint with a ratio of 1.618, and a step of 3.

```
$typi-ms: (
 null: 1.2,
 small: 1.333,
 large: 1.618,
);

$h1: (
 null: ,
 small: 
);

 {
 font-size: 1.44rem; 
}

@media all and (min-width: 600px) {
 {
 font-size: 2.36859rem; 
 }
}

@media all and (min-width: 800px) {
 {
 font-size: 4.2358rem; 
 }
}
```

If you have the following maps, Typi knows that it should use `1.618` as the ratio when creating 3rd step at the `med` breakpoint. (It determines the order based on the order of breakpoints in the `$breakpoints` map).

```
$typi-ms: (
 null: 1.2,
 small: 1.618,
)

$h1: (
 null: ,
 large: ,
)

 {
 font-size: 1.44rem; 
}

@media all and (min-width: 600px) {
 {
 font-size: 2.61792rem; 
 }
}

@media all and (min-width: 800px) {
 {
 font-size: 4.2358rem; 
 }
}
```

Additionally, if you want to, you can use multiple bases or multiple ratios together with the `$typi-ms` map. Here's an example:

```
$typi-ms: (
 null: , 
 small: , 
 med: (em, ), 
 large: (em em, .618), 
 dont--this: (em em, .333 .618) 
);
```

And that's everything about using Modular Scale with Typi :)

## Wrapping Up

In this article, we reviewed the basics of Modular Scale plugin, and learned how to use:

- a double-base modular scale
- a double-ratio modular scale

Then, we talked about how to do the same with Typi.

Finally, we looked at how to change the Modular Scale ratio at different breakpoints using Typi as well.

## Next Steps

Modular Scale can be difficult to understand if you're not use to Math. However, it gets really easy once you get used to the idea of "steps". Try using Modular Scale (by itself) or Modular Scale (with Typi) and let me know if you have any questions in the comments below.
