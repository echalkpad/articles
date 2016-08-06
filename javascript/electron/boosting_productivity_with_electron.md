# Boosting productivity with Electron

[Original URL](http://electron.rocks/boosting-dev-productivity/)

> You can speed up your development process by using live reloading to see your app in action on each change. First, let's add package: npm i live-reload –save-dev Note: There are a lot of packages...

You can speed up your development process by using live reloading to see your app in action on each change.

First, let's add package:

```
npm i live-reload --save-dev 
```

_Note: There are a lot of packages with similar name, so be sure to use this exact one._

In your `index.html` add this:

Now, we're going to need to fire up reload server along with our app. What I do is add one more npm script in my `package.json`:

```
"reload": "live-reload app --port 35729"
```

This command will open up live reload server on port (which is same one as specified in HTML), and will watch for changes in `app` folder. Prior to running my app, I just open up new terminal window and run it:

```
npm run reload 
```

As always, you can check it out in my sample project:<br>
<https://github.com/Vj3k0/ea-todo>

**Bonus tip:** This works pretty awesome in combination with [onchange](https://www.npmjs.com/package/onchange). If you'd like me to include sample of this, let me know.

Happy coding!
