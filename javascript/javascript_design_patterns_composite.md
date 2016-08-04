# JavaScript Design Patterns: Composite

[Original URL](http://www.joezimjs.com/javascript/javascript-design-patterns-composite/)

> My last post was about the Bridge Design Pattern, which continued the JavaScript Design Patterns series that started off with the Singleton. Today we've moved onto the Composite Pattern....

My last post was about the [Bridge Design Pattern](http://www.joezimjs.com/javascript/javascript-design-patterns-bridge/ "JavaScript Design Patterns: Bridge on joezimjs.com"), which continued the JavaScript Design Patterns series that started off with the [Singleton](http://www.joezimjs.com/javascript/javascript-design-patterns-singleton/ "JavaScript Design Patterns: Singleton on joezimjs.com"). Today we've moved onto the Composite Pattern. Composites are quite useful. By definition of the word "composite", Composites are _composed_ of multiple parts to create one whole entity.

These are the two main benefits that the Composite pattern provides:

You can treat the whole collection of objects the same way you would treat any of the individual objects in the collection. Functions executed on the composite are passed down to each of the children to be executed. On large collections, this becomes very beneficial (though also may be misleading, because you may not realize how large the collection is and therefore not understand how much performance may suffer).

It organizes the objects into a tree structure, and since each composite object contains a method to get its children, you can hide the implementation and organize the children in any way you wish.

## Structure of the composite pattern

In the Composite patterns hierarchy, there are two types of objects: leaf and composite. The image below shows an example of the Composite's structure. It is recursive, which is what gives this pattern its power. The difference between the composite objects and the leaves are that leaves do not have any children, while the very essence of a composite is that it has children.

![Composite Design Pattern Structure](http://www.joezimjs.com/wp-content/uploads/composite_structure.png "Composite Design Pattern Structure")

## Examples of the Composite Pattern

There a number of somewhat common examples of the Composite pattern. If you've ever used a PC, you've more than likely seen a frequently used implementation of this pattern: the file structure. Consider every disk/drive and folder to be a composite object and every file to be a leaf. When you try to delete a folder, it'll not only delete that folder, but also every other folder and file contained within it. You really don't get a much better example than that.

Another example that is a special type of composite is the Binary Tree. If you do not know what that is, you may want to see this [Wikipedia article on the Binary Tree](http://en.wikipedia.org/wiki/Binary_search_tree "Wikipedia article on the Binary Search Tree"). It is special because each node can contain at most 2 children. Also the leaf and composite pieces are exactly the same. Composites represent an end value, just like the leaves and the leaves can become composites at any time by giving them children. It is also optimized to be primarily used to search through sortable data.

If you look around, I'm sure you'll see some more examples. You might even see some implemented using JavaScript.

## Our JavaScript Example of the Composite Pattern

To demonstrate the Composite pattern to you using JavaScript, I will use neither of the above examples. Instead I will make an image gallery. It is actually quite similar to the file system example, except that this in no way needs to reflect the place in which the images are stored or organized on the drive and is really only intended to have a certain number of levels. See the image below:

![Gallery Example's Structure](http://www.joezimjs.com/wp-content/uploads/gallery_structure.png "Gallery Example's Structure")

Notice that in my image, all of the images are contained within composites on the "Gallery" level. This is by no means necessary, nor will it be enforced in the code, but it is ideally how the images are to be organized.

Each of the objects in the hierarchy needs to implement a certain interface. Since JavaScript does not have interfaces we just need to make sure we implement certain methods. Below is the list of methods and what they do:

Composite-Specific Methods `add` add a child node to this composite `remove` remove a child node from this composite (or one in a deeper level) `getChild` returns a child object Gallery-Specific Methods `hide` hides the composite and all of its children `show` shows the composite and all of its children Helper Methods `getElement` get the HTML element of the node First I'll show the JavaScript for implementing GalleryComposite using this interface. It may be worth noting that I will being using the [jQuery JavaScript library](http://jquery.com "jQuery JavaScript Library") to help me out with some of the DOM work.

1. <span class="kw2">var</span>

   GalleryComposite = 

  <span class="kw2">function</span>

   

  <span class="br0">(</span>

  heading, id

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw1">this</span>

  .

  <span class="me1">children</span>

   = 

  <span class="br0">[</span>

  <span class="br0">]</span>

  ;

3. <span class="kw1">this</span>

  .

  <span class="me1">element</span>

   = $

  <span class="br0">(</span>

  <span class="st0">‘&lt;div id="’</span>

   + id + 

  <span class="st0">‘" class=“composite-gallery”&gt;&lt;/div&gt;’</span>

  <span class="br0">)</span>

4. .

  <span class="me1">append</span>

  <span class="br0">(</span>

  <span class="st0">‘&lt;h2&gt;’</span>

   + heading + 

  <span class="st0">‘&lt;/h2&gt;’</span>

  <span class="br0">)</span>

  ;

5. 1. GalleryComposite.

    <span class="me1">prototype</span>

     = 

    <span class="br0">{</span>

6. add: 

  <span class="kw2">function</span>

   

  <span class="br0">(</span>

  child

  <span class="br0">)</span>

   

  <span class="br0">{</span>

7. <span class="kw1">this</span>

  .

  <span class="me1">children</span>

  .

  <span class="me1">push</span>

  <span class="br0">(</span>

  child

  <span class="br0">)</span>

  ;

8. <span class="kw1">this</span>

  .

  <span class="me1">element</span>

  .

  <span class="me1">append</span>

  <span class="br0">(</span>

  child.

  <span class="me1">getElement</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  <span class="br0">)</span>

  ;

9. 1. remove: 

    <span class="kw2">function</span>

     

    <span class="br0">(</span>

    child

    <span class="br0">)</span>

     

    <span class="br0">{</span>

10. <span class="kw1">for</span>

   

  <span class="br0">(</span>

  <span class="kw2">var</span>

   node, i = 

  <span class="nu0">0</span>

  ; node = 

  <span class="kw1">this</span>

  .

  <span class="me1">getChild</span>

  <span class="br0">(</span>

  i

  <span class="br0">)</span>

  ; i++

  <span class="br0">)</span>

   

  <span class="br0">{</span>

11. <span class="kw1">if</span>

   

  <span class="br0">(</span>

  node == child

  <span class="br0">)</span>

   

  <span class="br0">{</span>

12. <span class="kw1">this</span>

  .

  <span class="me1">children</span>

  .

  <span class="me1">splice</span>

  <span class="br0">(</span>

  i, 

  <span class="nu0">1</span>

  <span class="br0">)</span>

  ;

13. <span class="kw1">this</span>

  .

  <span class="me1">element</span>

  .

  <span class="me1">detach</span>

  <span class="br0">(</span>

  child.

  <span class="me1">getElement</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  <span class="br0">)</span>

  ;

14. <span class="kw1">return</span>

   

  <span class="kw2">true</span>

  ;

15. 1. <span class="kw1">if</span>

     

    <span class="br0">(</span>

    node.

    <span class="me1">remove</span>

    <span class="br0">(</span>

    child

    <span class="br0">)</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

16. <span class="kw1">return</span>

   

  <span class="kw2">true</span>

  ;

17. 1. 1. <span class="kw1">return</span>

       

      <span class="kw2">false</span>

      ;

18. 1. getChild: 

    <span class="kw2">function</span>

     

    <span class="br0">(</span>

    i

    <span class="br0">)</span>

     

    <span class="br0">{</span>

19. <span class="kw1">return</span>

   

  <span class="kw1">this</span>

  .

  <span class="me1">children</span>

  <span class="br0">[</span>

  i

  <span class="br0">]</span>

  ;

20. 1. hide: 

    <span class="kw2">function</span>

     

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

21. <span class="kw1">for</span>

   

  <span class="br0">(</span>

  <span class="kw2">var</span>

   node, i = 

  <span class="nu0">0</span>

  ; node = 

  <span class="kw1">this</span>

  .

  <span class="me1">getChild</span>

  <span class="br0">(</span>

  i

  <span class="br0">)</span>

  ; i++

  <span class="br0">)</span>

   

  <span class="br0">{</span>

22. node.

  <span class="me1">hide</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ;

23. 1. <span class="kw1">this</span>

    .

    <span class="me1">element</span>

    .

    <span class="me1">hide</span>

    <span class="br0">(</span>

    <span class="nu0">0</span>

    <span class="br0">)</span>

    ;

24. 1. show: 

    <span class="kw2">function</span>

     

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

25. <span class="kw1">for</span>

   

  <span class="br0">(</span>

  <span class="kw2">var</span>

   node, i = 

  <span class="nu0">0</span>

  ; node = 

  <span class="kw1">this</span>

  .

  <span class="me1">getChild</span>

  <span class="br0">(</span>

  i

  <span class="br0">)</span>

  ; i++

  <span class="br0">)</span>

   

  <span class="br0">{</span>

26. node.

  <span class="me1">show</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ;

27. 1. <span class="kw1">this</span>

    .

    <span class="me1">element</span>

    .

    <span class="me1">show</span>

    <span class="br0">(</span>

    <span class="nu0">0</span>

    <span class="br0">)</span>

    ;

28. 1. getElement: 

    <span class="kw2">function</span>

     

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

29. <span class="kw1">return</span>

   

  <span class="kw1">this</span>

  .

  <span class="me1">element</span>

  ;

30. 1.

Next we'll implement the images using GalleryImage:

1. <span class="kw2">var</span>

   GalleryImage = 

  <span class="kw2">function</span>

   

  <span class="br0">(</span>

  src, id

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw1">this</span>

  .

  <span class="me1">children</span>

   = 

  <span class="br0">[</span>

  <span class="br0">]</span>

  ;

3. <span class="kw1">this</span>

  .

  <span class="me1">element</span>

   = $

  <span class="br0">(</span>

  <span class="st0">‘&lt;img /&gt;’</span>

  <span class="br0">)</span>

4. .

  <span class="me1">attr</span>

  <span class="br0">(</span>

  <span class="st0">‘id’</span>

  , id

  <span class="br0">)</span>

5. .

  <span class="me1">attr</span>

  <span class="br0">(</span>

  <span class="st0">‘src’</span>

  , src

  <span class="br0">)</span>

  ;

6. 1. GalleryImage.

    <span class="me1">prototype</span>

     = 

    <span class="br0">{</span>

7. <span class="co1">// Due to this being a leaf, it doesn’t use these methods,</span>

8. <span class="co1">// but must implement them to count as implementing the</span>

9. <span class="co1">// Composite interface</span>

10. add: 

  <span class="kw2">function</span>

   

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

   

  <span class="br0">}</span>

  ,

11. remove: 

  <span class="kw2">function</span>

   

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

   

  <span class="br0">}</span>

  ,

12. getChild: 

  <span class="kw2">function</span>

   

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

   

  <span class="br0">}</span>

  ,

13. hide: 

  <span class="kw2">function</span>

   

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

14. <span class="kw1">this</span>

  .

  <span class="me1">element</span>

  .

  <span class="me1">hide</span>

  <span class="br0">(</span>

  <span class="nu0">0</span>

  <span class="br0">)</span>

  ;

15. 1. show: 

    <span class="kw2">function</span>

     

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

16. <span class="kw1">this</span>

  .

  <span class="me1">element</span>

  .

  <span class="me1">show</span>

  <span class="br0">(</span>

  <span class="nu0">0</span>

  <span class="br0">)</span>

  ;

17. 1. getElement: 

    <span class="kw2">function</span>

     

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

18. <span class="kw1">return</span>

   

  <span class="kw1">this</span>

  .

  <span class="me1">element</span>

  ;

19. 1.

Notice that the GalleryImage class doesn't do anything in the `add`, `remove`, and `getChild` functions. Since it is the leaf class, it doesn't contain any children, so it doesn't do anything for these methods. However, we do need to include those functions in order to comply with the Interface we set up. After all, the composite objects don't know that it's a leaf and might try calling those methods.

Now that the classes are set up, we'll write a bit of JavaScript code that utilizes those classes to create and show a real gallery. Though this example only shows the use of three of the methods shown above, you could extend this code to create a dynamic gallery, where users could add and remove images and folders easily.

1. <span class="kw2">var</span>

   container = 

  <span class="kw2">new</span>

   GalleryComposite

  <span class="br0">(</span>

  <span class="st0">’’</span>

  , 

  <span class="st0">‘allgalleries’</span>

  <span class="br0">)</span>

  ;

2. <span class="kw2">var</span>

   gallery1 = 

  <span class="kw2">new</span>

   GalleryComposite

  <span class="br0">(</span>

  <span class="st0">‘Gallery 1’</span>

  , 

  <span class="st0">‘gallery1’</span>

  <span class="br0">)</span>

  ;

3. <span class="kw2">var</span>

   gallery2 = 

  <span class="kw2">new</span>

   GalleryComposite

  <span class="br0">(</span>

  <span class="st0">‘Gallery 2’</span>

  , 

  <span class="st0">‘gallery2’</span>

  <span class="br0">)</span>

  ;

4. <span class="kw2">var</span>

   image1 = 

  <span class="kw2">new</span>

   GalleryImage

  <span class="br0">(</span>

  <span class="st0">‘image1.jpg’</span>

  , 

  <span class="st0">‘img1’</span>

  <span class="br0">)</span>

  ;

5. <span class="kw2">var</span>

   image2 = 

  <span class="kw2">new</span>

   GalleryImage

  <span class="br0">(</span>

  <span class="st0">‘image2.jpg’</span>

  , 

  <span class="st0">‘img2’</span>

  <span class="br0">)</span>

  ;

6. <span class="kw2">var</span>

   image3 = 

  <span class="kw2">new</span>

   GalleryImage

  <span class="br0">(</span>

  <span class="st0">‘image3.jpg’</span>

  , 

  <span class="st0">‘img3’</span>

  <span class="br0">)</span>

  ;

7. <span class="kw2">var</span>

   image4 = 

  <span class="kw2">new</span>

   GalleryImage

  <span class="br0">(</span>

  <span class="st0">‘image4.jpg’</span>

  , 

  <span class="st0">‘img4’</span>

  <span class="br0">)</span>

  ;

8. gallery1.

  <span class="me1">add</span>

  <span class="br0">(</span>

  image1

  <span class="br0">)</span>

  ;

9. gallery1.

  <span class="me1">add</span>

  <span class="br0">(</span>

  image2

  <span class="br0">)</span>

  ;

10. gallery2.

  <span class="me1">add</span>

  <span class="br0">(</span>

  image3

  <span class="br0">)</span>

  ;

11. gallery2.

  <span class="me1">add</span>

  <span class="br0">(</span>

  image4

  <span class="br0">)</span>

  ;

12. container.

  <span class="me1">add</span>

  <span class="br0">(</span>

  gallery1

  <span class="br0">)</span>

  ;

13. container.

  <span class="me1">add</span>

  <span class="br0">(</span>

  gallery2

  <span class="br0">)</span>

  ;

14. <span class="co1">// Make sure to add the top container to the body, </span>

15. <span class="co1">// otherwise it’ll never show up.</span>

16. container.

  <span class="me1">getElement</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  .

  <span class="me1">appendTo</span>

  <span class="br0">(</span>

  <span class="st0">‘body’</span>

  <span class="br0">)</span>

  ;

17. container.

  <span class="me1">show</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ;

You can demo the code on the [Composite Pattern Demo Page](http://www.joezimjs.com/composite-pattern-demo-page/ "Composite Pattern Demo Page"). You can view the source code of that page to see that none of the albums or images were premade using HTML, but were all added to the DOM via our JavaScript code.

## Benefits and Drawbacks of the Composite Pattern

You can see the amazing benefits of always being able to just call a function on a top level object and have the results happen to any or all of the nodes in the composite structure. The code becomes much easier to use. Also the objects in the composite are loosely coupled because they all just follow the same interface. Finally, the composite gives a nice structure to the objects, rather than keeping them all in separate variables or in an array.

However, as mentioned earlier, the composite pattern can be deceptive. Making a call to a single function may be so easy that you might not realize the adverse effect it'll have on performance if the composite grows rather large.

The Composite pattern is a powerful tool, but as many know, "with great power comes great responsibility." Use JavaScript's Composite pattern wisely and you'll be one step closer to becoming a JavaScript guru. If you thought this was helpful or you just plain liked the article, please spread the word using the social sharing buttons below the post. Thanks!

JavaScript Design Patterns series:

1. [JavaScript Design Patterns: Bridge](http://www.joezimjs.com/javascript/javascript-design-patterns-bridge/ "JavaScript Design Patterns: Bridge")
2. [JavaScript Design Patterns: Singleton](http://www.joezimjs.com/javascript/javascript-design-patterns-singleton/ "JavaScript Design Patterns: Singleton")
