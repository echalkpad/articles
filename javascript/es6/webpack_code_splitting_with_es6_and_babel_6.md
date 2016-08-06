# WebPack Code splitting with ES6 and Babel 6

[Original URL](http://jonathancreamer.com/webpack-code-splitting-with-es6-and-babel-6/)

> Today is the day that finally, we decided it was time to make the move from Babel 5 to 6\. It's been on our Technical Debt list for quite a while now. Here's what we had to do to get it working! First...

Today is the day that finally, we decided it was time to make the move from Babel 5 to 6\. It's been on our Technical Debt list for quite a while now.

Here's what we had to do to get it working!

First and foremost was to get all the latest and greatest from the Babel folks...

![](http://d.pr/i/15CP5+)

```
dependencies: { 
 "babel-core": "^6.5.2",
 "babel-polyfill": "^6.3.14",
 ...
},
devDependencies: { 
 "babel-loader": "^6.2.3",
 "babel-plugin-transform-decorators-legacy": "^1.3.4",
 "babel-preset-es2015": "^6.5.0",
 "babel-preset-react": "^6.5.0",
 ...
}
```

That's easy enough.

In case you hadn't yet yeard, Babel 6 is quite a bit different in it's philosophy. What started out as 6to5 whose job it was to merely transpile ES6 to ES5, Babel has now evolved into a transpiler of all the things. Highly plugable, faster, and easier to configure, Babel 6 is a fantastic way to usher in a new era of JavaScript tooling.

Ok, so in order to get Babel 6 working with your webpack setup, you have to change the loader...

```
test: /(\.jsx?)$/, 
loader: "babel", 
exclude: /node_modules/, 
query: { 
 "plugins": ["transform-decorators-legacy"],
 "presets": ["es2015", "react"]
}
```

That should get you most of the way there now.

## A thing we ran in to

As we were doing the upgrade, we ran into an interesting challenge. We use [Code Splitting](http://jonathancreamer.com/advanced-webpack-part-2-code-splitting/) to divide up our app into multiple smaller modules. As soon as the webpack and npm updates were in place, everything compiled just fine, however, when it came to the browser, something a bit strange happened.

We use ES6 modules for all our JavaScript code. More often than not, we use `export default`. I spent about an hour banging my head on the desk as the modules I was importing during my code splitting just were NOT working.

Turns out Kent C. Dodds has a fantastic article on why this was happening... [https://medium.com/@kentcdodds/misunderstanding-es6-modules-upgrading-babel-tears-and-a-solution-ad2d5ab93ce0#.wrsu2x6c5](mailto:https://medium.com/@kentcdodds/misunderstanding-es6-modules-upgrading-babel-tears-and-a-solution-ad2d5ab93ce0#.wrsu2x6c5)

This is how we're splitting our code up...

```
require([ 
 "rizzo-next/src/components/things_to_do",
 "rizzo-next/src/components/sights"
 /bin /boot /dev /etc /git /home /initrd.img /initrd.img.old /lib /lib64 /lost+found /media /mnt /opt /proc /root /run /sbin /snap /srv /sys /tmp /usr /var /vmlinuz /vmlinuz.old ... */
], function(ThingsToDo, Sights, /*...*/) {

 rizzo.renderComponent(Sights, ".sights");
 rizzo.renderComponent(ThingsToDo, ".ttd");
});
```

All that code worked just fine in Babel 5, but apparently, thanks to Kent's article, I learned that we were all apparently using ES6 modules incorrectly and Babel 6 for better or worse let us know that. Turns out though, the solution was actually not so bad...

```
rizzo.renderComponent(Sights.default, ".sights"); 
rizzo.renderComponent(ThingsToDo.default, ".ttd"); 
```

The only way to use CodeSplitting in WebPack v1 is with the require syntax. That should change in v2, but that's not out yet.

TL;DR

Don't mix ES6 and Require syntax in WebPack v1, but if you want to use Code Splitting, add `.default` to your imported modules.

Another hang up we had was, we like to `npm link` to our main component repository `rizzo-next`. Well, apparently if you `npm link` things, it gets all screwy when it comes to where loaders load things from.

There are 2 ways to solve this problem...

1. Install all the same tools in the other repository, aka all teh bablez in both places
2. Use `require.resolve` in the `query` options in WebPack

<!--  -->

```
query: { 
 "plugins": [require.resolve("babel-plugin-transform-decorators-legacy")],
 "presets": [require.resolve("babel-preset-es2015")]
}
```

## Conclusion

All in all, it wasn't a hugely painful upgrade. After all, we knew going into it that it was a major version uprade so there'd most likely be hiccups. After just a few hours, everything was transpiling as it was in Babel 5 and we were on our way!
