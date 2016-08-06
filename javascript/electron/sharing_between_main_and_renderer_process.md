# Sharing between main and renderer process

[Original URL](http://electron.rocks/sharing-between-main-and-renderer-process/)

> In main process: global.something = value; In renderer process: const remote = require('electron').remote; var something = remote.getGlobal('something'); To use same instance between main and...

In main process:

```
global.something = value;
```

In renderer process:

```
const remote = require('electron').remote;
var something = remote.getGlobal('something');
```

To use same instance between main and renderer process, you can use global object from Node. To demonstrate this, let's add logger to sample Todo application.

In `main.js` we're going to create logger instance which can be required and used in all other renderers. For logging I'm using [winston](https://www.npmjs.com/package/winston):

```
const logger = require('winston');
logger.level = 'debug';
```

Also, let's add logging to file:

```
var logFolder = path.join(app.getPath("userData"), "logs");
var logFile = new Date().toISOString().replace(/:/g, '.') + '.log';
logger.add(logger.transports.File, { filename: path.join(logFolder, logFile) });
```

This should create timestamped log file for each session in log folder in your [user data folder](http://electron.atom.io/docs/api/app/#appgetpathname).<br>
_Note: All `:` chars needs to be escaped, since it will create problems on Windows._

Finally, save logger instance to global object:

```
global.logger = logger;
```

Now that logger is accessible in global object, in renderer you can fetch it by using remote module. Following code will fetch logger instance and inject it in Angular for use throughout the app:

```
const {remote} = require('electron');
const logger = remote.getGlobal('logger');
angular
 .module('app')
 .value('logger', logger);
```

**Final note:**

Although winston does create one instance which can be used throughout your app, this doesn't apply when you have main and renderer process architecture like is in Electron. That's why I had to inject single instance in each renderer.

You can see it in action in standard [example project](https://github.com/Vj3k0/ea-todo).

Hope it helps, cheers!
