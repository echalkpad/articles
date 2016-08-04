# Create a Desktop Chat Application using the CEAN Stack and Electron

[Original URL](http://blog.couchbase.com/2016/january/create-a-desktop-chat-application-using-the-cean-stack-and-electron)

> So you're interested in creating a desktop chat application like Slack, but aren't sure where to start. Slack was very open in saying that they used Github's Electron platform for building the...

So you're interested in creating a desktop chat application like Slack, but aren't sure where to start. Slack was very open in saying that they used Github's [Electron](http://electron.atom.io) platform for building the desktop version of their software. This means that Slack was developed using web technologies.

We're going to see how to create a real-time chat desktop application that is nowhere near as feature rich as Slack, but it will give you perspective on how you could expand to that point.

## The Prerequisites

Although not requiring much, there are a few things that you'll need in order to be successful with what comes next:

- Couchbase Server 4+
- Node Package Manager (NPM)

We need Couchbase Server because all chat messages will be cached in the database after being sent. This allows everyone joining the chat room to be able to see previous messages. This is important because people may be joining the chat later than others and we wouldn't want them to miss out on anything. The Node Package Manager (NPM) is required because we'll be using it to install Angular 2, and Electron. It is available through installing [Node.js](http://nodejs.org).

With all that said, let's start designing our project.

## Cloning the Socket.io Server

As you may know, this isn't the first tutorial I wrote on the subject of real-time chat using the CEAN stack. I actually wrote two others:

To save us some time, we're going to use the server side code that we developed in the first CEAN stack tutorial, not the Ionic 2 tutorial. If you haven't already, I recommend you read through it. However, you can obtain the source code by [downloading it](https://github.com/couchbaselabs/cean-web-chat) from GitHub, or by executing the following from your Terminal (Mac and Linux) or Command Prompt (Windows):

```
git clone https://github.com/couchbaselabs/cean-web-chat
```

With the server side code downloaded, navigate into the project using your Command Prompt or Terminal and install all the dependencies by executing:

```
npm install
```

At this point, your chat server code is ready to go!

### Configuring Couchbase

Since Couchbase will be used with the Node.js application, we need a bucket configured and allowed to use N1QL queries. This was all explained in the first [CEAN stack chat tutorial](https://blog.nraboy.com/2016/01/create-a-real-time-chat-application-with-the-cean-stack-and-socket-io), but just as a refresher, let's see it again.

Install a copy of Couchbase Server 4 and create a bucket called **web-chat** that has the query service enabled. Since we will be using N1QL queries we need to create a primary index. This can be done using the Couchbase Query (CBQ) client. On Mac execute the following from a Terminal:

```
./Applications/Couchbase Server.app/Contents/Resources/couchbase-core/bin/cbq
```

The same can be done on Windows using the Command Prompt like so:

```
C:/Program Files/Couchbase/Server/bin/cbq.exe
```

With CBQ open, execute the following to create an index:

```
CREATE PRIMARY INDEX ON `web-chat` USING GSI;
```

Couchbase Server is now ready to go!

## Creating an Electron Project

Our Electron project is going to be a lot of cut and paste when it comes to code. This is because Electron pretty much accepts any web application you throw at it. Remember we have a very suitable web application bundled within our Socket.io server project.

Create a new directory, maybe on your Desktop, and call it **desktop-chat** or whatever you think is most appropriate. Navigate into the project with your Terminal or Command Prompt and execute the following:

```
npm init -y
```

This creates a very basic NPM **package.json** file in our project. Now we need to install all our desktop application dependencies. Execute the following:

```
npm install angular2@2.0.0-beta.0 systemjs typescript --save
npm install electron-prebuilt --save-dev
```

The first install will get all of our Angular 2 dependencies. In the server side project, the client facing UI was all made with Angular 2 and TypeScript. We'll do the same here. The second install obtains the Electron binary.

Now open the **package.json** file found at the root of the project and swap out the **scripts** part with the following:

```
"scripts": {
 "start": "electron main.js"
},
```

We'll see what that means soon, but first create a **main.js** file at the root of the project. It should contain the following code:

```
var app = require('app'); // Module to control application life.
var BrowserWindow = require('browser-window'); // Module to create native browser window.

// Keep a global reference of the window object, if you don't, the window will
// be closed automatically when the JavaScript object is garbage collected.
var mainWindow = null;

// Quit when all windows are closed.
app.on('window-all-closed', function() {
 // On OS X it is common for applications and their menu bar
 // to stay active until the user quits explicitly with Cmd + Q
 if (process.platform != 'darwin') {
 app.quit();
 }
});

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
app.on('ready', function() {
 // Create the browser window.
 mainWindow = new BrowserWindow({width: 800, height: 600});

 // and load the index.html of the app.
 mainWindow.loadURL('file://' + __dirname + '/public/index.html');

 // Emitted when the window is closed.
 mainWindow.on('closed', function() {
 // Dereference the window object, usually you would store windows
 // in an array if your app supports multi windows, this is the time
 // when you should delete the corresponding element.
 mainWindow = null;
 });
});
```

The above may look familiar for two reasons. One I took it from a [previous post](http://blog.couchbase.com/build-a-desktop-app-with-github-electron-and-couchbase) I wrote on Electron and two, it came pretty much straight from the Electron documentation. Our important line, however, is this:

```
mainWindow.loadURL('file://' + __dirname + '/public/index.html');
```

This means all our Angular 2 code will be in the **public** directory starting with the **index.html** file.

Copy the **public** directory from the **cean-web-chat** project you cloned from GitHub, and place it in the Electron project's root. There is already an **index.html** file in it, but we need to change a few things. Open it and change the **script** paths to the following:

```
<script src="../node_modules/angular2/bundles/angular2-polyfills.js"></script>
<script src="../node_modules/systemjs/dist/system.src.js"></script>
<script src="../node_modules/rxjs/bundles/Rx.js"></script>
<script src="../node_modules/angular2/bundles/angular2.dev.js"></script>
<script src="../node_modules/angular2/bundles/http.dev.js"></script>
<script src="../node_modules/angular2/bundles/router.dev.js"></script>
```

We did this because our library paths are not in the same place as they were in the server side application.

A few more things to do before we're ready to see it in action. Open your Electron project's **public/app/default/default.ts** file and create a new variable within the class as so:

```
socketHost: string;
```

This should be done within the class. Because we're no longer on the same host as the server, we need to tell our desktop client what host to connect to. Inside the constructor, set **socketHost** to whatever the host should be:

```
this.socketHost = "http://192.168.57.1:3000";
```

Our HTTP request in the constructor is no longer valid because our client is not bundled within the server. We need to define the host we just set. It can be done like so:

```
http.get(this.socketHost + "/fetch").subscribe((success) => {
```

Finally, find the line where you define your Socket.io object. That too needs a host to point to. Change it to look like the following:

```
this.socket = io(this.socketHost);
```

You should be good at this point!

## Seeing it in Action

You'll need two Command Prompts or Terminals open for this. The first Terminal will run the Socket.io Node.js server that connects to Couchbase Server. Execute the following, with the GitHub project as your working directory, to do so:

```
node app.js
```

If the server started then you're in good shape. In the second Terminal, execute the following, with the Electron project as your working directory:

```
npm start
```

Remember we made the **start** script within our **package.json** file? That's how we use it.

Try sending some messages. They should save to Couchbase Server and if you open another client or load the embedded chat client from the GitHub project, you can do a chat.

## Conclusion

You just saw how to convert an existing web application into a cross platform desktop application. This application offered real-time chat using bleeding edge technologies like Angular 2, Couchbase, [Socket.io](http://www.socket.io), and Node.js.

If you did not already, I recommend checking out the previous posts I did on the topic of Socket.io with Couchbase. They are good reads and can open the door to possibilities for you.
