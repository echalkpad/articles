# Inspiration for Line Menu Styles

[Original URL](http://tympanus.net/codrops/2016/01/06/inspiration-for-line-menu-styles/)

> Some ideas for menu styles and effects that employ the line as a creative design element. This is an open collection; contributions are welcome! View demo

Some ideas for menu styles and effects that employ the line as a creative design element. This is an open collection; contributions are welcome!

![](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/themes/codropstheme03/images/advertisement.jpg)

[![LineMenuStyles](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2016/01/LineMenuStyles.png)](http://tympanus.net/Development/LineMenuStyles/)

[View demo](http://tympanus.net/Development/LineMenuStyles/) [Download source](http://tympanus.net/Development/LineMenuStyles/LineMenuStyles.zip)

The line is an amazing design element. It's so versatile and it allows us to use it for adding enjoyable animations or enhance points of interest. Today we'd like to share a set of ideas for using the line as a design element in horizontal menus. There are many creative possibilities, some minuscule and subtle, others bold and more extravagant. We've prepared a little inspirational set for you where we explore some line menu styles.

_We'd also like to invite you to contribute your style to this open collection. Check out [the details](https://github.com/codrops/LineMenuStyles) on GitHub._

![menustyle](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2016/01/menustyle.gif)

Let's have a look at the markup which is, aside from some exceptions, the same for every menu style:

```
<section class="section section--menu" id="Prospero">

    <h2 class="section__title">Prospero</h2>

    <nav class="menu menu--prospero">

        <ul class="menu__list">
            <li class="menu__item menu__item--current">
                <a class="menu__link">Home</a>
            </li>
            <li class="menu__item">
                <a class="menu__link">Who we are</a>
            </li>
            <li class="menu__item">
                <a class="menu__link">What we offer</a>
            </li>
            <li class="menu__item">
                <a class="menu__link">Our news</a>
            </li>
            <li class="menu__item">
                <a class="menu__link">Contact us</a>
            </li>
        </ul>

    </nav>

</section>
```

The `menu__item--current` class indicates the currently selected item. Note that we use a BEM naming convention but allow for the main `nav` to have an indicative class that we then use in our stylesheet (component.css) to define the individual styles.

Let's have a look at the common styles for all menus:

```
.menu {
    line-height: 1;
    margin: 0 auto 3em;
}

.menu__list {
    position: relative;
    display: -webkit-flex;
    display: flex;
    -webkit-flex-wrap: wrap;
    flex-wrap: wrap;
    margin: 0;
    padding: 0;
    list-style: none;
}

.menu__item {
    display: block;
    margin: 1em 0;
}

.menu__link {
    font-size: 1.05em;
    font-weight: bold;
    display: block;
    padding: 1em;
    cursor: pointer;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    -webkit-touch-callout: none;
    -khtml-user-select: none;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.menu__link:hover,
.menu__link:focus {
    outline: none;
}
```

We'll use flexbox for the menu layout and define some common styles for the links.

![](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/themes/codropstheme03/images/advertisement.jpg)

The following is an effect style example (vendor prefixed properties omitted):

```
/* Prospero */

.menu--prospero .menu__link {
    position: relative;
    display: block;
    margin: 0 1.5em;
    padding: 0.75em 0;
    text-align: center;
    color: #b5b5b5;
    transition: color 0.3s;
}

.menu--prospero .menu__link:hover,
.menu--prospero .menu__link:focus {
    color: #929292;
}

.menu--prospero .menu__item--current .menu__link {
    color: #d94f5c;
}

.menu--prospero .menu__link::before {
    content: '';
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    height: 4px;
    background: #d94f5c;
    transform: scale3d(0, 1, 1);
    transition: transform 0.1s;
}

.menu--prospero .menu__item--current .menu__link::before {
    transform: scale3d(1, 1, 1);
    transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
    transition-duration: 0.3s;
}
```

In this effect we simply use a pseudo element line that starts to expand from the middle. It is positioned absolutely at the bottom of the link and initially scaled to 0 on the X. When we select a menu item by clicking on it, the line gets scaled to 1.

We hope you enjoy this little set and find it inspirational!

Don't forget to fork the repo and add your styles if you like. Read more about it on the GitHub project page:

[View demo](http://tympanus.net/Development/LineMenuStyles/) [Download source](http://tympanus.net/Development/LineMenuStyles/LineMenuStyles.zip)
