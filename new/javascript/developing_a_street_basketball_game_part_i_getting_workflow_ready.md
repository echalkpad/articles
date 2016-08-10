# Developing a street basketball game. (Part I. Getting workflow ready) -- WhitestormJS Framework

[Original URL](https://medium.com/whitestormjs-framework/developing-a-street-basketball-game-part-i-getting-workflow-ready-f4f6968e4d10)

> Some days ago i released a new version of WhitestormJS framework named r10 and decided to develop a game using almost all it's functionality. (Just was curious how easy it would be). The task...

![](https://cdn-images-1.medium.com/max/800/1*t_YNDcu8ZWgE5nDHPAKH5A.png)

Some days ago i released a new version of WhitestormJS framework named **r10** and decided to develop a game using almost all it's functionality. _(Just was curious how easy it would be)._ The task was to make a 3D game that requires complex 3D physics parts like concave _objects_ and _softbodies._

Before starting developing the game i added a couple of directories that i will use later: **js**, **css**, **textures** and **img.** Difference between _textures_ and _img_ folders is that _textures_ folder will be used for images that will be used in 3D part of app and _img_ is for images that are used in html, css and stuff like that.

Useful thing when you need to work with es6 models in front-end, especially when you use most of es6 features and want them even on old browser versions. I used it with two plugins: [babel for rollup](https://github.com/rollup/rollup-plugin-babel) and [uglify for rollup](https://github.com/rollup/rollup-plugin-babel) to develop a cross-browser 3D app.

Some libraries are getting updates regularly (yep, like _whitestorm.js_). For them i need front-end package manager like bower (You can use npm too, but bower will be enough). For tweening i used [**GSAP**](http://greensock.com/gsap) library, because of it's performance and also we need [**basket.js**](https://addyosmani.com/basket.js/) to keep such a large library as WhitestormJS in _localStorage_.

...And also i wrote a small bower plugin that will help me make preloading. [Check it out there.](https://github.com/sasha240100/ProgressLoader)

```
bower install whitestormjs gsap progress-loader basket.js
```

My index.html file will be simple and will contain just loaded scripts and _style.css_ for featured usage. Note that we don't load whitestorm.js yet. We will add it then using **basket.js.**

As you already noticed, we have _app.js_ included. So, let's create an empty 3D world with enabled softbody physics(will be used later for a net), autoresize, filled background, camera position and aspect, + gravity:

Now we have a basic world that has _0xcccccc (grey)_ as it's background color, gravity set as _{x: 0, y: -200, z: 0}_ and camera that will be at same height as our basket. Also we start the app only after we have whitestorm.js loaded.

I counted 14 objects that we will load for desktop devices. (I hope we'll also have an optimized version for mobile devices, so i used **APP.isMobile** variable which is a boolean.

Next thing we'll do is implementing a floor with just a ground and wall. Wall and ground will be very similar and we create them both from one object. We'll create a default plane 1000x800 with buffer geometry (Actually no need for buffer geometry there, but somebody says it increase performance;), but it won't be a big deal for a plane with 1x1 segments.

**0 mass** means that this object is not affected by gravity and won't move when collides with other objects. And we need to rotate it's by _(-Math.PI / 2, 0, 0)_ euler.

When we add each object to world -- it returns a promise which is resolved when object is generated and added to world. Such stuff is very useful when work with models or object's you want to know they are ready. Such as when we need to update loading status when each one is added.

We need just 2 simple lights to get a cool effect. Ambient light will cast light on the whole scene, Point light will cast only on objects near basket.

That's how it looks now:

![](https://cdn-images-1.medium.com/max/800/1*BH9fCSY_A5x9PIYcWVEH7Q.png)

Scene with wall, ground and lights. After we added lights we need to have a basket in which we will throw a ball. Our basket will be a simple torus with thin tube radius. Also i experimented with material parameters to get it metal look.

One more thing is that we set a _"concave"_ physics object type. By default, in performance reason each mesh with concave geometry works as a convex mesh for physics, but you can simply make it concave by setting this parameter. We make it static by applying 0 mass to this object.

Now it's time to make a ball. This time we are going to use optimization for mobile devices again (geometry with less vertices. It won't work with physics because when we process sphere collisions it's enough to have just a sphere radius)

This time we also have to apply x3 restitution to make out sphere jump like a basketball ball.

We use 2 textures for ball material. First one (in _map_ parameter) is a default colored texture. Second one (_normalMap_) defines how light will be casted on this ball.

I generated a normal map using [this normal map generator](http://cpetry.github.io/NormalMap-Online/). I often use it. You can make a normal map not just from a black-white heightmap, for generating this one, i just used first texture.

![](https://cdn-images-1.medium.com/max/800/1*x2f2M9oFNyFLhK-RaaNaQQ.png)

How ball map and normal map looks like.

If everything done right, you should see something like this:

![](https://cdn-images-1.medium.com/max/800/1*8m2Xu1wO-A8odWJDGlqf7w.png)

...And the ball will fall down...

Hopefully, second part will be next week. Don't forget to recommend this tutorial;)

A result of this part is also [available on github](https://github.com/WhitestormJS/StreetBasketballGame/tree/part1).

Full game: [Github](https://github.com/WhitestormJS/StreetBasketballGame)| [Demo](http://whitestormjs.xyz/StreetBasketballGame/)
