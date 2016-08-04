# Introducing CSS Scroll Snap Points

[Original URL](https://css-tricks.com/introducing-css-scroll-snap-points/)

> Before this new CSS I'm about to introduce existed, locking an element into the viewport on scroll required rigging up some JavaScript. As you may know, JavaScript has a well-earned reputation to be...

Before this new CSS I'm about to introduce existed, locking an element into the viewport _on scroll_ required rigging up some JavaScript. As you may know, JavaScript has a well-earned reputation to be tricky when paired with scrolling behavior.

The new [CSS Scroll Snap Points spec](https://drafts.csswg.org/css-snappoints/) promises to help, allowing for this kind of behavior using very few lines of CSS.

As happens with very new web tech, this spec has changed over time. There is "old" and "new" properties and values. It's promising though, as [support](http://caniuse.com/#feat=css-snappoints)has shot up quickly. I'll teach you how to get the widest support in this in-between stage.

## Polyfilled Demo

The demo below has horizontal scrolling. It's responsive: each "panel" is the width and height of the viewport (thanks to `vh` and `vw` units).

It uses a [polyfill](https://github.com/ckrack/scrollsnap-polyfill), but in order to use it (and support is still low enough that I suggest you do), you have to support the "old" values, which is why I'll cover them, too.

See the Pen [Simple Responsive Scroll Snap Point Demo](http://codepen.io/sdras/pen/qbzmpw/) by Sarah Drasner ([@sdras](http://codepen.io/sdras)) on [CodePen](http://codepen.io).

- **If you're looking in Firefox:** it has the best current support, so you can mostly clearly see how the native behavior looks and feels.
- **If you're looking Chrome or Opera:** don't have any support, so any behavior you notice in those browsers can be attributed to the polyfill entirely.
- **If you're looking in Edge or IE:** it probably won't work at all. These browsers [have partial support](https://dl.dropboxusercontent.com/u/444684/openwebref/CSS/scroll-snap-points/support.html), but apparently not enough to make this work.
- **If you're looking on a mobile device:** iOS 9 supports it (tested on an iPhone 6), but I've seen the easing behavior act pretty weird. No Chrome/Android support, but the polyfill kicks in and handles it pretty well (tested on a Android Nexus 6).

Note I'm using [Autoprefixer](https://css-tricks.com/autoprefixer/) in the Pen to automatically give me all the necessary vendor prefixed properties.

Here's the code used to make the magic:

```
.scroller {

 scroll-snap-type: mandatory;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old older spec implementation */
 scroll-snap-destination: 0% 100%;
 scroll-snap-points-x: repeat(100%);
}
```

Pretty slim! Let's break down these properties one by one.

## Current CSS Scroll Snap Properties

### scroll-snap-type

```
scroll-snap-type: none | mandatory | proximity; 
```

A `mandatory` value is what you might think it would mean: that the element _must_ come to rest on a snap point even when there are no active scrolling actions taken. If content is somehow modified or updated, the page finds the snap point again.

The `proximity` value is close to `mandatory`, but less strict. If the browser changes in size or content is added, it may or may not find the snap point again, depending on how close to a snap point it is.

From what I've seen playing around with this, `mandatory` is more commonly supported in browsers at this time with more consistent behavior.

### scroll-snap-align

```
scroll-snap-align: [none | start | end | center] [none | start | end | center]; 
```

This property refers to how an element's scroll snap margin aligns with its parent scroll container. It uses two values, `x` and `y`, and if you only use one value it will be read as shorthand and repeated for both values (sort of like padding where `padding: 10px;` equals `padding: 10px 10px 10px 10px;`). This property isn't animatable.

![scroll-snap-align](https://cdn.css-tricks.com/wp-content/uploads/2016/02/scroll-snap-align-1.jpg)

### scroll-snap-padding

```
scroll-snap-padding: <length> | <percentage>; 
```

This property relates to the scroll container in the visual viewport. It works much like normal padding, with the same kind of value order. For example, `scroll-snap-padding: 75px 0 0;` would be top padding of `75px` and all others `0`. This property is animatable, so if you need to move scroll snap align, this would be a good way to do so.

## Older CSS Scroll Snap Properties

As mentioned, the spec has been changing rapidly in the past year and there are already properties that are considered outdated, though are still good to know from a legacy support standpoint.

### scroll-snap-points

```
scroll-snap-points-<x or y>: none | repeat(<length>); 
```

`scroll-snap-point` addresses the axis that is the direction of the scroll. In the first Pen we saw, this property is set on the `x` axis. Here, we have it on the `y` axis (since it's a vertical scroll) using `scroll-snap-points-y: repeat(100%);` The percentages refer to the padding box of whatever you've defined as the scroll container.

See the Pen [Simple Scroll Snap Points](http://codepen.io/sdras/pen/43c9d13b23bc34a85bb3a5e2ea985958/) by Sarah Drasner ([@sdras](http://codepen.io/sdras)) on [CodePen](http://codepen.io).

### scroll-snap-destination

This property and `scroll-snap-coordinate` are very similar as far as values go. Where `scroll-snap-destination` refers to the parent element, `scroll-snap-coordinate` refers to the _element_ itself. You might only need `scroll-snap-destination` to be specified if the snapping point is specified purely by the element rather than the container it sits in.

```
scroll-snap-destination: <position>;
```

This property allows you to specify at what point in the viewport the scroll should snap. For instance, say you want to cheat out your content by 100px so that two one panel is teased to one side of the other. The diagram below shows how it the scroll snap destination will allow you to easily adjust this parameter.

![scroll-snap-destination](https://cdn.css-tricks.com/wp-content/uploads/2016/02/ssd1.jpg) When defined as a percentage, the point is relative to the width and height of the scroll container.

### scroll-snap-coordinate

```
Scroll-snap-coordinate: none | <position>;
```

This property allows you to specify where the scroll should snap to an element. The position amount refers to the element's border box. You might not need it unless you're doing something pretty fancy. `scroll-snap-coordinate` is the only value that can apply to all elements on the page, all other scroll snap properties apply only to scroll containers.

![scroll-snap-coordinate](https://cdn.css-tricks.com/wp-content/uploads/2016/02/ssd.jpg) These last two properties, `scroll-snap-destination` and `scroll-snap-coordinate` are **animatable** properties, while `scroll-snap-type` and `scroll-snap-points` -- are not -- which makes sense.

## More Resources

## Conclusion

At the time of this post, the spec was updated about a month ago and is still moving. Support isn't ubiquitous yet, but even if you have to account for the older values that the polyfill requires, the code to make this work is small and sweet. It's incredibly fun to see what's coming up in CSS, and if you run experiments, by all means let the spec authors know what you're doing and what you find--it does affect what they work on. I'm still on the fence about using something like this in production yet. It would depend on how willing you are to iterate on that code if things change around in the future. In the meantime, though, the polyfill performs pretty well and it's exciting to see this kind of thing land in CSS.
