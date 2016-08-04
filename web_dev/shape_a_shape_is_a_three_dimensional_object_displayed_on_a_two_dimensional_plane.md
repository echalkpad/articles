# Shape A shape is a three dimensional object displayed on a two dimensional plane

[Original URL](http://semantic-ui.com/modules/shape.html#/definition)

> A shape is a three dimensional object including any flat content as a side. A cube shape is formatted so that each side is the face of a cube A text shape is formatted to allow for sides of text...

** 

* A shape is a three dimensional object including any flat content as a side.
* A cube shape is formatted so that each side is the face of a cube
* A text shape is formatted to allow for sides of text to be displayed
* Shapes do not have to be regular or have its sides match up in length and width to animate correctly.
* Shapes can have any arbitrary content, just wrap each side in `side`
* Animations use CSS3 transitions and Javascript to set-up the correct conditions.

Initializing a shape

    $('.shape').shape();

Transitions automatically assume next side is the next sibling (or first if last element)

    $('.shape').shape('flip up');

To manually set the next side to appear use a selector or jQuery object

    $('.shape') .shape('set next side', '.second.side') .shape('flip up') ;

Any internal method can be invoked programmatically

    $('.shape').shape('repaint');

All the following behaviors can be called using the syntax:

    $('.your.element') .shape('behavior name', argumentOne, argumentTwo) ;

Behavior Description flip up Flips the shape upward flip down Flips the shape downward flip right Flips the shape right flip left Flips the shape left flip over Flips the shape over clock-wise flip back Flips the shape over counter-clockwise set next side(selector) Set the next side to a specific selector is animating Returns whether shape is currently animating reset Removes all inline styles queue(animation) Queues an animationtill after current animation repaint Forces a reflow on element set default side Set the next side to next sibling to active element set stage size Sets shape to the content size of the next side refresh Refreshes the selector cache for element sides get transform down Returns translation for next side staged below get transform left Returns translation for next side staged left get transform right Returns translation for next side staged right get transform up Returns translation for next side staged up get transform down Returns translation for next side staged down

Setting Default Description duration 700ms Duration of side change animation Setting Context Description beforeChange Next Side Is called before side change onChange Active Side Is called after visible side change Setting Default Description namespace shape Event namespace. Makes sure module teardown does not effect other events attached to an element. selector selector : { sides : '.sides', side : '.side' }

className className : { animating : 'animating', hidden : 'hidden', loading : 'loading', active : 'active' }

Setting Default Description name Shape Name used in debug logs debug False Provides standard debug output to console performance True Provides standard debug output to console verbose True Provides ancillary debug output to console error error: { side : 'You tried to switch to a side that does not exist.', method : 'The method you called is not defined' }
