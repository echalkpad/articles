# Learning jQuery

[Original URL](http://www.learningjquery.com/2015/10/use-jquery-to-create-a-simple-slider)

> Oct 14 2015 All it takes are a few lines of code to make your own simple, lightweight content slider using jQuery – no plugins necessary. First, use divs to separate your content and create each...

<span class="month m-10">Oct</span>

 

<span class="day d-14">14</span>

 

<span class="year y-2015">2015</span>

 All it takes are a few lines of code to make your own simple, lightweight content slider using jQuery – no plugins necessary. First, use divs to separate your content and create each slide. Make sure each of these divs has its own unique ID. Each div should also contain text or an image/icon with its own class that will be used to trigger the jQuery code.<br>

<span class="langName">HTML:</span>

1. <span>&lt;</span>

  div id="slide-1"

  <span>&gt;</span>

2. <span>&lt;</span>

  p

  <span>&gt;</span>

  First Slide

  <span>&lt;</span>

  /p

  <span>&gt;</span>

3. <span>&lt;</span>

  /div

  <span>&gt;</span>

4. <span>&lt;</span>

  div id="slide-2" class="dormant-class"

  <span>&gt;</span>

5. <span>&lt;</span>

  p

  <span>&gt;</span>

  Second Slide

  <span>&lt;</span>

  /p

  <span>&gt;</span>

6. <span>&lt;</span>

  /div

  <span>&gt;</span>

Add these lines to your CSS, and make sure that every slide except the first one is given the class "dormant-class", like in the example above, so that they are initially hidden.<br>

<span class="langName">CSS:</span>

1. .dormant-class

  <span>{</span>

2. <span>display</span>

  <span>:</span>

   

  <span>none</span>

  <span>;</span>

3. 1. .active-class

    <span>{</span>

4. <span>display</span>

  <span>:</span>

   

  <span>block</span>

  <span>;</span>

5.

Here's where the jQuery comes in. When the trigger (#arrow) is clicked, we're going to use jQuery to add the class "dormant class" to the first slide, and remove the class "dormant-class" while adding the class"active-class" to the second slide.<br>
The first slide will disappear as the second slide is displayed. To switch back to the first slide, the #slide-2 will have its own trigger (#arrow2), which will remove "active-class" and add "dormant-class" to the second slide, and remove "dormant class" from the first slide, making it active and visible once again.

[Scripts Used on This Site](http://www.learningjquery.com/scripts-used-on-this-site)
