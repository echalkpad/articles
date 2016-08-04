# Developer Edition 44: New visual editing and memory management tools

[Original URL](https://hacks.mozilla.org/2015/11/developer-edition-44-creative-tools-and-more/)

> This month marks the one-year anniversary of Firefox Developer Edition. To celebrate, we're excited to show you some new tools – and some improvements to existing tools – that...

This month marks the one-year anniversary of Firefox Developer Edition. To celebrate, we're excited to show you some new tools – and some improvements to existing tools – that let you work with the Web in a visual and intuitive way.

As the Web becomes a more dynamic, interactive and mobile experience, visual designers are more than ever experimenting with animation – and the latest Firefox Developer Edition has tools to make working with animations faster and easier. Firefox Developer Edition now gives visual designers and animators a set of visual editing tools that work like they do. We are addressing the technically challenging aspects of animation with a set of visceral, powerful, interactive tools that are comfortable for designers to use. Visual editing tools should appeal to animators, not just to programmers.

## DevTools Challenger

Reading about our new tools is great, but trying them out yourself is even better! To help you get started, we partnered with acclaimed Web animation engineer and advocate [Rachel Nabors](http://rachelnabors.com/) to create [DevTools Challenger](http://devtoolschallenger.com/). Check out DevTools Challenger for hands-on exercises with all of our new visual design tools. Remember to keep scrolling until you get to the ocean floor!

[![Screenshot of the Devtools Challenger demo website](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/challenger-500x409.png)](http://devtoolschallenger.com)

## Animation & CSS Filter Tools

The Page Inspector's animation panel makes it easy to scrub, pause, and visualize each animation on a webpage. Thanks to its tight integration in the DOM inspector, you can switch between a global view of every animation, or drill down to just a few nodes.

[![animationtools](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/animationtools.png)](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/animationtools.png)

Once you've found the animation you want, our visual cubic-bezier editor is just a click away. Packed with useful presets, the editor will ensure your animation moves perfectly.

[![Screenshot of the visual cubic-bezier curve editor](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/cubicbezier-cropped-500x447.png)](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/cubicbezier-cropped.png)

We've also built a similar editor for CSS filters, allowing you to visually add, remove, re-order, and adjust filters with live feedback from the page.

[![filtereditor-cropped](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/filtereditor-cropped-500x266.png)](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/filtereditor-cropped.png)

## Measurements & Colors

Firefox Developer Edition also features two brand new tools for fine-tuning layout: you can now enable pixel rulers along the page margins, or use our new measurement tool to drag-and-measure arbitrary regions of the page.

[![](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/measuretools1-500x344.png)](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/measuretools1.png)

The Inspector now defaults to displaying CSS colors "as authored," and shift-clicking on a color swatch cycles between authored styles and equivalent hex, rgb, and hsl values. There's even an eyedropper tool to pick colors right from the page.

## Memory Snapshots

The new Memory tool helps front-end engineers better understand and optimize the way pages allocate and retain memory. The tool works by taking a snapshot of the heap, then allows you to drill down by retained object type, allocation stack, or internal representation. We think you'll like it, and we've got many more features and enhancements in the works!

[![Screenshot of the DevTools memory panel](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/nyanparticles-500x304.png)](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/nyanparticles.png)

## WebSocket Debugging API

Lastly, we're extremely excited to announce that Firefox now exposes an API for monitoring WebSocket frames ([Bug 1203802](https://bugzilla.mozilla.org/show_bug.cgi?id=1203802)), which is the first step on the path to a full-fledged WebSocket inspection tool. Developer Tools engineer Jan Odvarko has built [an experimental add-on](https://github.com/firebug/websocket-monitor/wiki) for inspecting WebSocket traffic. Install the add-on and give it a try, we'd love your feedback.

[![Screenshot of the experimental WebSocket Debugger add-on](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/websocket-demo-500x202.png)](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2015/11/websocket-demo.png)

Firefox Developer Edition is available at [firefox.com/developer](https://firefox.com/developer). Let us know what you think about these features by commenting below or following [@FirefoxDevTools](https://twitter.com/FirefoxDevTools) on Twitter!

Dave Camp is Director of Engineering for Firefox at Mozilla.

[More articles by Dave Camp...](https://hacks.mozilla.org/author/dcampmozilla-com/)

Engineer with Mozilla Developer Relations, former Mozilla Persona developer.

[More articles by Dan Callahan...](https://hacks.mozilla.org/author/dcallahanmozilla-com/)
