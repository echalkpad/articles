# Introducing Tamper

[Original URL](http://nytimes.github.io/tamper/)

> What is Tamper? Tamper is a serialization protocol for categorical data. It achieves high compression ratios by finding the smallest possible binary representation for each category. How does it...

## What is Tamper?

Tamper is a serialization protocol for categorical data. It achieves high compression ratios by finding the smallest possible binary representation for each category.

## How does it work?

Take, for example, a boolean attribute. In naïve JSON we would represent this as a string, "true" or "false":

`[{ "guid" : 1, "edpick" : true }, { "guid" : 1, "edpick" : false }]`

Each value is 4-5 bytes; including punctuation **27 bytes are required per item**.

Tamper evaluates the data to find the most efficent encoding--in this case, a [BitmapPack](https://github.com/NYTimes/tamper/wiki/Packs/#bitmap-pack).

The data is serialized as `10`, **just 0.25 bytes!**

Full details of Tamper's encoding scheme are in the [protocol docs](https://github.com/NYTimes/tamper/wiki/Packs).

## How can it help me?

If you have a large set of objects that:

- you want to transfer from server to browser
- have categorical attributes (i.e. with a distinct set of possibilities)
- have numeric ids (Tamper cannot pack string ids)

Pourover can dramatically reduce the size of your data file, as well as time and memory required to serialize/deserialize.

## How do I get started?

Tamper is intended as a companion to [pourover.js](http://nytimes.github.io/pourover/), but can be used independently.

You'll need:

## How does Tamper compare to other serialization approaches?

### gzipped JSON

Gzip works by [writing backreferences to previous symbols](http://en.wikipedia.org/wiki/DEFLATE). Each time a symbol is repeated, gzip encodes the location and length of the backreference. These references are in turn compressed -- but in most real-world applications are larger than a direct binary encoding.

Additionally, conventional array-of-object JSON layouts sometimes generate backreferences at each attribute boundary. Because Tamper packs all values for an attribute in a fixed-width format, there is no attribute boundary overhead.

### Google Protocol Buffers

Integer packs are similar in concept to Protocol Buffer `varints`: integer size is dynamically scaled to fit the value rather than fixed at 32 bits. However, the minimum size of a varint is one byte; for many applications Tamper requires only 2-5 bits for each item.

[Protocol Buffers](https://developers.google.com/protocol-buffers/) are optimized for messaging details about a particular item; Tamper packs optimize for bulk categorical loads across many items.

Tamper is distributed under the [Apache 2.0 License](https://github.com/NYTimes/tamper/blob/master/LICENSE.txt).

![OpenNews logo](http://nytimes.github.io/tamper/public/opennews-logo.png) [Released for OpenNews Code Convening, April 2014](http://opennews.org/code.html)
