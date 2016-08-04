# A CSS Multi-column Layout Tutorial for Beginners

[Original URL](http://www.sitepoint.com/css-multi-column-layout-tutorial-for-beginners/)

> Reading very long lines of text can be problematic for some people. They will have to focus more on not missing a line instead of the text itself. This problem is easily solved by using multiple...

Reading very long lines of text can be problematic for some people. They will have to focus more on not missing a line instead of the text itself. This problem is easily solved by using multiple columns to lay out the content. Multiple columns are ubiquitous in print media. The [CSS Multi-column Layout Module](https://drafts.csswg.org/css-multicol/) features enable us to recreate the same kind of multi-column effect on websites.

One thing that makes it hard to use multiple columns when designing web pages is the inability to control the size of documents. In this tutorial, I will teach you how to create responsive multi-column layouts that look good on a variety of screen sizes. We will begin with the basics and then move on to more complex concepts.

## Browser Support

Browser support for multi-column layout is great if you are willing to use prefixes. This feature is supported by 95.32% browsers worldwide based on [stats from Can I use](http://caniuse.com/#feat=multicolumn). A few browsers like IE10+, Edge, and Opera Mini fully support multi-column layouts. Others like Firefox and Chrome need prefixes.

There is [an older polyfill](https://github.com/BetleyWhitehorne/CSS3MultiColumn) available that you try if you require support for older browsers (usually this means IE9 and below). Of course, if a browser does not support multi-column features, the layout degrades gracefully to a single column layout. So a polyfill in this case might not be the best option.

The CSS multi-column layout module has a number of different properties. In the following sections I will go over all of them one by one.

## Column Count and Column Width

The `column-count` property specifies the number of columns you want to set for an element. You can set it to `auto` or a positive number. When set to `auto`, the number of columns will be determined by the `column-width` property. If set to a positive number, all the columns are set to an equal width.

The `column-width` property specifies the width of individual columns of an element. This is not to be followed strictly. For instance, columns can be narrower if there is not enough space available. This property too can be set to both an `auto` value or a positive number. If set to auto, the width will be determined by the `column-count` property. Available space will be divided among all columns equally.

Alternatively, these two values can be set simultaneously using the shorthand `columns` property. The syntax for `columns` property would be:

```
.example {
 columns: <'column-width'> || <'column-count'>
}
```

A few examples of this property in use are shown below with the interpretation in the comment beside each example:

```
.example {
 columns: 10em; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old column-width: 10em / column-count: auto */
 columns: 4; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old column-width: auto / column-count: 4 */
 columns: 4 auto; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old column-width: auto / column-count: 4 */
 columns: auto 10em; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old column-count: auto / column-width: 4 */
 columns: auto; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old column-count: auto / column-width: auto */
 columns: auto auto; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old column-count: auto / column-width: auto */
}
```

As you can see, the first `columns` definition is a shorthand for what you see in the fourth, and second one is shorthand for the third. Basically, if the integer does not have any unit assigned it is parsed as `column-count`.

[**Here is a CodePen demo to demonstrate the features discussed so far**](http://codepen.io/SitePoint/pen/WwvMQK)

If you resize the window, you will notice a few things:

- The `column-count` property always keeps the number of columns equal to the value you specify. The only thing that changes is the width of the columns.
- The `column-width` property automatically changes the number of columns based on available space. The number of columns is adjusted in such a way that column width is greater than the specified value. It may also adjust the width of all columns to a smaller value if there is not enough space available.
- The `columns` property uses the `column-count` value as the limit for maximum columns allowed. It keeps adjusting the width in such a way that `column-count` never exceeds the count limit and `column-width` is also in close proximity to the specified width.

## Column Gap and Column Rule

The `column-gap` property allows us to specify the amount of space between columns. You can set it to `normal` or a length value. It can be zero but not negative. When you set it to `normal`, it uses the browser-defined default spacing between the columns.

The `column-rule` property is a shorthand that enables us to add a line between columns. This is similar to using the `border-left` or `border-right` properties. This property follows the syntax:

```
.example {
 column-rule: <'column-rule-width'> || <'column-rule-style'> || <'column-rule-color'>
}
```

For `column-rule-width`, you can specify the width as a length like `3px` or use a keyword like `thin`, `medium`, or `thick`. `column-rule-style` accepts values like `dashed`, `dotted`, `solid`, etc. You can use all the valid values of the `border-style` property with `column-rule-style` and `column-rule-color` can be any valid color value.

[**Here is a CodePen demo with these properties used together**](http://codepen.io/SitePoint/pen/PNqQxd)

## Column Fill and Column Span

The `column-fill` property determines how the content is to be partitioned to fill various columns. This property can be set to `auto` or `balance`. When set to `auto`, the columns are filled sequentially. Using `balance` makes sure that the content is divided equally between all the columns.

One thing worth keeping in mind is that, if you set a fixed height on a columned element, Firefox automatically balances the content. Other browsers, however, start filling the columns sequentially.

The `column-span` property controls how an element spans across multiple columns. It has two possible values: `all` or `none`. When set to `all`, the element spans across all columns. This property is [not supported in Firefox](https://bugzilla.mozilla.org/show_bug.cgi?id=616436).

[**Here is a CodePen demo that "spans" a blockquote element across all columns**](http://codepen.io/SitePoint/pen/yONvry)

In Firefox, the blockquote ends up in the middle column, which might be an acceptable fallback.

## Creating Responsive Layouts with Multiple Columns

Now that you are up to speed on the different properties and possible values, let's focus on keeping our layout responsive as well as user friendly.

Both `column-count` and `column-width` have their own issues. While `column-count` keeps the number of columns in check on larger devices, the layout will break on smaller ones. Similarly, `column-width` will make sure that the columns are not too narrow on smaller devices but result in lots of columns on larger ones.

To make sure your layout looks good on all screen sizes, you should specify both `column-count` and `column-width`. This way you can keep the width and number of columns under control. You might still need to fix a few issues, however, which we will discuss next.

If you specify a fixed height for your columns, making the viewport narrower will result in a horizontal scrollbar. Since the content cannot expand vertically, it will grow horizontally. To fix this, you can resize your browser to know the width when horizontal scrollbars first appear. Then, you use media queries to set the height of columns to `auto` below that width. Here is the code to do so:

```
.responsive-height {
 height: 250px;
}

@media (max-width: 1040px) {
 .responsive-height {
 height: auto;
 }
}
```

[**This CodePen demo shows both the problem and possible solution**](http://codepen.io/SitePoint/pen/vGOdwj)

Resize the window to see how both examples respond.

### Fixing Very Long Columns

If your columns are too long, it will be an annoyance for users to keep scrolling up and down to read all the content. When the height of columns becomes greater than the viewport height itself, it is a good idea to get rid of multiple columns. This can be achieved again using media queries:

```
@media (min-width: 800px) {
 .long-columns {
 columns: 3 200px;
 }
}
```

In this case, I am using multiple columns only when the viewport width is such that users no longer have to scroll up and down.

[**View demo with multiple columns and media queries**](http://codepen.io/SitePoint/pen/reVJEL)

## Conclusion

I hope this introduction to CSS's multi-column layout module has made you familiar with this feature. These properties can be a nice addition to your responsive design toolbox, and if you are still required to support older browsers, multiple columns usually degrade gracefully to a single column.
