# Reading Progress Indicator in SVG, CSS & jQuery

[Original URL](https://codyhouse.co/gem/reading-progress-indicator/)

> A widget containing a list of suggested articles, with a reading progress indicator powered by SVG, CSS and jQuery. Browser support ie Chrome Firefox Safari Opera 9+ Today's resource was...

A widget containing a list of suggested articles, with a reading progress indicator powered by SVG, CSS and jQuery.

**Browser support**

- ie
- Chrome
- Firefox
- Safari
- Opera

<span class="ie-version-supported">9+</span>

Today's resource was inspired by a widget found on [The Daily Beast](http://www.thedailybeast.com/articles/2015/10/23/why-bill-murray-is-a-golden-god.html): a list of related articles, enriched by a filling effect to indicate the reading progress. We created something similar, although we used SVG to animate the stroke property of a circle element.

Note that the url changes according to the article in focus, in case the user wants to share a specific article as opposed to the whole page.

Since such a widget is not a fundamental element of the page, but more of a subtle enrichment, 

<span>we decided to hide it on smaller devices</span>

.

## Creating the structure

The **HTML structure** is composed by `<article>` elements for the article contents, and an `<aside>` element wrapping the list of suggested articles.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-r">&lt;div </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-articles”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;article&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;header&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;img </span>
  <span class="crayon-e">src</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“img/img-1.png”</span>
  <span class="crayon-e">alt</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“article image”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;h1&gt;</span>
  <span class="crayon-i">20 Star Wars Secrets Revealed: From Leia’s ‘Cocaine Nail’ to the Ronald Reagan Connection</span>
  <span class="crayon-r">&lt;/h1&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/header&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;p&gt;</span>
</p>
  <p>
  <span class="crayon-i"> Lorem ipsum dolor sit amet, consectetur adipisicing elit. Perferendis maxime id, sunt, eum sed blanditiis aliquid! Minus assumenda tempore perspiciatis, numquam est aliquam, quis molestias enim consequuntur suscipit similique cumque ut natus facilis laboriosam quidem, nesciunt quasi doloribus tenetur. Quas doloremque suscipit, molestias odit, et quasi? Quas hic numquam, vitae?</span>
</p>
  <p>
  <span class="crayon-r">&lt;/p&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– additional content here –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/article&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;article&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– article content here –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/article&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– additional articles here –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;aside </span>
  <span class="crayon-e">class</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“cd-read-more”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;ul&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;li&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;a </span>
  <span class="crayon-e">href</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“index.html”</span>
  <span class="crayon-r">&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;em&gt;</span>
  <span class="crayon-i">20 Star Wars Secrets Revealed</span>
  <span class="crayon-r">&lt;/em&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;b&gt;</span>
  <span class="crayon-i">by J. Morrison</span>
  <span class="crayon-r">&lt;/b&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;svg </span>
  <span class="crayon-e">x</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“0px”</span>
  <span class="crayon-e">y</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“0px”</span>
  <span class="crayon-e">width</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“36px”</span>
  <span class="crayon-e">height</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“36px”</span>
  <span class="crayon-e">viewBox</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“0 0 36 36”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-r">&lt;circle </span>
  <span class="crayon-e">fill</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“none”</span>
  <span class="crayon-e">stroke</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“#2a76e8”</span>
  <span class="crayon-e">stroke-width</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“2”</span>
  <span class="crayon-e">cx</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“18”</span>
  <span class="crayon-e">cy</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“18”</span>
  <span class="crayon-e">r</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“16”</span>
  <span class="crayon-e">stroke-dasharray</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“100 100”</span>
  <span class="crayon-e">stroke-dashoffset</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“100”</span>
  <span class="crayon-e">transform</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“rotate(-90 18 18)”</span>
  <span class="crayon-r">&gt;</span>
  <span class="crayon-r">&lt;/circle&gt;</span>
  <span class="crayon-r">&lt;/svg&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/a&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/li&gt;</span>
</p>
  <p>
  <span class="crayon-c">&lt;!– additional links to articles –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/ul&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/aside&gt;</span>
  <span class="crayon-c">&lt;!– .cd-read-more –&gt;</span>
</p>
  <p>
  <span class="crayon-r">&lt;/div&gt;</span>
  <span class="crayon-c">&lt;!– .cd-articles –&gt;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Adding style

The `<aside>` element is visible only on big devices (viewport width bigger than 1100px): it has an absolute position and is placed in the top-right corner of the `.cd-articles` element; the class `.fixed` is then used to change its position to fixed so that it's always accessible while the user scrolls through the articles.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-n">@media only screen and (min-width: 1100px) {</span>
</p>
  <p>
  <span class="crayon-n">  .cd-articles {</span>
</p>
  <p>
  <span class="crayon-n">    position: relative;</span>
</p>
  <p>
  <span class="crayon-e">width</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">970px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">padding-right</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">320px</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-read-more </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-c">/<em> hide on mobile </em>/</span>
</p>
  <p>
  <span class="crayon-e">display</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">none</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-n">@media only screen and (min-width: 1100px) {</span>
</p>
  <p>
  <span class="crayon-n">  .cd-read-more {</span>
</p>
  <p>
  <span class="crayon-n">    display: block;</span>
</p>
  <p>
  <span class="crayon-e">width</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">290px</span>
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
  <span class="crayon-i">3em</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">right</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">0</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-k">.cd-read-more.fixed </span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">position</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">fixed</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">right</span>
  <span class="crayon-sy">:</span>
  <span class="crayon-i">calc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">50%</span>
  <span class="crayon-i">-</span>
  <span class="crayon-i">485px</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

To create the progress effect, we used the two svg attributes `stroke-dasharray` and `stroke-dashoffset`. Imagining the circle as a dashed line, the stroke-dasharray lets you specify dashes and gaps length, while the stroke-dashoffset lets you change where the dasharray starts. We initially set `stroke-dasharray="100 100"` and `stroke-dashoffset="100"` (where 100 is the svg circle circumference). This way, the dash and gap are both equal to the circle circumference, and since the stroke-dashoffset is equal to the circle circumference too, only the gap (transparent) is visible. To create the progress effect, we change the stroke-dashoffset from 100 to 0 (more in the Events Handling section).

## Events handling

On big devices (viewport width bigger than 1100px) we bind the `updateArticle()` and `updateSidebarPosition()` functions to the window scroll events: the first one checks which article the user is reading and updates the corresponding svg stroke-dashoffset attribute to show the progress, while the second function updates the sidebar position attribute (using the `.fixed` class ) according to the window scroll top value. Finally, the `changeUrl()` function is used to update the page url according to the article being read.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-t">function</span>
  <span class="crayon-e">updateArticle</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">scrollTop</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">window</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">scrollTop</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">articles</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-st">each</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-t">function</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-c">//articles = $(’.cd-articles’).children(’article’);</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">article</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">articleSidebarLink</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">articleSidebarLinks</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">eq</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">article</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">index</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">children</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘a’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//articleSidebarLinks = $(’.cd-read-more’).find(’li’)</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">articleTop</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">scrollTop</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-c">//articleTop = $(this).offset().top</span>
</p>
  <p>
  <span class="crayon-v">articleSidebarLink</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">removeClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘read reading’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-st">else</span>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">scrollTop</span>
  <span class="crayon-o">&gt;=</span>
  <span class="crayon-v">articleTop</span>
  <span class="crayon-o">&amp;&amp;</span>
  <span class="crayon-v">articleTop</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">articleHeight</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">scrollTop</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-c">//articleHeight = $(this).outerHeight()</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">dashoffsetValue</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">svgCircleLength</span>
  <span class="crayon-o">*</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-o">-</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">scrollTop</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">articleTop</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">articleHeight</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//svgCircleLength = 100</span>
</p>
  <p>
  <span class="crayon-v">articleSidebarLink</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">addClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘reading’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">removeClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘read’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">find</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘circle’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">attr</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-s">‘stroke-dashoffset’</span>
  <span class="crayon-o">:</span>
  <span class="crayon-i">dashoffsetValue</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">changeUrl</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">articleSidebarLink</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">attr</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘href’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-st">else</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">articleSidebarLink</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">removeClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘reading’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">addClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘read’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">function</span>
  <span class="crayon-e">updateSidebarPosition</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-t">var</span>
  <span class="crayon-v">scrollTop</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">window</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">scrollTop</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">scrollTop</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">articlesWrapperTop</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-c">//$(’.cd-articles’).offset().top</span>
</p>
  <p>
  <span class="crayon-v">aside</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">removeClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘fixed’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">attr</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘style’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">’’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//aside = $(’.cd-read-more’)</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-st">else</span>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">scrollTop</span>
  <span class="crayon-o">&gt;=</span>
  <span class="crayon-v">articlesWrapperTop</span>
  <span class="crayon-o">&amp;&amp;</span>
  <span class="crayon-v">scrollTop</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">articlesWrapperTop</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">articlesWrapperHeight</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">windowHeight</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-c">// articlesWrapperHeight = $(’.cd-articles’).outerHeight()</span>
</p>
  <p>
  <span class="crayon-v">aside</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">addClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘fixed’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">attr</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘style’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">’’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-st">else</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">aside</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">hasClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘fixed’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-v">aside</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">removeClass</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘fixed’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">css</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘top’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">articlesWrapperHeight</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">articlePaddingTop</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">windowHeight</span>
  <span class="crayon-o">+</span>
  <span class="crayon-s">‘px’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//articlePaddingTop = Number($(’.cd-articles’).children(’article’).eq(1).css(’padding-top’).replace(’px’, ’’))</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Dec 21, 2015**

- Resource released by CodyHouse
