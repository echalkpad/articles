# jwagner/smartcrop.js

[Original URL](https://github.com/jwagner/smartcrop.js/)

> Smartcrop.js implements an algorithm to find good crops for images. Image: <https://www.flickr.com/photos/endogamia/5682480447/> by N. Feans Demos Algorithm Overview Smartcrop.js works using fairly...

Smartcrop.js implements an algorithm to find good crops for images.

[![Example](http://29a.ch/sandbox/2014/smartcrop/example.jpg)](https://camo.githubusercontent.com/1086f55f4396e6c34f076b8f4a43c083fc5253d1/687474703a2f2f3239612e63682f73616e64626f782f323031342f736d61727463726f702f6578616d706c652e6a7067) Image: [https://www.flickr.com/photos/endogamia/5682480447/](https://www.flickr.com/photos/endogamia/5682480447) by N. Feans

## [](https://github.com/jwagner/smartcrop.js/#demos)Demos

## [](https://github.com/jwagner/smartcrop.js/#algorithm-overview)Algorithm Overview

Smartcrop.js works using fairly dumb image processing. In short:

1. Find edges using laplace
2. Find regions with a color like skin
3. Find regions high in saturation
4. Generate a set of candidate crops using a sliding window
5. Rank them using an importance function to focus the detail in the center and avoid it in the edges.
6. Output the candidate crop with the highest rank

## [](https://github.com/jwagner/smartcrop.js/#simple-example)Simple Example

```
SmartCrop.crop(image, {width: 100, height: 100}, function(result){console.log(result);});
// {topCrop: {x: 300, y: 200, height: 200, width: 200}}
```

## [](https://github.com/jwagner/smartcrop.js/#download-installation)Download/ Installation

`npm install smartcrop` or `bower install smartcrop` or just download [smartcrop.js](https://raw.githubusercontent.com/jwagner/smartcrop.js/master/smartcrop.js) from the git repo.

## [](https://github.com/jwagner/smartcrop.js/#cli--nodejs)CLI / Node.js

The [smartcrop-cli](https://github.com/jwagner/smartcrop-cli) offers command line interface to smartcrop.js. It is based on node.js and node-canvas. You can also view it as an example on how to use smartcrop.js from a node.js app.

## [](https://github.com/jwagner/smartcrop.js/#module-formats)Module Formats

Supported:

- common js
- amd
- global export / window

## [](https://github.com/jwagner/smartcrop.js/#supported-browsers)Supported Browsers

See [caniuse.com/canvas](http://caniuse.com/canvas)

## [](https://github.com/jwagner/smartcrop.js/#api)API

The API is not yet finalized. Look at the code for details and expect changes.

### [](https://github.com/jwagner/smartcrop.js/#smartcropcropimage-options-callback)SmartCrop.crop(image, options, callback)

Crop image using options and call callback(result) when done.

**image:** anything ctx.drawImage() accepts, usually HTMLImageElement, HTMLCanvasElement or HTMLVideoElement. Keep in mind that [origin policies](https://en.wikipedia.org/wiki/Same-origin_policy) apply to the image source, and you may not use cross-domain images without [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

**options:** see cropOptions

**callback:** function(cropResult)

### [](https://github.com/jwagner/smartcrop.js/#cropoptions)cropOptions

**debug:** if true, cropResults will contain a debugCanvas

**minScale:** minimal scale of the crop rect, set to 1.0 to prevent smaller than necessary crops (lowers the risk of chopping things off).

**width:** width of the crop you want to use.

**height:** height of the crop you want to use.

There are many more (for now undocumented) options available. Check the [source](https://github.com/jwagner/smartcrop.js/blob/master/smartcrop.js#L32) and know that they might change in the future.

### [](https://github.com/jwagner/smartcrop.js/#cropresult)cropResult

```
{
 topCrop: crop,
 crops: [crop]
}
```

### [](https://github.com/jwagner/smartcrop.js/#crop)crop

```
{
 x: 1,
 y: 1,
 width: 1,
 height: 1
}
```

## [](https://github.com/jwagner/smartcrop.js/#tests)Tests

You can run the tests using grunt test. Alternatively you can also just run grunt (the default task) and open <http://localhost:8000/test/>. The test coverage for smartcrop.js is very limited at the moment. I expect to improve this as the code matures and the concepts solidify.

## [](https://github.com/jwagner/smartcrop.js/#benchmark)Benchmark

There are benchmarks for both the browser (test/benchmark.html) and node (node test/benchmark-node.js [requires node-canvas]) both powered by [benchmark.js](http://benchmarkjs.com).

If you just want some rough numbers: It takes **< 100 ms** to find a **square crop** of a **640x427px** picture on an i7\. In other words, it's fine to run it on one image, it's not cool to run it on an entire gallery on page load.

## [](https://github.com/jwagner/smartcrop.js/#contributors)Contributors

## [](https://github.com/jwagner/smartcrop.js/#ports-alternatives)Ports, Alternatives

## [](https://github.com/jwagner/smartcrop.js/#license)License

Copyright (c) 2014 Jonas Wanger, licensed under the MIT License (enclosed)
