# kasketis/netfox

[Original URL](https://github.com/kasketis/netfox)

> A lightweight, one line setup, network debugging library that provides a quick look on all executed network requests performed by your app. It grabs all requests - of course yours, requests from 3rd...

[![](https://raw.githubusercontent.com/kasketis/netfox/master/assets/netfox_logo.png)](https://raw.githubusercontent.com/kasketis/netfox/master/assets/netfox_logo.png)

A lightweight, one line setup, network debugging library that provides a quick look on all executed network requests performed by your app. It grabs all requests - of course yours, requests from 3rd party libraries (such as AFNetworking or else), UIWebViews, and more

Very useful and handy for network related issues and bugs

Implemented in Swift 2.1 - bridged also for Objective-C

Feel free to contribute :)

## [](https://github.com/kasketis/netfox#overview)Overview

[![](https://raw.githubusercontent.com/kasketis/netfox/master/assets/overview.gif)](https://raw.githubusercontent.com/kasketis/netfox/master/assets/overview.gif)

## [](https://github.com/kasketis/netfox#installation)Installation

Copy the "nfx" folder in your project (make sure that "Create groups" option is selected) and add the following line in didFinishLaunchingWithOptions: method of your AppDelegate

Swift

```
NFX.sharedInstance().start()
```

Objective-C

```
[[NFX sharedInstance] start];
```

Just simple as that!

Note: Please wrap the above line with

```
#if DEBUG
. . .
#endif
```

to prevent library's execution on your production app.

You can add the DEBUG symbol with the -DDEBUG entry. Set it in the "Swift Compiler - Custom Flags" section -> "Other Swift Flags" line in project's "Build Settings"

## [](https://github.com/kasketis/netfox#usage)Usage

Just shake your device and check what's going right or wrong! Shake again and go back to your app! fact [![](https://raw.githubusercontent.com/kasketis/netfox/master/assets/shake.png)](https://raw.githubusercontent.com/kasketis/netfox/master/assets/shake.png)

## [](https://github.com/kasketis/netfox#custom-gestures)Custom gestures

By default the library registers for shake motion. If you want to open the logs with an other way than shake add the following line after the installation one

```
NFX.sharedInstance().setGesture(.custom)
```

Then you can use

```
NFX.sharedInstance().show()
```

when you want to show the logs and

```
NFX.sharedInstance().hide()
```

when you want to hide them.

## [](https://github.com/kasketis/netfox#features)Features

- Search: You can easily search among requests via

  - Request url: github.com, .gr, or whatever you want
  - Request method: GET, POST, etc
  - Response type: Like json, xml, html, image and more

- Sharing: You can share your log via email with backend devs or someone who can help.

  - Simple log option includes only request/response headers and small request/response bodies (when applicable)
  - Full log option includes request/response headers and request/response bodies (as attachments)

- More to come.. ;)

## [](https://github.com/kasketis/netfox#other)Other

- Alamofire users check [this](https://github.com/kasketis/netfox/blob/master/Alamofire.md)
- Due to the large size of request/response bodies, the library provides disk storage for low memory overhead

## [](https://github.com/kasketis/netfox#licence)Licence

All source code is licensed under [MIT License](https://github.com/kasketis/netfox/blob/master/LICENSE). Which means you could do virtually anything with the code. I will appreciate it very much if you keep an attribution where appropriate.
