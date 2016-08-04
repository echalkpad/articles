# Understanding the box model by building a navigation bar

[Original URL](http://www.chenhuijing.com/blog/that-navigation-bar-design/)

> Working in an agency means that most of the work I do is project-based. This means building a new design every couple of months (or less, if you know what I mean). The interesting part is, after a...

Working in an agency means that most of the work I do is project-based. This means building a new design every couple of months (or less, if you know what I mean). The interesting part is, after a while, I discover each designer's style and preferences. An example would be the navigation UI, I had three consecutive projects where the navigation UI had a similar style. This particular element stood out to me, not only because I'd seen it twice before, but because I found that it touched almost every aspect of the box model.

That's a phrase most of us hear pretty often. I'll be honest, the first time I saw this design, I thought to myself, it looks simple enough. No, brain. Wrong conclusion 

<span class="kaomoji">ಠ_ಠ</span>

.

Minimalism is in, I suppose Let's break the requirements down piece by piece.

1. Each link should be delimited by a vertical line, with equal space on both the right and left. The first and last links should NOT have these delimiters on their left and right, respectively.
2. The delimiter should be the same height as the text.
3. The text on the link should have an underline applied upon hover, but there should be some breathing space between the text and the underline.
4. Hover effects apply to individual links only.
5. The entire link box should be clickable, and the hover effect should kick in once the cursor enters the link box.
6. The active state of each link should be the same as when the link is hovered.

I'm going to step through my actual thought process, which is essentially my box-model journey. If you just want the end result, [skip right to it](http://www.chenhuijing.com/blog/that-navigation-bar-design/#step-2-style-the-navigation-to-match-design-attempt-3). But in the words of [Arthur Ashe](http://www.arthurashe.org/life-story.html) (what can I say, I'm an athlete too 

<span class="kaomoji">¯\_(ツ)_/¯</span>

):

> Success is a journey not a destination. The doing is usually more important than the outcome. - Arthur Ashe

## Step 1: Basic bare-bones mark-up and styles

Let's start off with the most basic mark-up, an unordered list in a `nav` element, with the text of each link wrapped in an `a` tag.

```
<nav>
 <ul>
 <li><a href="javascript:void(0)">Link 1</a></li>
 <li><a href="javascript:void(0)">Link 2</a></li>
 <li><a href="javascript:void(0)">Link 3</a></li>
 <li><a href="javascript:void(0)">Link 4</a></li>
 </ul>
</nav>
```

Then, apply some simple CSS to get the list in a horizontal configuration.

```
li {
 display: inline-block;
}
```

[Link 1]()[Link 2]()[Link 3]()[Link 4]() Bare-bones navigation bar This is the step with oh-so-many variations. There are loads of ways to get the navigation to look like the design, given the number of elements available to work with. I started off just putting in fonts, colours, and some padding.

```
nav {
 font-family: 'Slim Jim', 'Impact', 'Arial Black', sans-serif;
 background: black;
}
li {
 display: inline-block;
 padding: 1rem; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old not such a good idea */
 border-left: 1px solid white; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old neither is this */
 &:first-child {
 border-left: none;
 }
}
a {
 color: white;
 text-decoration: none;
 &:hover {
 border-bottom: 2px solid white;
 }
}
```

That code will give you something like this, which somehow, doesn't fit the bill. Also, the underline only appears when you hover over the text, not the entire link box. 

