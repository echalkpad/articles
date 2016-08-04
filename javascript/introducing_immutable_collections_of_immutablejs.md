# Introducing immutable collections of ImmutableJS

[Original URL](http://webuilddesign.com/introducing-immutable-collections-of-immutablejs/)

> ImmutableJS is a library that allows us to manage different collections and add immutable qualities. It was created by Facebook in 2013 and is complemented perfectly with...

[ImmutableJS](https://facebook.github.io/immutable-js/) is a library that allows us to manage different collections and add **immutable qualities**. It was created by Facebook in 2013 and is complemented perfectly with [ReactJS](http://webuilddesign.com/resources-and-tutorials-for-getting-started-with-programming-reactive/) flux architecture and the way they work with states and properties.

## What is an immutable object?

An [immutable object](https://en.wikipedia.org/wiki/Immutable_object) is basically one that **after being created never changes.** In Javascript objects are mutable by default. Example:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">person</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">name</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘Jeison’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">name</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘Higuita’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-i">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">person</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//Object {name: “Jeison” name “Higuita”}</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">person2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-i">person</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-i">person2</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">age</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">22</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-i">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">person2</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//Object {name: “Jeison” name “Higuita”, age: 22}</span>
</p>
  <p>
  <span class="crayon-i">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">person</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//Object {name: “Jeison” name “Higuita”, age: 22}</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

As you can see, to make a change to an object ( **person2**) reference (changed **person**). Working with immutable objects brings benefits such as:

- When you are working with multiple threads or asynchronous processes, you don't have to worry about the state as this will never change
- <span id="ouHighlight__7_9TO0_3">More</span>

  <span id="ouHighlight__11_17TO5_11">natural</span>

  <span id="ouHighlight__0_5TO13_22">management</span>

  <span id="ouHighlight__19_20TO24_25">of</span>

  <span id="ouHighlight__34_37TO27_30">Maps</span>

  <span id="ouHighlight__39_39TO32_34">and</span>

  <span id="ouHighlight__41_43TO36_38">Set</span>

  <span id="ouHighlight__22_32TO40_50">Collections</span>

- It avoids conflict between objects
- It changes in your data are more explicit
- It allows you to write queries or other simple data management algorithms.

Besides, when you are working with ImmutableJS you will get following advantages:

- Implemented collections optimized for Javascript
- Interaction with objects defined in the Javascript API
- It works with nested collections

## Using Immutable?

To use Immutable first we have to install it with [NPM](https://www.npmjs.com/):

 | <div class="crayon-pre">
  <p>
  <span class="crayon-i">npm</span>
  <span class="crayon-i">i</span>
  <span class="crayon-v">immutable</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">D</span>
</p>
  <p>
  <span class="crayon-e">import </span>
  <span class="crayon-e">Immutable </span>
  <span class="crayon-i">from</span>
  <span class="crayon-s">‘immutable’</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">persona</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">Immutable</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Map</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">nombre</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘Jeison’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">apellido</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘Higuita’</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">persona</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">toJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//Object {nombre: “Jeison”, apellido: “Higuita”}</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">persona2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">persona</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">set</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘apellido’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘Sanchez’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">persona2</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">toJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//Object {nombre: “Jeison”, apellido: “Sanchez”}</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">personato</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">JS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//Object {nombre: “Jeison”, apellido: “Higuita”}</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

As you can see, when you create the new object ( **person2** ) the referencing object does not change (**person**).

## Immutable Collections

Immutable has several collections. Here are some most useful ones:

### Maps

A map is basically an object which consists of key/value pairs:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">persona</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">Immutable</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Map</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">nombre</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘Jeison’</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-v">apellido</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">‘HIguita’</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">//Metodos</span>
</p>
  <p>
  <span class="crayon-c">//set()</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">persona2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">persona1</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">set</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘apellido’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘Sanchez’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">//get()</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">persona2</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">get</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘apellido’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">//delete()</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">persona3</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">persona2</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">delete</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘apellido’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">//merge()</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">persona4</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">persona1</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">merge</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">persona2</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">// persona4 = Object {nombre: “Jeison”, apellido: “Sanchez”}</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">persona5</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">persona2</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">merge</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">persona1</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">// persona4 = Object {nombre: “Jeison”, apellido: “Higuita”}</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<span id="ouHighlight__8_13TO0_1">It</span>

<span id="ouHighlight__0_6TO3_6">also</span>

<span id="ouHighlight__8_13TO8_10">has</span>

<span id="ouHighlight__19_25TO12_18">methods</span>

<span id="ouHighlight__27_30TO20_26">such as</span>

**<span id="ouHighlight__32_40TO28_36">mergeDeep</span>**

<span id="ouHighlight__42_42TO38_40">and</span>

**<span id="ouHighlight__44_52TO42_50">mergeWith</span>**

<span id="ouHighlight__54_56TO52_56">which</span>

<span id="ouHighlight__62_70TO58_67">suitable for working with</span>

<span id="ouHighlight__80_84TO78_81">both </span>

<span id="ouHighlight__142_149TO100_105">nested</span>

<span id="ouHighlight__130_140TO107_117">collections</span>

<span id="ouHighlight__107_110TO119_121">and</span>

<span id="ouHighlight__91_105TO123_132">iterations</span>

<span id="noHighlight_0.4172795698978007">.</span>

**MergeDeep** basically allows us to gain access to our collections modify data.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-c">//mergeDeep</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">mapa</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">Immutable</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">fromJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">a</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">x</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">10</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">y</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">10</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">b</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">x</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">20</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">y</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">50</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">mapa2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">Immutable</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">fromJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">a</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">x</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">2</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">b</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">y</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">5</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">c</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">z</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">3</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">mapa</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">mergeDeep</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">mapa2</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c">// {a: { x: 2, y: 10 }, b: { x: 20, y: 5 }, c: { z: 3 } }</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**MergeWith** allows us not only to merge to maps but returns us iterable object.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-c">//mergewith</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">mapa</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">Immutable</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Map</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">a</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">10</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">b</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">20</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">c</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">30</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">mapa2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">Immutable</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Map</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">b</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">40</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">a</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">50</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">d</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">60</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">mapa</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">mergeWith</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">prev</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">next</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">prev</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">next</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">mapa2</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c">// { a: 0.2, b: 0.5, c: 30, d: 60 }</span>
</p>
  <p>
  <span class="crayon-v">mapa2</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">mergeWith</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">prev</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">next</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">=</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">prev</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">next</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">mapa</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c">// { b: 2, a: 5, d: 60, c: 30 }</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

You can check the methods mentioned above and more Immutable API methods in its [documentation](https://facebook.github.io/immutable-js/docs/#/Map).

### List

It is **the representation of an array in JavaScript**. However, when it is altered, the sources never changes. Similarly to Maps, this collection also has methods for nested collections.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">qwerty</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">Immutable</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">List</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">‘q’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘w’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘e’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘r’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘t’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘y’</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">qwerty</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">size</span>
  <span class="crayon-c">// 6</span>
</p>
  <p>
  <span class="crayon-c">//push</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">qwertyu</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">qwerty</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">push</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘u’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">//pop</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">qwert</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">qwertyu</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">pop</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">pop</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">//shift</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">wertArray</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">qwert</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">shift</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">toJSON</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">// [“w”, “e”, “r”, “t”]</span>
</p>
  <p>
  <span class="crayon-c">//concat</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">qwertyuiArray</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">qwert</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">concat</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">‘y’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘u’</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">‘i’</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">toJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">// [“q”, “w”, “e”, “r”, “t”, “y”, “u”, “i”]</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Set

The Set collection is an array that does not allow duplicate values.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">set</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">Immutable</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Set</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">11</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">2</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">4</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">5</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">4</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">8</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">set</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">toJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-c">//[11, 2, 4, 5, 8]</span>
</p>
  <p>
  <span class="crayon-m">const</span>
  <span class="crayon-v">set2</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">Immutable</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Set</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">22</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">2</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">8</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">7</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">set2</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">toJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">//union()</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">set</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">union</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">set2</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">toJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c">//[11, 2, 4, 5, 8, 22, 7]</span>
</p>
  <p>
  <span class="crayon-c">//intersect()</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">set</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">intersect</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">set2</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">toJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c">//[8, 2]</span>
</p>
  <p>
  <span class="crayon-c">//subtract()</span>
</p>
  <p>
  <span class="crayon-v">console</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">log</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">set</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">subtract</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">set2</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">toJS</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c">//[11, 5, 4]</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Immutable has many more collections that you may find useful when it comes to managing your data efficiently. So learn more about it in [ImmutableJS](https://facebook.github.io/immutable-js/) and share your thoughts with us in the comment below.
