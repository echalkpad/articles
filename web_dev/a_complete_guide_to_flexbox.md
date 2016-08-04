# A Complete Guide to Flexbox

[Original URL](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

> Background The Flexbox Layout (Flexible Box) module (currently a W3C Last Call Working Draft) aims at providing a more efficient way to lay out, align and distribute space among items in a container,...

## [Background](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flexbox-background)

The `Flexbox Layout` (Flexible Box) module (currently a W3C Last Call Working Draft) aims at providing a more efficient way to lay out, align and distribute space among items in a container, even when their size is unknown and/or dynamic (thus the word "flex").

The main idea behind the flex layout is to give the container the ability to alter its items' width/height (and order) to best fill the available space (mostly to accommodate to all kind of display devices and screen sizes). A flex container expands items to fill available free space, or shrinks them to prevent overflow.

Most importantly, the flexbox layout is direction-agnostic as opposed to the regular layouts (block which is vertically-based and inline which is horizontally-based). While those work well for pages, they lack flexibility (no pun intended) to support large or complex applications (especially when it comes to orientation changing, resizing, stretching, shrinking, etc.).

**Note:** Flexbox layout is most appropriate to the components of an application, and small-scale layouts, while the [Grid](http://css-tricks.com/snippets/css/complete-guide-grid/) layout is intended for larger scale layouts.

## [Basics & Terminology](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flexbox-basics)

![](https://css-tricks.com/wp-content/uploads/2014/05/flex-container.svg)

## Properties for the Parent

<span>(flex container)</span>

### display

This defines a flex container; inline or block depending on the given value. It enables a flex context for all its direct children.

```
.container {
 display: flex; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old or inline-flex */
}
```

Note that CSS columns have no effect on a flex container.

### flex-direction

![](https://css-tricks.com/wp-content/uploads/2014/05/flex-direction1.svg)<br>
This establishes the main-axis, thus defining the direction flex items are placed in the flex container. Flexbox is (aside from optional wrapping) a single-direction layout concept. Think of flex items as primarily laying out either in horizontal rows or vertical columns.

```
.container {
 flex-direction: row | row-reverse | column | column-reverse;
}
```

- `row` (default): left to right in `ltr`; right to left in `rtl`
- `row-reverse`: right to left in `ltr`; left to right in `rtl`
- `column`: same as `row` but top to bottom
- `column-reverse`: same as `row-reverse` but bottom to top

### flex-wrap

![](https://css-tricks.com/wp-content/uploads/2014/05/flex-wrap.svg)<br>
By default, flex items will all try to fit onto one line. You can change that and allow the items to wrap as needed with this property. Direction also plays a role here, determining the direction new lines are stacked in.

```
.container{
 flex-wrap: nowrap | wrap | wrap-reverse;
}
```

- `nowrap` (default): single-line / left to right in `ltr`; right to left in `rtl`
- `wrap`: multi-line / left to right in `ltr`; right to left in `rtl`
- `wrap-reverse`: multi-line / right to left in `ltr`; left to right in `rtl`

### flex-flow (Applies to: parent flex container element)

This is a shorthand `flex-direction` and `flex-wrap` properties, which together define the flex container's main and cross axes. Default is `row nowrap`.

```
flex-flow: <‘flex-direction’> || <‘flex-wrap’>
```

### justify-content

![](https://css-tricks.com/wp-content/uploads/2013/04/justify-content.svg)<br>
This defines the alignment along the main axis. It helps distribute extra free space left over when either all the flex items on a line are inflexible, or are flexible but have reached their maximum size. It also exerts some control over the alignment of items when they overflow the line.

```
.container {
 justify-content: flex-start | flex-end | center | space-between | space-around;
}
```

- `flex-start` (default): items are packed toward the start line
- `flex-end`: items are packed toward to end line
- `center`: items are centered along the line
- `space-between`: items are evenly distributed in the line; first item is on the start line, last item on the end line
- `space-around`: items are evenly distributed in the line with equal space around them. Note that visually the spaces aren't equal, since all the items have equal space on both sides. The first item will have one unit of space against the container edge, but two units of space between the next item because that next item has its own spacing that applies.

### align-items

![](https://css-tricks.com/wp-content/uploads/2014/05/align-items.svg)<br>
This defines the default behaviour for how flex items are laid out along the cross axis on the current line. Think of it as the `justify-content` version for the cross-axis (perpendicular to the main-axis).

```
.container {
 align-items: flex-start | flex-end | center | baseline | stretch;
}
```

- `flex-start`: cross-start margin edge of the items is placed on the cross-start line
- `flex-end`: cross-end margin edge of the items is placed on the cross-end line
- `center`: items are centered in the cross-axis
- `baseline`: items are aligned such as their baselines align
- `stretch` (default): stretch to fill the container (still respect min-width/max-width)

### align-content

![](https://css-tricks.com/wp-content/uploads/2013/04/align-content.svg)<br>
This aligns a flex container's lines within when there is extra space in the cross-axis, similar to how `justify-content` aligns individual items within the main-axis.

**Note:** this property has no effect when there is only one line of flex items.

```
.container {
 align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

- `flex-start`: lines packed to the start of the container
- `flex-end`: lines packed to the end of the container
- `center`: lines packed to the center of the container
- `space-between`: lines evenly distributed; the first line is at the start of the container while the last one is at the end
- `space-around`: lines evenly distributed with equal space around each line
- `stretch` (default): lines stretch to take up the remaining space

![](https://css-tricks.com/wp-content/uploads/2014/05/flex-items.svg)

## Properties for the Children

<span>(flex items)</span>

### order

![](https://css-tricks.com/wp-content/uploads/2013/04/order-2.svg)<br>
By default, flex items are laid out in the source order. However, the `order` property controls the order in which they appear in the flex container.

```
.item {
 order: <integer>;
}
```

### flex-grow

![](https://css-tricks.com/wp-content/uploads/2014/05/flex-grow.svg)<br>
This defines the ability for a flex item to grow if necessary. It accepts a unitless value that serves as a proportion. It dictates what amount of the available space inside the flex container the item should take up.

If all items have `flex-grow` set to 1, the remaining space in the container will be distributed equally to all children. If one of the children a value of 2, the remaining space would take up twice as much space as the others (or it will try to, at least).

```
.item {
 flex-grow: <number>; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old default 0 */
}
```

Negative numbers are invalid.

### flex-shrink

This defines the ability for a flex item to shrink if necessary.

```
.item {
 flex-shrink: <number>; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old default 1 */
}
```

Negative numbers are invalid.

### flex-basis

This defines the default size of an element before the remaining space is distributed. It can be a length (e.g. 20%, 5rem, etc.) or a keyword. The `auto` keyword means "look at my width or height property" (which was temporarily done by the `main-size` keyword until deprecated). The `content` keyword means "size it based on the item's content" - this keyword isn't well supported yet, so it's hard to test and harder to know what its brethren `max-content`, `min-content`, and `fit-content` do.

```
.item {
 flex-basis: <length> | auto; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old default auto */
}
```

If set to `0`, the extra space around content isn't factored in. If set to `auto`, the extra space is distributed based on its `flex-grow` value. [See this graphic.](http://www.w3.org/TR/css3-flexbox/images/rel-vs-abs-flex.svg)

### flex

This is the shorthand for `flex-grow,` `flex-shrink` and `flex-basis` combined. The second and third parameters (`flex-shrink` and `flex-basis`) are optional. Default is `0 1 auto`.

```
.item {
 flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```

**It is recommended that you use this shorthand property** rather than set the individual properties. The short hand sets the other values intelligently.

### align-self

![](https://css-tricks.com/wp-content/uploads/2014/05/align-self.svg)<br>
This allows the default alignment (or the one specified by `align-items`) to be overridden for individual flex items.

Please see the `align-items` explanation to understand the available values.

```
.item {
 align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

Note that `float`, `clear` and `vertical-align` have no effect on a flex item.

### Examples

Let's start with a very very simple example, solving an almost daily problem: perfect centering. It couldn't be any simpler if you use flexbox.

```
.parent {
 display: flex;
 height: 300px; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Or whatever */
}

.child {
 width: 100px; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Or whatever */
 height: 100px; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Or whatever */
 margin: auto; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Magic! */
}
```

This relies on the fact a margin set to `auto` in a flex container absorb extra space. So setting a vertical margin of `auto` will make the item perfectly centered in both axis.

Now let's use some more properties. Consider a list of 6 items, all with a fixed dimensions in a matter of aesthetics but they could be auto-sized. We want them to be evenly and nicely distributed on the horizontal axis so that when we resize the browser, everything is fine (without media queries!).

```
.flex-container {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old We first create a flex layout context */
 display: flex;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Then we define the flow direction and if we allow the items to wrap 
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii Remember this is the same as:
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii flex-direction: row;
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii flex-wrap: wrap;
 */
 flex-flow: row wrap;

 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old Then we define how is distributed the remaining space */
 justify-content: space-around;
}
```

Done. Everything else is just some styling concern. Below is a pen featuring this example. Be sure to go to CodePen and try resizing your windows to see what happens.

See the Pen [Demo Flexbox 1](http://codepen.io/team/css-tricks/pen/EKEYob/) by CSS-Tricks ([@css-tricks](http://codepen.io/css-tricks)) on [CodePen](http://codepen.io).

Let's try something else. Imagine we have a right-aligned navigation on the very top of our website, but we want it to be centered on medium-sized screens and single-columned on small devices. Easy enough.

```
/* Large */
.navigation {
 display: flex;
 flex-flow: row wrap;
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old This aligns items to the end line on main-axis */
 justify-content: flex-end;
}

/* Medium screens */
@media all and (max-width: 800px) {
 .navigation {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old When on medium sized screens, we center it by evenly distributing empty space around items */
 justify-content: space-around;
 }
}

/* Small screens */
@media all and (max-width: 500px) {
 .navigation {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old On small screens, we are no longer using row direction but column */
 flex-direction: column;
 }
}
```

See the Pen [Demo Flexbox 2](http://codepen.io/team/css-tricks/pen/YqaKYR/) by CSS-Tricks ([@css-tricks](http://codepen.io/css-tricks)) on [CodePen](http://codepen.io).

Let's try something even better by playing with flex items flexibility! What about a mobile-first 3-columns layout with full-width header and footer. And independent from source order.

```
.wrapper {
 display: flex;
 flex-flow: row wrap;
}

/* We tell all items to be 100% width */
.header, .main, .nav, .aside, .footer {
 flex: 1 100%;
}

/* We rely on source order for mobile-first approach
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii in this case:
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1\. header
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 2\. nav
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 3\. main
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 4\. aside
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 5\. footer
 */

/* Medium screens */
@media all and (min-width: 600px) {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old We tell both sidebars to share a row */
 .aside { flex: 1 auto; }
}

/* Large screens */
@media all and (min-width: 800px) {
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old We invert order of first sidebar and main
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii And tell the main element to take twice as much width as the other two sidebars 
 */
 .main { flex: 2 0px; }

 .aside-1 { order: 1; }
 .main { order: 2; }
 .aside-2 { order: 3; }
 .footer { order: 4; }
}
```

See the Pen [Demo Flexbox 3](http://codepen.io/team/css-tricks/pen/jqzNZq/) by CSS-Tricks ([@css-tricks](http://codepen.io/css-tricks)) on [CodePen](http://codepen.io).

### [Prefixing Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flexbox-sass)

Flexbox requires some vendor prefixing to support the most browsers possible. It doesn't just include prepending properties with the vendor prefix, but there are actually entirely different property and value names. This is because the Flexbox spec has changed over time, creating an ["old", "tweener", and "new"](http://css-tricks.com/old-flexbox-and-new-flexbox/) versions.

Perhaps the best way to handle this is to write in the new (and final) syntax and run your CSS through [Autoprefixer](http://css-tricks.com/autoprefixer/), which handles the fallbacks very well.

Alternatively, here's a Sass @mixin to help with some of the prefixing, which also gives you an idea of what kind of things need to be done:

```
@mixin flexbox() {
 display: -webkit-box;
 display: -moz-box;
 display: -ms-flexbox;
 display: -webkit-flex;
 display: flex;
}

@mixin flex($values) {
 -webkit-box-flex: $values;
 -moz-box-flex: $values;
 -webkit-flex: $values;
 -ms-flex: $values;
 flex: $values;
}

@mixin order($val) {
 -webkit-box-ordinal-group: $val; 
 -moz-box-ordinal-group: $val; 
 -ms-flex-order: $val; 
 -webkit-order: $val; 
 order: $val;
}

.wrapper {
 @include flexbox();
}

.item {
 @include flex(1 200px);
 @include order(2);
}
```

### [Related Properties](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flexbox-related)

### [Other Resources](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flexbox-resources)

### [Bugs](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flexbox-bugs)

### Browser Support

Broken up by "version" of flexbox:

- (new) means the recent syntax from the specification (e.g. `display: flex;`)
- (tweener) means an odd unofficial syntax from 2011 (e.g. `display: flexbox;`)
- (old) means the old syntax from 2009 (e.g. `display: box;`)

<span>Chrome</span>     | <span>Safari</span>       | <span>Firefox</span>     | <span>Opera</span> | <span>IE</span>            | <span>Android</span>      | <span>iOS</span>
----------------------- | ------------------------- | ------------------------ | ------------------ | -------------------------- | ------------------------- | -------------------------
20- (old)<br>
21+ (new) | 3.1+ (old)<br>
6.1+ (new) | 2-21 (old)<br>
22+ (new) | 12.1+ (new)        | 10 (tweener)<br>
11+ (new) | 2.1+ (old)<br>
4.4+ (new) | 3.2+ (old)<br>
7.1+ (new)

Blackberry browser 10+ supports the new syntax.

For more informations about how to mix syntaxes in order to get the best browser support, please refer to [this article (CSS-Tricks)](https://css-tricks.com/using-flexbox/) or [this article (DevOpera)](http://dev.opera.com/articles/view/advanced-cross-browser-flexbox/#fallbacks).
