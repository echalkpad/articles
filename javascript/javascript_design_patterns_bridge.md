# JavaScript Design Patterns: Bridge

[Original URL](http://www.joezimjs.com/javascript/javascript-design-patterns-bridge/)

> In my last blog post article I discussed the Singleton Design Pattern and how it is used in JavaScript. This time around the Bridge design pattern is poking its head up and asking for a bit of...

In my last blog post article I discussed the [Singleton Design Pattern](http://www.joezimjs.com/javascript/javascript-design-patterns-singleton/ "JavaScript Design Patterns: Singleton") and how it is used in JavaScript. This time around the Bridge design pattern is poking its head up and asking for a bit of attention, making it the second in the JavaScript Design Patterns series.

Every place I've read about the Bridge pattern likes to quote the Gang of Four directly in their description of what the Bridge pattern is, so I figure why not me too? The Bridge pattern should "decouple an abstraction from its implementation so that the two can vary independently." Bridges are quite beneficial in event-driven applications, which are quite common in JavaScript. Considering that fact, it's surprisingly one of the most underused design patterns.

## Event Listeners Example

In the following examples, I'll be using a bit of [jQuery](http://jquery.com "Official jQuery Home"), so if you don't know what a function does or how it works, you can look at their [documentation](http://docs.jquery.com "jQuery Documentation").

Below you'll see a small bit of code using an API method called `getXById`, which is badly implemented. We are using a click event to determine which element to pull the id from. The callback itself is `getXById`, which gets the id from the clicked element and uses AJAX to get X from the server using the Id it found.

1. getXById = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw2">var</span>

   id = 

  <span class="kw1">this</span>

  .

  <span class="me1">id</span>

  ;

3. $.

  <span class="me1">ajax</span>

  <span class="br0">(</span>

  <span class="br0">{</span>

4. url:

  <span class="st0">‘/getx?id=’</span>

   + id,

5. success: 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  response

  <span class="br0">)</span>

   

  <span class="br0">{</span>

6. console.

  <span class="me1">log</span>

  <span class="br0">(</span>

  response

  <span class="br0">)</span>

  ;

7. 1. <span class="br0">}</span>

    <span class="br0">)</span>

    ;

8. 1. $

    <span class="br0">(</span>

    <span class="st0">‘someElement’</span>

    <span class="br0">)</span>

    .

    <span class="me1">bind</span>

    <span class="br0">(</span>

    <span class="st0">‘click’</span>

    , getXById

    <span class="br0">)</span>

    ;

This code isn't all that bad if it's only meant to be used that one specific way on the one specific page, but it's (supposedly) part of an API, so this needs to be changed a lot. Let's decouple `getXById` from the event listener and the implementation of what is done with the result:

1. getXById = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  id, callback

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. $.

  <span class="me1">ajax</span>

  <span class="br0">(</span>

  <span class="br0">{</span>

3. url:

  <span class="st0">‘/getx?id=’</span>

   + id,

4. success: callback

5. 1. 1. getXBridge = 

      <span class="kw2">function</span>

      <span class="br0">(</span>

      <span class="br0">)</span>

       

      <span class="br0">{</span>

6. <span class="kw2">var</span>

   id = 

  <span class="kw1">this</span>

  .

  <span class="me1">id</span>

  ;

7. getXById

  <span class="br0">(</span>

  <span class="kw1">this</span>

  .

  <span class="me1">id</span>

  , 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

8. console.

  <span class="me1">log</span>

  <span class="br0">(</span>

  response

  <span class="br0">)</span>

  ;

9. <span class="br0">}</span>

  <span class="br0">)</span>

  ;

10. 1. $

    <span class="br0">(</span>

    <span class="st0">‘someElement’</span>

    <span class="br0">)</span>

    .

    <span class="me1">bind</span>

    <span class="br0">(</span>

    <span class="st0">‘click’</span>

    , getXBridge

    <span class="br0">)</span>

    ;

Now the `getXById` can be used just about anywhere and you can do anything with X now.

## Classical Example

What I mean by "classical" is two-fold: the example is more common to classic object-oriented programming languages and it is uses classes. Technically JavaScript doesn't have classes but you mimic interfaces and use prototypes to mimic classes. Originally this example was in the book [Head First Design Patterns](http://www.amazon.com/First-Design-Patterns-Elisabeth-Freeman/dp/0596007124 "Head First Design Patterns on Amazon"), which uses Java in their example. However, this is one the minor patterns in the back of the book that didn't actually have a code example, so I'm really just using the diagrams (except I recreated them because I'm awesome).

### Our Starting Product

![Original Remote Control Diagram](http://www.joezimjs.com/wp-content/uploads/original_remote.png "Original Remote Control Diagram: Bridge Pattern")We start with the `RemoteControl` Interface. `ToshibaRemote` and `SonyRemote` both implement that interface to work with their respective televisions. With this code, you can call `on()`, `off()`, or `setChannel()` on any remote and even though all the TVs are different, it'll work. What happens though, when you want to make improvements on the remotes? That's where the Bridge pattern comes in:

![Bridge Remote Control Diagram](http://www.joezimjs.com/wp-content/uploads/bridge_remote.png "Bridge Remote Control Diagram: Bridge Patten")

Now, since the TVs adhere to an interface and all of the remotes adhere to another interface – actually just a class because it only needs the one implementation – we can create variations to either on through inheritance and still be compatible. Wanna see some code? I'll show you the code for the new solution with the Bridge pattern, but I don't think you need to see the code for the original. I really don't think many of you need to see any code at all, but I'm sure there are those out there who'd like to see it anyway. We're programmers, right? Show us the code!

1. <span class="kw2">var</span>

   RemoteControl = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  tv

  <span class="br0">)</span>

   

  <span class="br0">{</span>

2. <span class="kw1">this</span>

  .

  <span class="me1">tv</span>

   = tv;

3. <span class="kw1">this</span>

  .

  <span class="me1">on</span>

   = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

4. <span class="kw1">this</span>

  .

  <span class="me1">tv</span>

  .

  <span class="me1">on</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ;

5. 1. <span class="kw1">this</span>

    .

    <span class="me1">off</span>

     = 

    <span class="kw2">function</span>

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

6. <span class="kw1">this</span>

  .

  <span class="me1">tv</span>

  .

  <span class="me1">off</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ;

7. 1. <span class="kw1">this</span>

    .

    <span class="me1">setChannel</span>

     = 

    <span class="kw2">function</span>

    <span class="br0">(</span>

    ch

    <span class="br0">)</span>

     

    <span class="br0">{</span>

8. <span class="kw1">this</span>

  .

  <span class="me1">tv</span>

  .

  <span class="me1">tuneChannel</span>

  <span class="br0">(</span>

  ch

  <span class="br0">)</span>

  ;

9. 1. 1. <span class="coMULTI">/* Newer, Better Remote Control */</span>

10. <span class="kw2">var</span>

   PowerRemote = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  tv

  <span class="br0">)</span>

   

  <span class="br0">{</span>

11. <span class="kw1">this</span>

  .

  <span class="me1">tv</span>

   = tv;

12. <span class="kw1">this</span>

  .

  <span class="me1">currChannel</span>

   = 

  <span class="nu0">0</span>

  ;

13. <span class="kw1">this</span>

  .

  <span class="me1">setChannel</span>

   = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  ch

  <span class="br0">)</span>

   

  <span class="br0">{</span>

14. <span class="kw1">this</span>

  .

  <span class="me1">currChannel</span>

   = ch;

15. <span class="kw1">this</span>

  .

  <span class="me1">tv</span>

  .

  <span class="me1">tuneChannel</span>

  <span class="br0">(</span>

  ch

  <span class="br0">)</span>

  ;

16. 1. <span class="kw1">this</span>

    .

    <span class="me1">nextChannel</span>

     = 

    <span class="kw2">function</span>

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

17. <span class="kw1">this</span>

  .

  <span class="me1">setChannel</span>

  <span class="br0">(</span>

  <span class="kw1">this</span>

  .

  <span class="me1">currChannel</span>

   + 

  <span class="nu0">1</span>

  <span class="br0">)</span>

  ;

18. 1. <span class="kw1">this</span>

    .

    <span class="me1">prevChannel</span>

     = 

    <span class="kw2">function</span>

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

19. <span class="kw1">this</span>

  .

  <span class="me1">setChannel</span>

  <span class="br0">(</span>

  <span class="kw1">this</span>

  .

  <span class="me1">currChannel</span>

   – 

  <span class="nu0">1</span>

  <span class="br0">)</span>

  ;

20. 1. 1. PowerRemote.

      <span class="me1">prototype</span>

       = 

      <span class="kw2">new</span>

       RemoteControl

      <span class="br0">(</span>

      <span class="br0">)</span>

      ;

21. <span class="coMULTI">/** TV Interface</span>

22. <span class="coMULTI"> Since there are no Interfaces in JavaScript I’m just</span>

23. <span class="coMULTI"> going to use comments to define what the implementors</span>

24. <span class="coMULTI"> should implement</span>

25. 1. <span class="coMULTI"> function on</span>

26. <span class="coMULTI"> function off</span>

27. <span class="coMULTI"> function tuneChannel(channel)</span>

28. 1. <span class="coMULTI">/* Sony TV */</span>

29. <span class="kw2">var</span>

   SonyTV = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

30. <span class="kw1">this</span>

  .

  <span class="me1">on</span>

   = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

31. console.

  <span class="me1">log</span>

  <span class="br0">(</span>

  <span class="st0">‘Sony TV is on’</span>

  <span class="br0">)</span>

  ;

32. 1. <span class="kw1">this</span>

    .

    <span class="me1">off</span>

     = 

    <span class="kw2">function</span>

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

33. console.

  <span class="me1">log</span>

  <span class="br0">(</span>

  <span class="st0">‘Sony TV is off’</span>

  <span class="br0">)</span>

  ;

34. 1. <span class="kw1">this</span>

    .

    <span class="me1">tuneChannel</span>

     = 

    <span class="kw2">function</span>

    <span class="br0">(</span>

    ch

    <span class="br0">)</span>

     

    <span class="br0">{</span>

35. console.

  <span class="me1">log</span>

  <span class="br0">(</span>

  <span class="st0">‘Sony TV tuned to channel ’</span>

   + ch

  <span class="br0">)</span>

  ;

36. 1. 1. <span class="coMULTI">/* Toshiba TV */</span>

37. <span class="kw2">var</span>

   ToshibaTV = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

38. <span class="kw1">this</span>

  .

  <span class="me1">on</span>

   = 

  <span class="kw2">function</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

   

  <span class="br0">{</span>

39. console.

  <span class="me1">log</span>

  <span class="br0">(</span>

  <span class="st0">‘Welcome to Toshiba entertainment’</span>

  <span class="br0">)</span>

  ;

40. 1. <span class="kw1">this</span>

    .

    <span class="me1">off</span>

     = 

    <span class="kw2">function</span>

    <span class="br0">(</span>

    <span class="br0">)</span>

     

    <span class="br0">{</span>

41. console.

  <span class="me1">log</span>

  <span class="br0">(</span>

  <span class="st0">‘Goodbye Toshiba user’</span>

  <span class="br0">)</span>

  ;

42. 1. <span class="kw1">this</span>

    .

    <span class="me1">tuneChannel</span>

     = 

    <span class="kw2">function</span>

    <span class="br0">(</span>

    ch

    <span class="br0">)</span>

     

    <span class="br0">{</span>

43. console.

  <span class="me1">log</span>

  <span class="br0">(</span>

  <span class="st0">‘Channel ’</span>

   + ch + 

  <span class="st0">‘ is set on your Toshiba television’</span>

  <span class="br0">)</span>

  ;

44. 1. 1. <span class="coMULTI">/* Let’s see it in action */</span>

45. <span class="kw2">var</span>

   sony = 

  <span class="kw2">new</span>

   SonyTV

  <span class="br0">(</span>

  <span class="br0">)</span>

  ,

46. toshiba = 

  <span class="kw2">new</span>

   ToshibaTV

  <span class="br0">(</span>

  <span class="br0">)</span>

  ,

47. std_remote = 

  <span class="kw2">new</span>

   RemoteControl

  <span class="br0">(</span>

  sony

  <span class="br0">)</span>

  ,

48. pwr_remote = 

  <span class="kw2">new</span>

   PowerRemote

  <span class="br0">(</span>

  toshiba

  <span class="br0">)</span>

  ;

49. std_remote.

  <span class="me1">on</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ; 

  <span class="co1">// prints “Sony TV is on”</span>

50. std_remote.

  <span class="me1">setChannel</span>

  <span class="br0">(</span>

  <span class="nu0">55</span>

  <span class="br0">)</span>

  ; 

  <span class="co1">// prints “Sony TV tuned to channel 55”</span>

51. std_remote.

  <span class="me1">setChannel</span>

  <span class="br0">(</span>

  <span class="nu0">20</span>

  <span class="br0">)</span>

  ; 

  <span class="co1">// prints “Sony TV tuned to channel 20”</span>

52. std_remote.

  <span class="me1">off</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ; 

  <span class="co1">// prints “Sony TV is off”</span>

53. pwr_remote.

  <span class="me1">on</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ; 

  <span class="co1">// prints “Welcome to Toshiba entertainment”</span>

54. pwr_remote.

  <span class="me1">setChannel</span>

  <span class="br0">(</span>

  <span class="nu0">55</span>

  <span class="br0">)</span>

  ; 

  <span class="co1">// prints “Channel 55 is set on your Toshiba television”</span>

55. pwr_remote.

  <span class="me1">nextChannel</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ; 

  <span class="co1">// prints “Channel 56 is set on your Toshiba television”</span>

56. pwr_remote.

  <span class="me1">prevChannel</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ; 

  <span class="co1">// prints “Channel 55 is set on your Toshiba television”</span>

57. pwr_remote.

  <span class="me1">off</span>

  <span class="br0">(</span>

  <span class="br0">)</span>

  ; 

  <span class="co1">// prints “Goodbye Toshiba user”</span>

Well, that just about wraps things up for the Bridge pattern in JavaScript. If you haven't yet, make sure you go back and read about the [Singleton Pattern](http://www.joezimjs.com/javascript/javascript-design-patterns-singleton/ "JavaScript Design Patterns: Singleton"). Also keep an eye out for the next article in this series: the Composite Pattern. If you thought this was helpful or you just plain liked the article, please spread the word using the social sharing buttons below the post. Thanks!

JavaScript Design Patterns series:

1. [JavaScript Design Patterns: Singleton](http://www.joezimjs.com/javascript/javascript-design-patterns-singleton/ "JavaScript Design Patterns: Singleton")
