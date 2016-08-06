# Library is not defined

[Original URL](http://electron.rocks/library-is-not-defined/)

> App loads JS library, but still compiler says that: jQuery is not defined. Most people will experience this with jQuery, but I had similar problems with few other libs as well. This is caused because...

App loads JS library, but still compiler says that:

```
jQuery is not defined. 
```

Most people will experience this with jQuery, but I had similar problems with few other libs as well.

This is caused because library will probably contain something similar to this:

```
if ( typeof module === "object" && typeof module.exports === "object" ) {
 module.exports = ...
} else ...
```

This will create problems since library will use module exports to create definition on it, instead of using global object. So even though library is correctly loaded, it will find module (since Electron is using Node.js), and it will put definitions there.

The fix is to delete module, load library and restore module again. This did the trick for me.

Original answer is [here](http://stackoverflow.com/a/37480521/1064381) (thanks!). You can check related [forum thread](https://github.com/electron/electron/issues/254) as well.
