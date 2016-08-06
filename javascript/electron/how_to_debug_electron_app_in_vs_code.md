# How to debug Electron app in VS Code

[Original URL](http://electron.rocks/debugging-electron-in-vs-code-revised/)

> Last time I wrote about debugging Electron apps in Code, I found that it is possible only to debug main process. But not much code will be in your main process, but in renderers, so it would leave...

Last time I wrote about debugging Electron apps in Code, I found that it is possible only to debug main process. But not much code will be in your main process, but in renderers, so it would leave you to fire up Chrome dev tools and work it from there.

On Electron website, there are instructions how you can debug your app, but it is enabled only for main process. In my [original post](http://electron.rocks/debugging-electron-in-vs-code/), I've made instructions how to debug it from VS Code, but that was also available only for main process. Since Electron comes packed with Chrome DevTools, you can debug your renderer process there. Thanks to @IsidorN, I've become aware of [Chrome debug extension for VS Code](https://github.com/Microsoft/vscode-chrome-debug) which actually does just that.

First, you'll have to install this extension. So, fire up Code, press `Cmd+Shift+P`, type `install` and select `Extensions: Install Extension`. Then type `chrome` and select `Debugger for Chrome`. Extension is now installed and it will probably need to restart.

So here is the magic step. In your launch.json, add new configuration:

```
{
 "name": "Debug",
 "type": "chrome",
 "request": "launch",
 "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/electron",
 "runtimeArgs": [
 "${workspaceRoot}",
 "--enable-logging",
 "--remote-debugging-port=9222"
 ],
 "sourceMaps": false
}
```

Now in your Debug panel, you can find new target: _Debug_. Select it and fire up your app. You should be able to hit the breakpoints and debug your renderer process in Code.

This new extension is adding new type in launch configuration: `chrome`. That's going to do the heavy lifting of connecting our code through web sockets and pass around debug info.<br>
Usually this extension is working with Chrome executable where user will pass url to which he's going to connect, but here we are specifying `runtimeExecutable` in same fashion like any other launch configuration.

In previous post we've set `cwd` to specify root directory and then in `runtimeArgs` passed `.` char to tell Electron where is our app root. This won't work and we need to explicitly give full path to Electron exec in `runtimeArgs`. Another special thing is `--remote-debugging-port=9222`. This is the magic argument that will enable debugging info on port 9222\. If you run your app and then in browser look at `127.0.0.1:9222`, you would see something like this:

```
[ {
 "description": "",
 "id": "AD2B1FA4-8FAD-435A-A67F-CCA58CA342D1",
 "title": "EA Todo",
 "type": "page",
 "url": "file:///Users/vratkajec/dev/ea-todo/app/index.html"
} ]
```

Happy debugging!
