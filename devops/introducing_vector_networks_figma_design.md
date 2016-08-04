# Introducing Vector Networks -- Figma Design

[Original URL](https://medium.com/figma-design/introducing-vector-networks-3b877d2b864f#.9bfdtwvb0)

> Before I co-founded Figma my background was in game development, not in design. I remember being very surprised when I first encountered modern vector editing tools. Many of the interactions felt...

![](https://cdn-images-2.medium.com/max/2000/1*A8KOKqq-ygO8W0jYhF1pLg.png)

Before I co-founded [Figma](http://figma.com) my background was in game development, not in design. I remember being very surprised when I first encountered modern vector editing tools. Many of the interactions felt broken. Why couldn't you just manipulate things directly? Why did connecting and disconnecting stuff only sometimes work? Is this the best we can do?

The pen tool as we know it today was [originally introduced in 1987](https://www.youtube.com/watch?v=xv3xl2B6yUs) and has remained largely unchanged since then. We decided to try something new when we set out to build the vector editing toolset for Figma. Instead of using paths like other tools, Figma is built on something we're calling _vector networks_ which are backwards-compatible with paths but which offer much more flexibility and control:

![](https://cdn-images-2.medium.com/max/800/1*r1gof0PNNWj1kpQsIp8Ivw.gif)

A vector network in action After our first implementation of vector networks, we did a series of user studies to refine the concept. We were surprised to discover that many people didn't even notice a difference between vector networks and paths. The tool just worked how people expected it to work. They _did_ notice the difference when they went back and tried to use other tools, however. That was especially painful to watch and confirmed to us we had found something special.

To understand vector networks, it's useful to first understand paths. A path is a chain of lines and curves from one endpoint to the other. You can think of a path as a sequence of instructions that a device like a pen plotter might follow. Put the pen down, drag it around, and lift it up.

![](https://cdn-images-2.medium.com/max/800/1*fNbtOlHkI1E_BfrUaThFCQ.jpeg)

Think of a pen plotter when thinking of paths (Photograph by [Tomasz Sienicki](https://commons.wikimedia.org/wiki/User:Tsca "User:Tsca")) A vector network improves on the path model by allowing lines and curves between any two points instead of requiring that they all join up to form a single chain. This helps provide the best of both worlds; it combines the ease with which points can be connected on paper and the ease with which geometry can be manipulated once it's drawn. Splitting and recombining geometry is much more natural with vector networks. Delete anything, anywhere. Connect anything to anything else. Stroke cap and join styles just work naturally in vector networks, even for points with three or more lines coming off of them. This isn't the case for paths because it's impossible to use paths to represent attaching three lines together at a single point.

![](https://cdn-images-2.medium.com/max/800/1*PxChJlNJWli3Do9k2ppczg.gif)

Vector networks support standard stroke cap and join styles Another way we wanted to improve over existing vector editing tools was to add support for direct manipulation. Vector graphics today are based on[cubic bezier splines](https://vimeo.com/106757336), which are curves with two extra points called _control handles_ that are positioned away from the curve itself and that control how much it bends, sort of like how a magnet might bend a wire towards it. Changing the shape of a curve involves dragging a control handle off in space instead of dragging the curve directly.

We initially experimented with some powerful curve types that can provide greater control and shaping power, but in the end it made the most sense to remain backwards-compatible with existing vector data. That's why both paths and vector networks use the same type of curves. In addition to dragging the control handles, Figma's bend tool (the command key on OS X) lets you drag the curve around directly. The editor will automatically figure out where to place the control handles for you:

![](https://cdn-images-2.medium.com/max/800/1*J7-SIlkTIeirqMBToahUUQ.gif)

The bend tool drags curves around The other job of the vector engine is filling the area between curves with color. All current vector engines use a confusing concept called the _winding number_ to fill the area between curves. It uses the number of times the path winds around a given location to decide whether that location is inside or outside the shape. The number depends on the direction in which you originally drew the curves (clockwise or counter-clockwise), which is especially counter-intuitive because no vector editor actually shows you the direction your curve is going in.

![](https://cdn-images-2.medium.com/max/800/1*Dyp1KVaHUQN1WtjYnrzs_w.png)

The non-zero winding rule means fill depends on curve orientation Instead of using this confusing concept, fills in Figma start off by automatically filling all enclosed space:

![](https://cdn-images-2.medium.com/max/800/1*2Oi7tJtWQlcRGf4Y2ps8rQ.gif)

Enclosed space is filled automatically If you later want to add holes, just punch the holes out directly without needing to worry about curve orientation:

![](https://cdn-images-2.medium.com/max/800/1*_3EyhWTUL5C6aWuy5EqR1g.gif)

The paint bucket tool can toggle the fill for any enclosed region Getting fills to work well was the hardest part. We tried a few different ways of representing fills but kept running into the problem of requiring the user to explicitly manipulate negative space to create holes, which was hard to wrap your head around. The tool to toggle regions on and off was the trick that finally made it work.

Reinventing something as fundamental as paths was hard. The solution we came up with may seem obvious, but there were many dead ends and existential moments during development and many times where we considered cutting our losses and just doing paths even after all that work. There's still lots more we'd like to do to make vector networks even better, but we're satisfied with how vector networks turned out and we're excited to get them in front of designers!
