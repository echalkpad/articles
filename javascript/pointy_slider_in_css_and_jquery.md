# Pointy Slider in CSS and jQuery

[Original URL](https://codyhouse.co/gem/pointy-slider/)

> A slideshow with sliding-in panels that unveil new, fixed background images. Browser support ie Chrome Firefox Safari Opera 9+ Today's resource is a simple, responsive slider, with a sharp...

A slideshow with sliding-in panels that unveil new, fixed background images.

**Browser support**

- ie
- Chrome
- Firefox
- Safari
- Opera

<span class="ie-version-supported">9+</span>

Today's resource is a simple, responsive slider, with a sharp design and an interesting motion effect: with each new slide item, a sliding-in block of content covers the old one, and unveils a new image.

Inspiration: [shft.run](http://shft.run/)

Images: [unsplash.com](https://unsplash.com/)

## Creating the structure

The **HTML structure** is composed by two main elements: a `ul.cd-slider` for the slides, and a `ol.cd-slider-navigation` for the slider navigation; both are wrapped inside a `div.cd-slider-wrapper`.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-r">&lt;div </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-slider-wrapper”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;ul </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-slider”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;li </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“is-visible”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;div </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-half-block image”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-r">&lt;/div&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;div </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-half-block content”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;div&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;h2&gt;</span>
  <span class="crayon-i">Slide Number 1</span>
  <span class="crayon-r">&lt;/h2&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;p&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– content here –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/p&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/div&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/div&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/li&gt;</span>
  <span class="crayon-c">&lt;!– .cd-half-block.content –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;li&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– item content here –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/li&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– addition list items here –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/ul&gt;</span>
  <span class="crayon-c">&lt;!– .cd-slider –&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– The ol.cd-slider-navigation element is created using jQuery and inserted here–&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/div&gt;</span>
  <span class="crayon-c">&lt;!– .cd-slider-wrapper –&gt;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Note that the `ol.cd-slider-navigation` element is not directly inserted in the HTML but created using jQuery.

## Adding style

On small devices (viewport width smaller than 900px), the slider structure is pretty straightforward: the `.cd-slider` element has a relative position while its children `<li>` have an absolute position, with a top and left of zero.<br>
All the list items are translated to the right, outside the viewport (`translateX(100%)`); the `.is-visible` class is added to the visible one to move it back into the viewport (`translateX(0)`).

 | <div class="crayon-pre">
  <p>
  <span class="crayon-k">.cd-slider </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">position</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">relative</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">height</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">100%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">overflow</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">hidden</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-slider li </span>
  <span class="crayon-sy">{</span>
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
  <span class="crayon-e">height</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">100%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">width</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">100%</span>
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
  <span class="crayon-i">0.6s</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-slider li.is-visible </span>
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
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

On bigger devices, the list items are moved back inside the viewport (`translateX(0)`), while their two children elements, `.cd-half-block.content` and `.cd-half-block.image`, are translated to the right (`translateX(200%)` and `translateX(100%)` respectively). This way, both elements are outside the viewport, overlapping.

When the `.is-visible` class is added to the selected list item, the two `.cd-half-block` elements are moved back inside the viewport (`translateX(0)`).<br>
We used **CSS3 Transitions** to animate both elements: for the `.cd-half-block.content` we set a transition-duration of 0.6s and a transition-delay of 0s, while for the `.cd-half-block.image` we set a transition-duration of 0s and a transition-delay of 0.3s.<br>
This way, when the `.cd-half-block.content` has translated of half the total translation value (100%), the `.cd-half-block.image` is moved back inside the viewport (`translateX(0)`) instantaneously (`transition-duration: 0s`). This creates the unveiling image effect.

Here is a quick animation that explains the logic behind the blocks animation (.gif created using After Effects):

![pointy-blocks-animation](https://0bf196087c14ed19d1f11cf1-ambercreativelab.netdna-ssl.com/wp-content/uploads/2016/01/pointy-blocks-animation.gif)

 | <div class="crayon-pre">
  <p>
  <span class="crayon-n">@media only screen and (min-width: 900px) {</span>
</p>
  <p>
  <span class="crayon-n">  .cd-slider li {</span>
</p>
  <p>
  <span class="crayon-n">    transform: translateX(0);</span>
</p>
  <p>
  <span class="crayon-k">.cd-slider .cd-half-block </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">height</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">100%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">width</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">50%</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">float</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">right</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-slider .cd-half-block.content </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">200%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">transition</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">transform</span>
  <span class="crayon-i">0.6s</span>
  <span class="crayon-i">0s</span>
  <span class="crayon-i">ease-in-out</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-slider .cd-half-block.image </span>
  <span class="crayon-sy">{</span>
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
  <span class="crayon-i">0s</span>
  <span class="crayon-i">0.3s</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-slider li.is-visible .cd-half-block.content,</span>
</p>
  <p>
  <span class="crayon-k">  .cd-slider li.is-visible .cd-half-block.image </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transform</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">translateX</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">0%</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-slider li.is-visible .cd-half-block.content </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">transition</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">transform</span>
  <span class="crayon-i">0.6s</span>
  <span class="crayon-i">0s</span>
  <span class="crayon-i">ease-in-out</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

One thing to note: we set the `.cd-half-block.image` transition-delay to be equal to half the `.cd-half-block.content` transition-duration. This is because we used `ease-in-out` as timing-function, which assures that after half the transition-duration (in our case .3s), the `.cd-half-block.content` has actually moved of half the total translation value.

## Events handling

We used jQuery to create and insert into the DOM the slider navigation.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">sliderPagination</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">createSliderPagination</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">sliderContainer</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">// sliderContainer = $(’.cd-slider-wrapper’)</span>
</p>
  <p>
  <span class="crayon-t">function</span>
  <span class="crayon-e">createSliderPagination</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">container</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">wrapper</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘&lt;ol class=“cd-slider-navigation”&gt;&lt;/ol&gt;’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">container</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">children</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘.cd-slider’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">find</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘li’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-st">each</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-t">function</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">dotWrapper</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">index</span>
  <span class="crayon-o">==</span>
  <span class="crayon-cn">0</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">?</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘&lt;li class=“selected”&gt;&lt;/li&gt;’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘&lt;li&gt;&lt;/li&gt;’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">dot</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘&lt;a href=“#0”&gt;&lt;/a&gt;’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">appendTo</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">dotWrapper</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">dotWrapper</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">appendTo</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">wrapper</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">dotText</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">index</span>
  <span class="crayon-o">+</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-cn">10</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">?</span>
  <span class="crayon-s">‘0’</span>
  <span class="crayon-o">+</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">index</span>
  <span class="crayon-o">+</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">index</span>
  <span class="crayon-o">+</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">dot</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">text</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">dotText</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">wrapper</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">appendTo</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">container</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">return</span>
  <span class="crayon-v">wrapper</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">children</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘li’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Besides, we used jQuery to implement a basic slider functionality (touch swipe and slider navigation).

**Jan 18, 2016**

- Resource released by CodyHouse
