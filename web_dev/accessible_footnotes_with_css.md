# Accessible Footnotes with CSS

[Original URL](http://www.sitepoint.com/accessible-footnotes-css/)

> I was playing with CSS counters the other day and thought about using them to deal with footnotes. According to Plagiarism which has a surprisingly long entry on the matter, footnotes are: [...]...

I was playing with [CSS counters](http://www.sitepoint.com/understanding-css-counters-and-their-use-cases/) the other day and thought about using them to deal with footnotes. According to [Plagiarism](http://www.plagiarism.org/citing-sources/what-are-footnotes/) which has a surprisingly long entry on the matter, footnotes are:

> [...] notes placed at the bottom of a page. They cite references or comment on a designated part of the text above it.

You often see them in papers when the author wants to add a piece of information or cite a reference without doing it in the middle of the content or using parentheses. Usually, footnotes are represented with a number according to the position of the footnote in the document, then the same numbers are present at the bottom of the document, adding extra content.

The problem with footnotes on the web is that they can be a pain to maintain. If you happen to work on the same document often, changing the order of sections, adding references along the way, it might be tedious to have to re-number all existing footnotes. For example, if you have 3 existing references to footnotes in a document, and you want to add another one, but on a piece of content that occurs before all the others, you have to re-number them all. Not great...

We could use CSS counters to make this whole thing much easier. What if we did not have to maintain the numbering by hand and it could be done automatically? The only thing we would have to pay attention to, is that the order of the actual notes in the footer respect the order of appearance of the references in the text.

## Creating a sample document

Let's create a sample document so we can get started.

```
<article>
 <h1>CSS-Powered Footnotes</h1>

 <p>Maintaining <a href="#footnotes">footnotes</a> manually can be a pain. 
 By using <a href="#css">CSS</a> <a href="#css-counters">counters</a> to add 
 the numbered references in the text and an ordered list to display the actual 
 footnotes in the footer, it becomes extremely easy.</p>

 <footer>
 <ol>
 <li id="footnotes">Footnotes are notes placed at the bottom of a page. They 
 cite references or comment on a designated part of the text above it.</li>

 <li id="css">Cascading Style Sheets</li>

 <li id="css-counters">CSS counters are, in essence, variables maintained by 
 CSS whose values may be incremented by CSS rules to track how many times 
 they're used.</li>
 </ol>
 </footer>
</article>
```

Our example is lightweight: we have some content in an `<article>` element, which contains some links (`<a>`) pointed at in-document IDs, mapped to the notes in the `<footer>` of the article.

With a few styles, it might look like this:

![Accessible footnotes with CSS – raw version](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/06/1434403915footnotes-1.png)

## Making it accessible

Before actually getting onto the counters thing, we should make sure our markup is fully accessible for screen-readers. The first thing we want to do is add a title to our footer that will serve as a description or our footnote references. We'll hide this title with CSS so it doesn't show up visually.

```
<footer>
 <h2 id="footnote-label">Footnotes</h2>
 <ol>
 ...
 </ol>
</footer>
```

Then, we want to describe all our references with this title, using the `aria-describedby` attribute:

```
<p>Maintaining <a aria-describedby="footnote-label" href="#footnotes">footnotes</a> 
manually can be a pain. By using <a aria-describedby="footnote-label" href="#css">CSS</a> 
<a aria-describedby="footnote-label" href="#css-counters">counters</a> to add the 
numbered references in the text and an ordered list to display the actual footnotes 
in the footer, it becomes extremely easy.</p>
```

Now screen reader users will understand when links are references to footnotes.

## Adding the references

I know what you're thinking: _He said there would be CSS counters. Where are the CSS counters?_ Well worry not, my friend, they are coming.

What we are going to do is increment a counter for every link in the document that has an `aria-describedby` attribute set to `footnote-label`. Then we'll display the counter using the `::after` pseudo-element. From there, it's all about applying CSS styles.

```
/**
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii Initialiazing a `footnotes` counter on the wrapper
 */
article {
 counter-reset: footnotes;
}

/**
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii Inline footnotes references
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1\. Increment the counter at each new reference
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 2\. Reset link styles to make it appear like regular text
 */
a[aria-describedby="footnote-label"] {
 counter-increment: footnotes; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 1 */
 text-decoration: none; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 2 */
 color: inherit; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 2 */
 cursor: default; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 2 */
 outline: none; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 2 */
}

/**
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii Actual numbered references
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 1\. Display the current state of the counter (e.g. `[1]`)
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 2\. Align text as superscript
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 3\. Make the number smaller (since it's superscript)
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 4\. Slightly offset the number from the text
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 5\. Reset link styles on the number to show it's usable
 */
a[aria-describedby="footnote-label"]::after {
 content: '[' counter(footnotes) ']'; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 1 */
 vertical-align: super; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 2 */
 font-size: 0.5em; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 3 */
 margin-left: 2px; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 4 */
 color: blue; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 5 */
 text-decoration: underline; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 5 */
 cursor: pointer; /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old 5 */
}

/**
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii Resetting the default focused styles on the number
 */
a[aria-describedby="footnote-label"]:focus::after {
 outline: thin dotted;
 outline-offset: 2px;
}
```

Now it looks like this:

![Accessible footnotes with CSS](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/06/1434403907footnotes-2.png)

Pretty nice, huh? As a final touch, when heading to a footnote from a reference, we want to highlight the note in the footer so we actually see what is the note being referred to, which we can do using the `:target` pseudo-class:

```
footer :target {
 background: yellow;
}
```

It is a bit raw, so feel free to customise. Although I must say I like the pure yellow for a highlight – it looks so authentic:

![Accessible footnotes with CSS – A footnote is targeted](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/06/1434403911footnotes-3.png)

Our demo needs one final element to be fully accessible (as well as pretty cool): back-to-content links. Think about it: You focus a reference, head to the relevant note in the footer, read it and then... nothing. You need a way to go back to where you left!

Providing those links is not that hard: we only need to add a unique ID attribute to each reference in the content so they can be linked to. I decided to go simple and take the ID they refer to, and simply append `-ref` to it:

```
<p>Maintaining <a aria-describedby="footnote-label" href="#footnotes" id="footnotes-ref">footnotes</a> 
manually can be a pain. By using <a aria-describedby="footnote-label" href="#css" id="css-ref">CSS</a> 
<a aria-describedby="footnote-label" href="#css-counters" id="css-counters-ref">counters</a> 
to add the numbered references in the text and an ordered list to display the actual 
footnotes in the footer, it becomes extremely easy.</p>
```

Then each list item from the footer has its own link heading to the relevant `id` we just added. The content of the link is the _backlink_ Unicode icon (↩), and it has an `aria-label` attribute with a value of "Back to content".

```
<ol>
 <li id="footnotes">Footnotes are notes placed at the bottom of a page. 
 They cite references or comment on a designated part of the text above it. 
 <a href="#footnotes-ref" aria-label="Back to content">↩</a></li>
 <li id="css">Cascading Style Sheets 
 <a href="#css-ref" aria-label="Back to content">↩</a></li>
 <li id="css-counters">CSS counters are, in essence, variables maintained 
 by CSS whose values may be incremented by CSS rules to track how many 
 times they're used. <a href="#css-counters-ref" aria-label="Back to content">↩</a></li>
</ol>
```

To target those links in CSS, we can rely on the `aria-label` attribute the same way we did for `aria-describedby`:

```
[aria-label="Back to content"] {
 font-size: 0.8em;
}
```

Here is what the final demo looks like:

See the Pen [Accessible footnotes with CSS](http://codepen.io/SitePoint/pen/QbMgvY/) by SitePoint ([@SitePoint](http://codepen.io/SitePoint)) on [CodePen](http://codepen.io).

## Final thoughts

With nothing but a couple of lines of CSS and a few ARIA attributes, we managed to create CSS-powered footnotes that are accessible and do not need any JavaScript. How cool is that?

On topic, I highly recommend [Semantic CSS with intelligent selectors](http://www.smashingmagazine.com/2013/08/20/semantic-css-with-intelligent-selectors/) from [Heydon Pickering](https://twitter.com/heydonworks). Also, be sure to check out [a11y.css](https://ffoodd.github.io/a11y.css/) from [Gaëk Poupard](https://twitter.com/ffoodd_fr) to check the accessibility of your pages.

_Huge thanks to [Heydon Pickering](https://twitter.com/heydonworks) for his valuable help regarding accessibility in this demo._
