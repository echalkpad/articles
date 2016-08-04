# Vertical Fixed Navigation 2 in CSS and jQuery

[Original URL](https://codyhouse.co/gem/vertical-fixed-navigation-2/)

> A smart vertical navigation, with round indicators that turn into labelled icons when the user interacts with them. Browser support ie Chrome Firefox Safari Opera 9+ Our first concept of vertical...

A smart vertical navigation, with round indicators that turn into labelled icons when the user interacts with them.

**Browser support**

- ie
- Chrome
- Firefox
- Safari
- Opera

<span class="ie-version-supported">9+</span>

Our first concept of [vertical fixed navigation](https://codyhouse.co/gem/vertical-fixed-navigation/) is one of our most popular resources. This time, we tried to push this concept a little further.

The basic idea behind putting round indicators on the side of a web page, is to give a hint to the user about the number of sections she/he can go through. We think of each dot as a content chapter, with its own title. Usually, users have to hover over a dot to access the title.

In an attempt to simplify this pattern, we decided to transform the dots when the user interacts with them, by scaling them up and showing an icon + label. Users don't need to select a specific dot/item, but just move to the side, thus showing their willingness to access the navigation.

Here is a quick preview of the final result (created using After Effects):

![v-nav-animation](https://0bf196087c14ed19d1f11cf1-ambercreativelab.netdna-ssl.com/wp-content/uploads/2016/01/v-nav-animation.gif)

Icons: [Nucleoapp.com](https://nucleoapp.com/)

## Creating the structure

Our navigation is an unordered list wrapped in a `nav.cd-vertical-nav`. A `button.cd-nav-trigger` is used to open the navigation on small devices.<br>
Besides, a `section.cd-section` has been created for each navigation item.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-r">&lt;nav </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-vertical-nav”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;ul&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;li&gt;</span>
  <span class="crayon-r">&lt;a </span>
  <span class="crayon-e">href</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“#section1”</span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“active”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-r">&lt;span </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“label”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-i">Intro</span>
  <span class="crayon-r">&lt;/span&gt;</span>
  <span class="crayon-r">&lt;/a&gt;</span>
  <span class="crayon-r">&lt;/li&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;li&gt;</span>
  <span class="crayon-r">&lt;a </span>
  <span class="crayon-e">href</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“#section2”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-r">&lt;span </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“label”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-i">Events</span>
  <span class="crayon-r">&lt;/span&gt;</span>
  <span class="crayon-r">&lt;/a&gt;</span>
  <span class="crayon-r">&lt;/li&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– additional navigation items here –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/ul&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/nav&gt;</span>
  <span class="crayon-c">&lt;!– .cd-vertical-nav –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;button </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-nav-trigger cd-image-replace”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-i">Open navigation</span>
  <span class="crayon-r">&lt;span </span>
  <span class="crayon-e">aria-hidden</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“true”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-r">&lt;/span&gt;</span>
  <span class="crayon-r">&lt;/button&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;section </span>
  <span class="crayon-e">id</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“section1”</span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-section”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;div </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“content-wrapper”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;h1&gt;</span>
  <span class="crayon-i">Vertical Fixed Navigation #2</span>
  <span class="crayon-r">&lt;/h1&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;a </span>
  <span class="crayon-e">href</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“#section2”</span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-scroll-down cd-image-replace”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-i">scroll down</span>
  <span class="crayon-r">&lt;/a&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/div&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/section&gt;</span>
  <span class="crayon-c">&lt;!– cd-section –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;section </span>
  <span class="crayon-e">id</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“section2”</span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-section”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;div </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“content-wrapper”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– section content here –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/div&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/section&gt;</span>
  <span class="crayon-c">&lt;!– cd-section –&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– additional sections here –&gt;</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Adding style

On small devices (viewport width smaller than 800px), we set a `position: fixed` for the `.cd-nav-trigger` and `<nav>` elements and placed them at the bottom-right corner of the page; we then scale down the navigation, using the bottom-right corner as transform origin.

When user clicks on the `.cd-nav-trigger` element, we give the `.open` class to the navigation to change its scale value from 0 to 1, with a **CSS3 transition** to achieve a smooth animation.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-k">.cd-nav-trigger </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">display</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">block</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">position</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">fixed</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">z-index</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">2</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">bottom</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">30px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">right</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">5%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">position</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">fixed</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">z-index</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">1</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">right</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">5%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">bottom</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">30px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transform-origin</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">right</span>
  <span class="crayon-i">bottom</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transition</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">transform</span>
  <span class="crayon-i">0.2s</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav.open </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">1</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

On bigger devices, we use [Modernizr](http://modernizr.com/ "Modernizr") to detect touch and no-touch devices (using`.touch` and `.no-touch` classes).<br>
On touch devices, the lateral navigation items (labels and icons) are visible by default, while on no-touch devices they are shown when the user hovers over the lateral navigation.

We set a fixed height and width for the `<nav>` element, and place it on the right side of the viewport. We use its `::before` pseudo-element to create the navigation background; on no-touch devices only, the `::before` element is, by default, translated to the right (outside the viewport) and is moved back to its original position when the user hovers over the navigation. The same happens for the `span.label` elements.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-n">@media only screen and (min-width: 800px) {</span>
</p>
  <p>
  <span class="crayon-n">  .cd-vertical-nav {</span>
</p>
  <p>
  <span class="crayon-n">    right: 0;</span>
</p>
  <p>
  <span class="crayon-e">top</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">height</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">100vh</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">width</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">90px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav::before </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-c">/<em> this is the navigation background </em>/</span>
</p>
  <p>
  <span class="crayon-e">content</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-s">’’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">position</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">absolute</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">top</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">left</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">width</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">100%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">height</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">100%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">background</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">rgba</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0,</span>
  <span class="crayon-i">0,</span>
  <span class="crayon-i">0,</span>
  <span class="crayon-i">0.8</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">100%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transition</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">transform</span>
  <span class="crayon-i">0.4s</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.no-touch .cd-vertical-nav:hover::before, </span>
</p>
  <p>
  <span class="crayon-k">  .touch .cd-vertical-nav::before </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav .label </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">display</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">block</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">100%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transition</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">transform</span>
  <span class="crayon-i">0.4s</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.no-touch .cd-vertical-nav:hover .label, </span>
</p>
  <p>
  <span class="crayon-k">  .touch .cd-vertical-nav .label </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

To create the navigation item icons and dots, we use, respectively, the `::after` and `::before` pseudo-elements of the navigation anchor elements (`<a>`). On no-touch devices only, the `::after` and `::before` are scaled down by default and then scaled back up when the user hovers over the navigation.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-n">@media only screen and (min-width: 800px) {</span>
</p>
  <p>
  <span class="crayon-n">  .cd-vertical-nav a {</span>
</p>
  <p>
  <span class="crayon-n">    position: relative;</span>
</p>
  <p>
  <span class="crayon-e">padding</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">3em</span>
  <span class="crayon-i">0</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">margin</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">1.4em</span>
  <span class="crayon-i">auto</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav a::before, </span>
</p>
  <p>
  <span class="crayon-k">  .cd-vertical-nav a::after </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-c">/<em> used to create the filled circle and the background icon </em>/</span>
</p>
  <p>
  <span class="crayon-e">content</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-s">’’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">position</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">absolute</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">left</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">50%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transition</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">transform</span>
  <span class="crayon-i">0.4s</span>
  <span class="crayon-i">0s</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav a::before </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-c">/<em> filled circle </em>/</span>
</p>
  <p>
  <span class="crayon-e">top</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">height</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">32px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">width</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">32px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">border-radius</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">50%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">background</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">#eaf2e3</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0.25</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav a::after </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-c">/<em> icon </em>/</span>
</p>
  <p>
  <span class="crayon-e">top</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">8px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">height</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">16px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">width</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">16px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">background</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">url</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">../img/cd-nav-icons.svg</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">no-repeat</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.no-touch .cd-vertical-nav:hover a::before, </span>
</p>
  <p>
  <span class="crayon-k">  .no-touch .cd-vertical-nav:hover a::after, </span>
</p>
  <p>
  <span class="crayon-k">  .touch .cd-vertical-nav li:nth-of-type(n) a::before, </span>
</p>
  <p>
  <span class="crayon-k">  .touch .cd-vertical-nav li:nth-of-type(n) a::after </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">1</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now the tricky part: when the navigation dots are scaled down, they are too distant one from the other. We can reduce this distance translating them along the Y axis.

Let's start from the central dots (in our case, the second and the third); we want to translate down the second one (so we have to use a positive translate value), while we want to translate up the third one (so we have to use a negative translate value). In our case we have:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-k">.cd-vertical-nav li:nth-of-type(2) a::after </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">translateY</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">1.5em</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav li:nth-of-type(2) a::before </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">translateY</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">1.5em</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0.25</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav li:nth-of-type(3) a::after </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">translateY</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-1.5em</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav li:nth-of-type(3) a::before </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">translateY</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-1.5em</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0.25</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Then, the translate value for the first dot is gonna be three times the one of the second dot, and the same for the fourth one (three times the translate value of the third dot).

 | <div class="crayon-pre">
  <p>
  <span class="crayon-k">.cd-vertical-nav li:first-of-type a::after </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">translateY</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">4.5em</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav li:first-of-type a::before </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">translateY</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">4.5em</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0.25</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav li:nth-of-type(4) a::after </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">translateY</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-4.5em</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-vertical-nav li:nth-of-type(4) a::before </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-50%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">translateY</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">-4.5em</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-i">scale</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0.25</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

If you have a different number of navigation items, you have to change these translate values accordingly. For example, if you have six items, starting again from the central dots (in this case, the third and the fourth), you can assign them a translateY value of 1.5 em/-1.5em; then to the second and the fifth a translateY value of 4.5em/-4.5em (3*1.5), and finally to the first and sixth a translateY value of 7.5em/-7.5em (5*1.5).

If you have an odd number of items, let's say 5, you do not translate the central one (in this case the third one). You then assign a translateY value of 3em/-3em(2*1.5) to the second and the fourth dots, and finally a translateY value of 6em/-6em(4*1.5) to the first and fifth dots.

## Events handling

When user scrolls through the sections, the `updateSections()` function evaluates which section is currently being viewed and assigns the `.active` class to the corresponding navigation item.<br>
Besides, we listen to the click event on the `button.cd-nav-trigger` to open/close the navigation on small devices.

**Jan 07, 2016**

- Resource released by CodyHouse
