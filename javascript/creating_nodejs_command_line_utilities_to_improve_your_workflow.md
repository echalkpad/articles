# Creating Node.js Command Line Utilities to Improve Your Workflow

[Original URL](http://developer.telerik.com/featured/creating-node-js-command-line-utilities-improve-workflow/)

> Once upon a time, the command line seemed scary and intimidating to me. I felt as if it stared back at me blankly like the price tag on something extremely expensive saying, "If you have to...

Once upon a time, the command line seemed scary and intimidating to me. I felt as if it stared back at me blankly like the price tag on something extremely expensive saying, "If you have to ask, it's not for you." I preferred the comfort of the buttons and menus laying out for me what the possibilities were.

Today, the command line is all about completing tasks as fast as you can type them, without the overhead of a GUI program. I've even [spoken before](https://www.youtube.com/watch?v=zfP1caw0ANw) about how npm offers a wealth of command-line utilities that can improve a developer's workflow. And since then, the list of tools has only grown tremendously.

However, in every job there are repetetive tasks that both unique to that position but also ripe for automating...if only it were easy to build yourself a tool to do so.

Today, I'm going to explore how, using an npm library named [Vorpal](https://github.com/dthree/vorpal), it is relatively easy to create your own command line applications. And I do mean applications.

What differentiates Vorpal from [rolling your own command line tools](https://developer.atlassian.com/blog/2015/11/scripting-with-node/) is that it makes it easy to build much more complex and immersive command line applications rather than simple, single-command utilities. These command line applications run within their own context that offers built-in help, command history, tabbed auto-completion and more. Combining Vorpal with the abundance of modules available on npm opens up a ton of possibilities.

## A Simple Example

Most tutorials start with "Hello World." That's boring. I want something with a little more attitude. I'm sure you're already thinking what I'm thinking. Right, let's build a "Hello Daffy Duck."

[![Daffy Duck](http://developer.telerik.com/wp-content/uploads/2015/11/DuckRabbitDuck.jpg)](http://developer.telerik.com/wp-content/uploads/2015/11/DuckRabbitDuck.jpg)

My sample command line application is going to be based off the [Rabbit Fire](http://www.imdb.com/title/tt0043953/) episode from Looney Tunes. If you don't know which one I am talking about, then you are missing out. It's a classic.

Of course, first you need to install [Vorpal](https://github.com/dthree/vorpal).

```
npm install vorpal
```

Now, let's create an daffy.js file and require Vorpal.

```
var vorpal = require('vorpal')();
```

### Delimiter

One of the cool things about Vorpal is that it offers a variety of methods for you to customize the "UI" of your utility. For example, let's start by making our command line delimiter "daffy."

```
vorpal
 .delimiter('daffy$')
 .show();
```

Go ahead and run the program via `node daffy.js` and you'll see something like this:

[![Daffy Delimiter](http://developer.telerik.com/wp-content/uploads/2015/11/daffy_delimeter.jpg)](http://developer.telerik.com/wp-content/uploads/2015/11/daffy_delimeter.jpg)

At this point our utility doesn't do anything, so let's add some functionality.

### Commands

The primary way of interacting with a command line utility is via [commands](https://github.com/dthree/vorpal/wiki/api-%7C-vorpal.command#vorpalcommandcommand-description). Commands in Vorpal have a lot of flexibility – for instance, they can be a single word or multiple words and have required arguments as well as optional arguments.

Let's start with a simple one word command – when we say "duck" then Daffy will respond with "Wabbit."

```
// duck
vorpal
 .command('duck', 'Outputs "rabbit"')
 .action(function(args, callback) {
 this.log('Wabbit');
 callback();
 });
```

The `command()` method defines the text of the command as well as the help text (i.e. what will show up when I use `--help` in the command line). We then use Vorpal's `log()` method rather than the `console.log()` to output to the console. Calling the callback method will return the user to our custom console, as opposed to exiting the program.

### Completed Code

The complete code for the Daffy command line application is below. It includes several multi-word commands that all work fairly similarly.

```
var vorpal = require('vorpal')(),
 duckCount = 0,
 wabbitCount = 0;

// duck
vorpal
 .command('duck', 'Outputs "rabbit"')
 .action(function(args, callback) {
 this.log('Wabbit');
 callback();
 });
vorpal
 .command('duck season', 'Outputs "rabbit season"')
 .action(function(args, callback) {
 duckCount++;
 this.log('Wabbit season');
 callback();
 });

// wabbit
vorpal
 .command('wabbit', 'Outputs "duck"')
 .action(function(args, callback) {
 this.log('Duck');
 callback();
 });
vorpal
 .command('wabbit season', 'Outputs "duck season"')
 .action(function(args, callback) {
 // no cheating
 if (duckCount < 2) {
 duckCount = 0;
 this.log('You\'re despicable');
 callback();
 }
 else if (wabbitCount === 0) {
 wabbitCount++;
 this.log('Duck season');
 callback();
 }
 // doh!
 else {
 this.log('I say it\'s duck season. And I say fire!');
 vorpal.ui.cancel();
 }
 });

vorpal
 .delimiter('daffy$')
 .show();
```

Since the methods all work similarly, this should be pretty straightforward. You'll notice the use of `vorpal.ui.cancel()`, this exits the application and returns to the main command line console (one note, this can't be followed by additional code or it will fail to function).

Now that the application is built, a `help` command is built in, without requiring any additional code. Notice that the multiword commands are automatically grouped.

[![Daffy help](http://developer.telerik.com/wp-content/uploads/2015/11/vorpal_help.jpg)](http://developer.telerik.com/wp-content/uploads/2015/11/vorpal_help.jpg)

Let's see our application in action!

[![Daffy command line app](http://developer.telerik.com/wp-content/uploads/2015/11/daffy_opt.gif)](http://developer.telerik.com/wp-content/uploads/2015/11/daffy_opt.gif)

[![Daffy fired](http://developer.telerik.com/wp-content/uploads/2015/11/daffy_fired.jpg)](http://developer.telerik.com/wp-content/uploads/2015/11/daffy_fired.jpg)

## A Real World Example

While I'm certain you will find hours of entertainment in the Daffy sample, let's try something a little more complex. In this example application, we'll explore things like required and optional arguments as well as getting feedback from the user via [prompts](https://github.com/dthree/vorpal/wiki/API-%7C-CommandInstance#commandinstancepromptobject-callback).

The utility we are going to start building will allow us to quickly resize and reformat images individually or in bulk. In my role managing this site (i.e. the Telerik Developer Network), I often receive articles with very numerous large PNG formatted images. These images need to be resized and reformatted before posting to reduce the weight of the images on the page. Being able to quickly resize every image in a folder would make this a much simpler and less tedious task.

### Sharp

Of course, on its own, Vorpal can't do image resizing. And while Node.js has a [filesystem](https://nodejs.org/docs/v0.3.1/api/fs.html#file_System) module, it doesn't have the capability to manipulate images.

Luckily, there are a number of image resizing modules on npm. The one I chose to use is called [Sharp](https://github.com/lovell/sharp) ([documentation](http://sharp.dimens.io/en/stable/)).

Sharp works fast, but, unfortunately, the [setup is a little complex](http://sharp.dimens.io/en/stable/install/) depending on what platform you are running on. Sharp depends on a library called [libvips](https://github.com/jcupitt/libvips), which we'll need to install first. I am working on a Mac with [Homebrew](http://brew.sh/) installed, which offered the easiest route for me to get libvips.

```
brew install homebrew/science/vips --with-webp --with-graphicsmagick
```

Once libvips is installed, then install Sharp.

```
npm install sharp
```

Of course, to use Sharp within our application, we'll need to require it.

```
var sharp = require('sharp');
```

### MMMagic

Since we'll be bulk modifying any images in a given directory, we'll need to be able to tell that any given file is an image or not. Again, the filesystem module, while powerful, doesn't quite meet our needs.

For this purpose, I chose a npm module called [MMMagic](https://github.com/mscdex/mmmagic). MMMagic allows you to pass a file and it will get the MIME type, encoding and other metadata for you.

There are no complicated prerequisites for MMMagic, simply...

```
npm install mmmagic
```

Now require it...

```
var mmm = require('mmmagic');
```

We'll also need an instance of it to work with.

```
var Magic = mmm.Magic;
```

The reason for having both the `mmm` and `Magic` variables is because later on we'll need to access some constants that are in the root (`mmm` in this case).

### Putting All the Pieces Together

We'll start by creating a single `resize` command that will take both a required argument (the path of the directory or image that you want to resize) as well as some optional arguments. The optional arguments are available as a shortcut for the user to bypass some of the prompts and make our task even faster to complete.

```
vorpal
 .command('resize <path> [width] [height]', 'Resize an image or all images in a folder')
 .action(function(args, cb) {
 const self = this;
 path = args.path;

 // if a width and height are defined when the command is called, set them
 if (args.width)
 width = args.width;
 if (args.height)
 height = args.height;

 // resize all images in a directory to a set width
 if (fs.lstatSync(path).isDirectory()) {
 this.prompt({
 type: 'confirm',
 name: 'continue',
 default: false,
 message: 'Resize all images in the folder? ',
 },
 function(result){
 if (result.continue) {
 files = fs.readdirSync(args.path);
 // skip the prompts if a width was supplied
 if (width)
 doResize(self);
 else
 getWidth(self);
 }
 else {
 cb();
 }
 });
 }
 // resize a single image
 else if (fs.lstatSync(args.path).isFile()) {
 // get the file name without the path
 files = [args.path.split("/").pop()];
 //get the path without the file name
 path = args.path.substr(0, args.path.lastIndexOf('/'))
 // skip the questions if a width was supplied
 if (width)
 doResize(self);
 else
 getWidth(self);
 }
 });
```

Let's walk through the code a bit. The required argument, `path`, is denoted with angle brackets (i.e. `<` and `>`). The optional arguments, `width` and `height`, are denoted by square brackets (i.e. `[` and `]`). The arguments are available in the `args` object.

After setting our variables, we determine whether the user has passed a filename or a directory and respond accordingly (note that a future improvement would be to handle situations where the user passes an invalid file or directory). If the user passed a directory, we display a corfirm-type prompt to ensure that they intend to resize every image in the directory.

Finally, in both cases we either move on to complete the resizing or move to the next prompt if the user did not specify a width argument in the command. Let's look at the prompts to get the width and height when the user doesn't specify them.

### Input Prompts

The below prompts request additional information for the maximum width and height via an input-type prompt on the command line. The way the application currently works is that it will shrink anything over these sizes to the specified dimensions, but will not enlarge items that are already less than the specified dimensions.

```
// ask for a width
function getWidth(v) {
 self = v;
 self.prompt({
 type: 'input',
 name: 'width',
 default: false,
 message: 'Max width? ',
 },
 function(result){
 if (result.width)
 width = result.width;
 getHeight(self);
 });
}

// ask for a height
function getHeight(v) {
 self = v;
 self.prompt({
 type: 'input',
 name: 'height',
 default: false,
 message: 'Max height? ',
 },
 function(result){
 if (result.height)
 height = result.height;
 doResize(self);
 });
}
```

Both inputs have a default if no value is entered (i.e. the user simply hits the enter key).

### Resizing the Images

The next step is to loop through the specified files and resize them. Note that in the case where a user specifies a single file, it is still placed within the same array of files, it just only contains a single file.

```
function doResize(v) {
 self = v;
 // create a folder to dump the resized images into
 if (!fs.existsSync('optimized'))
 fs.mkdirSync('optimized')

 for (var i in files) {
 detectFileType(files[i]);
 }
}
```

You'll note above that we create a folder to place the optimized images.

Within the loop, we call a function to detect the file type to prevent errors when the folder contains files other than images.

```
function detectFileType(filename) {
 var fullpath = path + "/" + filename,
 filenameNoExt = filename.substr(0, filename.lastIndexOf('.'));
 magic = new Magic(mmm.MAGIC_MIME_TYPE);

 // make sure this is an appropriate image file type
 magic.detectFile(fullpath, function(err, result) {
 if (!err) {
 if (result.split('/')[0] == 'image')
 // resize to a jpeg without enlarging it beyond the specified width/height
 sharp(fullpath)
 .resize(parseInt(width),parseInt(height))
 .max()
 .withoutEnlargement()
 .jpeg()
 .toFile('optimized/'+filenameNoExt+'.jpg', function(err) {
 if (err)
 self.log(err);
 else
 self.log('Resize of ' + filename + ' complete');
 });
 }
 else
 self.log(err);
 });
}
```

In the `detectFileType` method we use the MMMagic module to determine if the current file is actually an image. Sharp can handle [most image types](http://sharp.dimens.io/en/stable/api/#format), so at this point we're simply assuming that if it is an image, Sharp can handle it (plus, if the image processing fails for some reason, we do note it in the console output).

Lastly, we finally put Sharp to use to perform the resize. The `resize()` method can take a width and/or height, and if only one is passed, it will resize while maintaining the same aspect ratio. The `max()` and `withoutEnlargement()` methods ensure that we only resize images larger than the specified sizes, though all images will be processed and converted into a JPEG regardless.

If the resize is successful, we note it in the console. If there is an error, we note that as well.

Let's see the utility in action. In the example below, I am passing just the directory so that you can see the prompts (note that I do not supply a height). As you can see by the results, the images in the optimized folder are all JPEG (including the one PNG from the source) and are all 600px width (except the one image that was already less than 600px wide).

[![resizing utility](http://developer.telerik.com/wp-content/uploads/2015/11/resizer_opt.gif)](http://developer.telerik.com/wp-content/uploads/2015/11/resizer_opt.gif)

We could bypass the prompts by using the command `resize images 600` which would have specified the width. We also could have simply resized a single image using `resize images\test3.png 600`.

### Future Improvements

While this utility is already useful, I would love to add some improvements. First, of course, even though this is a personal utility, the code could almost certainly be improved (I'm relatively new to using Node.js, so I'm sure there are things I can be doing better).

Second, I should [package the command line utility](https://developer.atlassian.com/blog/2015/11/scripting-with-node/#packaging-shell-commands) so that it is installable. This would allow me to call it much more easily from whichever directory I happen to be working in.

Also, at this point, it really is a single command utility, but there are a number of ways I would like to see it expand into a full application. For example, I didn't specify an output [quality](http://sharp.dimens.io/en/stable/api/#qualityquality) for my JPEG, which is something I will need to do in the long run. I could also create additional commands to allow different kinds of resizing or even cropping – Sharp offers a good number of [operations](http://sharp.dimens.io/en/stable/api/#operations) that can be performed on images that we didn't cover here. In the long run, it could become a full command-line image tool rather than just a resizer.

## Conclusion

I'm sure that there are a ton of tasks in your own job that you could potentially automate. Creating a complete command-line application for these purposes is easy using [Vorpal](https://github.com/dthree/vorpal). These applications can get very complex – we only touched on commands and prompts, but Vorpal offers a fairly [extensive API](https://github.com/dthree/vorpal#api).

So get busy automating those time-consuming and repetetive tasks with custom command-line applications. You don't even need to tell your boss. Perhaps you can use the spare time to hunt wabbits – it's wabbit season after all!

[![Rabbit Season](http://developer.telerik.com/wp-content/uploads/2015/11/Rabbit-Season.jpg)](http://developer.telerik.com/wp-content/uploads/2015/11/Rabbit-Season.jpg)
