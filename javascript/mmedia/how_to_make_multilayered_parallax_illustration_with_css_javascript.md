# How to make Multi-Layered Parallax Illustration with CSS & Javascript

[Original URL](https://medium.com/@PatrykZabielski/how-to-make-multi-layered-parallax-illustration-with-css-javascript-2b56883c3f27#.ihm1rcnnu)

> The Painting used for the parallax effect, made by Patryk Zabielski.Hey friends, I will be showing you how to create a simple multi-layered illustration with depth that transitions into the content...

![](https://cdn-images-1.medium.com/max/1200/1*n031qAVaSjia44OBenLOnw.png)

The Painting used for the parallax effect, made by Patryk Zabielski.

Hey friends, I will be showing you how to create a simple multi-layered illustration with depth that transitions into the content in a unique way. We'll be using a technique that involves CSS and pure JS (no jQuery!).

The tutorial is for beginners with basic Javascript and CSS knowledge, so I will be explaining most of the things and link to external sources.

[**Preview final effect**](http://codepen.io/zabielski/full/MyoBaY)

Let's start by cutting the painting into layers. Best-case-scenario would be a picture that you painted or if you have access to a source file with all the layers.

If you don't, then do not worry we can fix that.

I will be providing a painting that I did last year. If you don't feel like creating your own assets, you can download assets before we start _for free_.

[**Download assets**](https://www.dropbox.com/s/o41hppzrgcspto6/parallax_illustration_assets.zip?dl=0)

![](https://cdn-images-1.medium.com/max/600/1*AMb-_gXiOj8B_kK1YkqBKw.png)

Visualized concept of layers in 3D Space What we are looking for is splitting the painting into multiple png files with transparent background (layers), which will allow us to create the sense of depth.

Layers in the back will be moving slower than the ones in the foreground what will give us the effect of depth.

One other thing that will give a better transition to next section is making the bottom of the image the same color as the background. I have added a slight foreground line on the bottom and made sure that it's not transparent on the bottom.

![](https://cdn-images-1.medium.com/max/1200/1*MKLaI7GQ1xn2e_fH0Y69hg.png)

The Version on the right has a slight line added that will give us better transition into next section.

What you will need:

- A fresh [**Codepen**](http://www.codepen.com) _**_ (if you'd like to follow code below remember to change HTML to HAML, CSS to SCSS and JS to Coffeescript in Codepen settings)

- Basic knowledge of [**HAML**](http://haml.info/)  ****& [**Sass**](http://sass-lang.com/)**** (or use converters like [**HAML Converter**](http://www.haml-converter.com/)  ****&****  [**Sass Converter**](http://www.sassmeister.com/))

- Basic understanding of **Javascript** and [**Coffeescript**](http://coffeescript.org/) (If you're still struggling with that -- I recommend [**"Javascript & jQuery" by Jon Duckett**](http://www.javascriptbook.com/)). _**_ If you don't feel like playing around with Coffeescript then you can use [**this Coffescript converter**](http://js2.coffee/) and paste in code snippets below to get raw js.

- Your illustration/photo split into multiple png layers.

Let's start with the HTML structure. We will create a parent container and give it an id of '_hero_'. Then we want to add multiple divs with class "_layer_" ( one for each layer you have created) and [**data-type**](https://developer.mozilla.org/en/docs/Web/Guide/HTML/Using_data_attributes) attribute with value "_parallax._"

```
#hero
 .layer{“data-type” => “parallax”}
 .layer{“data-type” => “parallax”}
 .layer{“data-type” => “parallax”}
 .layer{“data-type” => “parallax”}
 .layer{“data-type” => “parallax”}
```

Let's add our basic styling. We'll start by styling the id _"hero"_. I have set the height of the illustration to 800 px.

```
#hero {
 height: 800px;
 overflow: hidden;
 position: relative;
}
```

Now, let's move to styling the repeating class of layers. All of them will have the same height as the id _"hero"_ container base positioning, and we'll add _position: fixed_.

```
.layer {
 background-position: bottom center;
 background-size: auto;
 background-repeat: no-repeat;
 width: 100%;
 height: 800px;
 position: fixed;
 z-index: -1;
}
```

The next thing we'll want to do is add the layer images we have previously prepared. We'll create another set of classes, one for every layer. Then, place the URL of the picture inside the "_background-image_" property.

```
.layer-01 {
 background-image: url(‘input_link_to_image_here');
}
.layer-02 {
 background-image: url(‘input_link_to_image_here');
}

// etc.
```

Let's not forget to update the HTML file and assign the classes to the proper divs in our order, with the first layer being the background; the subsequent layers will be stacked on top of each other as you add the next numbers.

```
#hero
 .layer.layer-01{“data-type” => “parallax”}
 .layer.layer-02{“data-type” => “parallax”}
 .layer.layer-03{“data-type” => “parallax”}
 .layer.layer-04{“data-type” => “parallax”}
 .layer.layer-05{“data-type” => “parallax”}
```

Now, let's add a method that will check if the user has begun scrolling down.

```
window.addEventListener ‘scroll’, (event)
```

> The EventTarget.addEventListener() method registers the specified listener on the [EventTarget](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget "EventTarget is an interface implemented by objects that can receive events and may have listeners for them.") it's called on. The event target may be an [Element](https://developer.mozilla.org/en-US/docs/Web/API/Element "The Element interface represents an object of a Document. This interface describes methods and properties common to all kinds of elements. Specific behaviors are described in interfaces which inherit from Element but add additional functionality.") in a document, the [Document](https://developer.mozilla.org/en-US/docs/Web/API/Document "The Document interface represent any web page loaded in the browser and  serves as an entry point into the web page's content, the DOM tree (including elements such as <body> or ). It provides functionality which is global to the document, such as obtaining the page's URL and creating new elements in the document.") itself, a [Window](https://developer.mozilla.org/en-US/docs/Web/API/Window "The window object represents a window containing a DOM document; the document property points to the DOM document loaded in that window."), or any other object that supports events (such as [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest)).

Then let's store the value of pixels that the document has already been scrolled vertically into the _topDistance_ variable. To do that, we'll use the [**pageYOffset**](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollY) _**_ property.

```
window.addEventListener ‘scroll’, (event) ->
 topDistance = @pageYOffset
```

After that, we want to select all the layers in our illustration and store them into a variable called '_layers_'. To do that, we'll use the [**querySelectorAll**](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) _**_ method and data attribute inside the HTML that we have specified previously.

```
window.addEventListener ‘scroll’, (event) ->
 topDistance = @pageYOffset
 layers = document.querySelectorAll("[data-type='parallax']")
```

Next thing we'll have to do is to loop through all the layers and apply proper _transform_ to each of the layers. But before that, we need to specify one more thing inside of our HTML file, the _data-depth_. It will allow us to control how fast the elements move, let's not dig too deep into the values inside of it yet, we'll get back to this later on.

```
#hero
 .layer.layer-01{“data-type” => “parallax”, "data-depth" => "0.10"}
 .layer.layer-02{“data-type” => “parallax”, "data-depth" => "0.20"}
 .layer.layer-03{“data-type” => “parallax”, "data-depth" => "0.50"}
 .layer.layer-04{“data-type” => “parallax”, "data-depth" => "0.80"}
 .layer.layer-05{“data-type” => “parallax”, "data-depth" => "1.00"}
```

For looping through all the elements, we'll use the _for_ loop. We start the loop by creating a variable where we will be storing our layers (coffescript allows us to do that all with a simple sentence _for layer in layers_); then take the value from the data-depth attribute we specified inside of our HTML. After that, we calculate the movement of the layers by multiplying the distance from the top of the page by our data-depth for the given layer. Element with the value of 1.0 will flow normally with the rest of the document, you can think of this as a "parallax off" state.

The last thing we do is we update the final value of movement to the layer's CSS parameter of _transform_ _translate3d_, to do that we'll use [**style**](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style) property along with all the prefixes for _transform_.

To make things more readable and [**DRY**](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), we'll store the translate3d property in a variable called 'translate3d'.

```
for layer in layers
 depth = layer.getAttribute(‘data-depth’)
 movement = -(topDistance articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata playground start-thesrc swift thesrc ucii depth)
 translate3d = 'translate3d(0, ' + movement + 'px, 0)'
 layer.style['-webkit-transform'] = translate3d
 layer.style['-moz-transform'] = translate3d
 layer.style['-ms-transform'] = translate3d
 layer.style['-o-transform'] = translate3d
 layer.style.transform = translate3d
```

Now when we specify the _data-depth="1.00"_ the element will move with the page as a standard element with no parallax effect. All values that are less than 100 will have the parallax effect.

For mobile we'll be turning off the parallax version and replacing it with static image to save up on performance and file size (since it requires multiple png files). To do that we'll create a new div below id _hero_ with id of _hero-mobile_ and apply _display: none_ along with the background and height property.

```
#hero-mobile {
 display: none;
 background: url(“https://s3-us-west-2.amazonaws.com/s.cdpn.io/272781/full_illustration.png") no-repeat center bottom / cover;
 height: 320px;
}
```

To show it instead of parallax we'll use simple [**media query**](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries) _**_ and apply _display: none_ to desktop version, while overriding our _display: none_ with _display: block_ on _hero-mobile_.

```
@media all and (max-width: 640px) {
 #hero {
 display: none;
 }
 #hero-mobile {
 display: block;
 }
}
```

Further tweaking involves adding _background-position_ to the individual layer classes to position elements as you wish.

Thank you so much for going through this guide. I sincerely hope that I have managed to show you a trick or two. I did learn a lot by writing this first tutorial of mine, and I would love to hear your feedback.

Feel free to reach out to me on twitter [**@patrykzabielski**](https://twitter.com/PatrykZabielski).

**_Thank you for hitting the_** ❤ **_if you liked the guide.<br>
This will tell me to write more!_**