<span class="kaomoji">(╯°□°）╯︵ ┻━┻</span>

Something doesn't look quite right here.

## Interlude: Learn the box model, padawan

If you think about it, web pages are just content in boxes, but laid out in a myriad of patterns and designs. Browsers render these boxes based on the properties we give them. Each box is described to the browser based on the **box model**, which determines how much a space a box takes up on the page.

The model is made up of four boxes, from inside to outside:

- Content
- Padding
- Border
- Margin

MarginBorderPaddingContentMargin edgePadding edgeBorder edgeContent edge The box model, visualised

### Content

This area encompasses the actual content of the element, the text, background, image(s) and so on. It stops at the content edge. Referring to the above diagram, it's everything in the blue box.

### Padding

Padding extends to the padding edge. Any content in the content area (like background or an image) will extend into the padding. The amount padding between the content area and the padding edge is controlled via the `padding` property. Refer to the green area in the diagram above.

### Border

Border extends to the border edge. By default, backgrounds extend underneath the border area. The thickness of the border is controlled by the `border` property. Refer to the yellow area in the diagram above.

### Margin

Margin extends to the margin edge. However, it is an "empty" area that separates the elements its neighbouring elements. The space the margin takes up is controlled via the `margin` property. There is one particular behaviour of margins that we need to take note of. It's called **margin collapsing**.

This happens when margins of adjacent block elements combine into a single margin, which takes the larger value of the two. This phenomena only occurs vertically, which means for `margin-top` and `margin-bottom`. Horizontal margins ( `margin-left` and `margin-right` ) never collapse. Here are some common scenarios where margins collapse.

**Adjacent block elements**

We start off with 2 block elements stacked on top on each other.

```
<div class="block1"></div>
<div class="block2"></div>

.block1 {
 width: 300px;
 height: 100px;
 background: lawngreen;
}
.block2 {
 width: 300px;
 height: 100px;
 background: orange;
}
```

Given they have no margins, they will look something like this:

.block1.block2 2 block elements on top of each other. Now, let's add some margin to the bottom and top of the first and second element respectively.

```
.block1 {
 width: 300px;
 height: 100px;
 background: lawngreen;
 margin-bottom: 2rem;
}
.block2 {
 width: 300px;
 height: 100px;
 background: orange;
 margin-top: 1rem;
}
```

If the first element has a margin-bottom of 2 rem, and the second element has a margin-top of 1 rem, the resultant margin between them will be 2 rem. You can think of it as the smaller margin (that of the second element) having _collapsed_.

.block1.block22 rem Margin collapses to 2 rem In the event of negative margins, the sum of the positive and negative margins will be the resultant value. E.g. if the top element has a margin-bottom of 2 rem and the bottom element has a margin-top of -1 rem, the resultant margin will be 1 rem.

**Parent/child elements**

Margin collapse also occurs when both the parent and child elements have vertical margins that touch. Take a look at this code:

```
.block2 {
 width: 300px;
 height: 100px;
 background: orange;
 margin-top: 2rem;
}
.block2-1 {
 width: 268px;
 height: 50px;
 background: yellow;
 margin-top: 1rem;
}
```

You'd think that the margin between `.block1` and `.block2` will be 2 rem and there will be a margin of 1 rem between the edge of `.block2` and the top of `.block2-1`, right? Nope. The margins collapse into one combined margin, which again, takes the larger of the 2 values.

.block1.block22 rem.block2-1 Again, the margin of .block2-1 is collapsed If there was no margin on `.block2`, and a `margin-top` of 1 rem on the child element `.block2-1`, then the space between `.block1` and `.block2` is 1 rem.

```
.block2 {
 width: 300px;
 height: 100px;
 background: orange;
 margin-top: 0;
}
.block2-1 {
 width: 268px;
 height: 50px;
 background: yellow;
 margin-top: 1rem;
}
```

.block1.block21 rem.block2-1 Margin of inner div .block2-1 becomes the margin of .block2 If you don't want margins to collapse between parent and child elements, you'll have to prevent their margins to coming in contact with each other. We can do that by adding padding or a border. Assume box-sizing has been set to `border-box`.

```
.block2 {
 width: 300px;
 height: 100px;
 background: orange;
 margin-top: 0;
 padding: 1px;
}
.block2-1 {
 width: 268px;
 height: 50px;
 background: yellow;
 margin-top: 1rem;
}
```

.block1.block21 rem + 1 px.block2-1 And the collapse magically disappears Also, elements which are floated, and elements which are positioned absolutely never suffer from collapsed margins.

### The box-sizing property

We also need to understand a CSS property called **box-sizing**. This property alters the default box model, which affects the way your browser calculates the width and height of your elements. There are two values this property accepts: `content-box` and `border-box`. By default, this property is set to `content-box`.

For example, we have some code for a simple `div`:

```
div {
 margin: 10px;
 border: 1px solid green;
 padding: 10px;
 height: 100px;
 width: 100px;
}
```

This `div`, by default (which means `box-sizing: content-box;`), will have its height and width measured by **content** only. Now, because it also has a border and some padding, the browser will recognise the dimensons of this `div` as _122px_ by _122px_.

1010110MarginBorderPadding110100 x 1001010101110100px Content-box takes the width of the **content only**. If we set a `box-sizing: border-box;` property to it, the browser sees things a little differently. The browser will consider the width and height of the content, padding and border, to be the dimensions of this `div`. So the width of the content box becomes _78px_ while the height of the content box becomes _78px_.

1010110MarginBorderPadding11078 x 781010101110100px Border-box **includes** padding and border widths Alright, now that we've got all that covered, it's back to our scheduled programming.

We want the clickable area take up the entire link box. We also need to have a delimiter that's the same height at the link text. And we want the underline to only cover the length of the text itself. Unfortunately, there's no straightforward way to do this.

###Entire link box should be a clickable area

Let's try this styling thing one more time. To tackle the clickable area problem, we apply the padding to the `a` tag instead of the `li` like so:

```
li {
 display: inline-block;
}
a {
 color: white;
 text-decoration: none;
 display: block;
 padding: 1rem;
}
```

Note that `a` elements by default are `display: inline` and if you want the vertical padding to take, you'll have to set it to `display: block` (you can do `display:inline-block` too). So now the entire link box is clickable. But our underline ends up on the edge of the link box too.

### Underline the link text when clickable area is hovered

To get around that problem, we'll have to wrap the link text with a `span`. I couldn't just use the `text-decoration: underline` property because it "doesn't look good". Instead, a white bottom border was used instead.

```
<nav>
 <ul>
 <li><a href="javascript:void(0)"><span>Link 1</span></a></li>
 <li><a href="javascript:void(0)"><span>Link 2</span></a></li>
 <li><a href="javascript:void(0)"><span>Link 3</span></a></li>
 <li><a href="javascript:void(0)"><span>Link 4</span></a></li>
 </ul>
</nav>

a {
 color: white;
 text-decoration: none;
 display: block;
 padding: 1rem;
 &:hover span {
 border-bottom: 2px solid white;
 }
}
```

There are advantages to this, in that there are more ways you can control the look of the underline as opposed to using text decoration. By using the `:hover` pseudo-class, we can trigger the `span` to display its white bottom border the moment the cursor enters the link box.

Okay, so far so good

### Delimiter should be same height as text with equal horizontal spacing

If you followed along during that long interlude about the box model, you'd realise that this is quite tricky. Because we want the delimiter to be the same height as the text, we can't just apply a right border to the link box and call it a day (_\_glares at designer** ). How about applying the border to the `span` instead? Keep in mind that the box model goes content, then padding, then border, then margin. Padding will extend the content, but margin will not.

Applying a white 1 px right border to the `span` displays a delimiter that kisses the text (it is the right height, though). Okay, let's put in some right padding on the `span` as well. But padding also means that the underline will extend beyond the length of the text, not to mention it adds to the existing padding from the `a` already 

<span class="kaomoji">(╯°□°）╯︵ ┻━┻</span>

.

Well, that didn't come out right Third time's the charm (otherwise I might throw my keyboard out the window...just kidding). But we've learnt a lot from all those previous missteps. We now know we probably shouldn't use the `span` to handle the delimiter. There is one more thing we can use though, the `:after` pseudo-element. Pseudo-elements are mainly used for styling purposes, which suits the situation well.

```
a {
 color: white;
 text-decoration: none;
 display: block;
 padding: 1rem 0rem 1rem 1rem;
 &:after {
 content: '';
 display: inline-block;
 vertical-align: middle;
 width: 1px;
 height: 1rem;
 border-right: 1px solid white;
 margin-left: 1rem;
 }
 &:hover span {
 border-bottom: 2px solid white;
 }
}
```

Pseudo-elements are inline by default, so we set `display: inline-block` to allow us to style it accordingly. Because the pseudo-elements are inserted before or after the _content_ of the element, a margin on the pseudo-element becomes an extension of the content of the element itself. So this additional space is still a clickable area.

The final touch is to remove the right padding on the `a` to compensate for the left margin on the pseudo-element. And we're finally done!

This took a while to get too =_= If the amount of code on the pseudo-element gets to you, you can always just use the pipe character instead. The end result looks similar anyway.

```
a {
 color: white;
 text-decoration: none;
 display: block;
 padding: 1rem 0rem 1rem 1rem;
 &:after {
 content: '|';
 margin-left: 1rem;
 }
}
```

## Wrapping up

In spite of all the time I spent building this particular navigation bar, I do appreciate the experience. I probably wouldn't have understood the box model completely if not for it. In the event my designer ever reads this article, please know that in spite of all the times I show my "angry developer face" (something like this 

<span class="kaomoji">ಠ_ಠ</span>

), I still love you all the same ![:heart:](https://assets.github.com/images/icons/emoji/unicode/2764.png ":heart:").

## Further reading
