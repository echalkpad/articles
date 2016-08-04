# PerfectlySoft/Perfect

[Original URL](https://github.com/PerfectlySoft/Perfect)

> Perfect is an application server which provides a framework for developing web and other REST services in the Swift programming language. Its pri

[![Get Involved](https://www.perfect.org/images/github-banner.jpg)](https://perfect.org/get-involved.html)

[![Perfect logo](https://www.perfect.org/images/icon_128x128.png)](https://camo.githubusercontent.com/2de532f84bb14fed9949eda6211747493d624664/68747470733a2f2f7777772e706572666563742e6f72672f696d616765732f69636f6e5f313238783132382e706e67) [![Join the chat at https://gitter.im/PerfectlySoft/Perfect](https://camo.githubusercontent.com/da2edb525cde1455a622c58c0effc3a90b9a181c/68747470733a2f2f6261646765732e6769747465722e696d2f4a6f696e253230436861742e737667)](https://gitter.im/PerfectlySoft/Perfect?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Perfect is an application server which provides a framework for developing web and other REST services in the Swift programming language. Its primary focus is on facilitating mobile apps which require backend server software. It enables you to use one language for both front and back ends.

Perfect operates using either its own stand-alone HTTP server or through FastCGI with Apache 2.4\. It provides a system for loading your own Swift based modules at startup and for interfacing those modules with its built-in moustache template processing system.

Perfect consists of the following components:

- [PerfectLib](https://github.com/PerfectlySoft/Perfect/blob/master/PerfectLib/#perfectlib) - Framework components and utilities for client and server.

  - [PerfectLib Reference](http://www.perfect.org/docs/)
  - OS X / Linux
  - iOS

- [Perfect Server](https://github.com/PerfectlySoft/Perfect/blob/master/PerfectServer/#perfectserver) - Backend server supporting FastCGI or stand-alone HTTP.

  - Perfect Server FastCGI - Server process which accepts connections over FastCGI.
  - Perfect Server HTTP - Stand-alone HTTP server.
  - Perfect Server HTTP App - Development focused stand-alone HTTP server OS X app.

- Connectors - Server-side connectivity.

  - [mod_perfect](https://github.com/PerfectlySoft/Perfect/blob/master/Connectors/mod_perfect/#mod_perfect) - FastCGI connectivity for Apache 2.4.
  - [MySQL](https://github.com/PerfectlySoft/Perfect/blob/master/Connectors/MySQL/#mysql) - Provides connectivity for MySQL databases.
  - [PostgreSQL](https://github.com/PerfectlySoft/Perfect/blob/master/Connectors/PostgreSQL/#postgresql) - Provides connectivity for PostgreSQL databases.
  - [MongoDB](https://github.com/PerfectlySoft/Perfect/blob/master/Connectors/MongoDB/#mongodb) - Provides connectivity for MongoDB databases (_in progress_).

- [Examples](https://github.com/PerfectlySoft/Perfect/blob/master/Examples/#examples) - A set of examples which show how to utilize Perfect.

  - Mobile iOS Examples
  - Web Site Examples
  - Game Examples (coming soon!)

## [](https://github.com/PerfectlySoft/Perfect#getting-started)Getting Started

Check the [README](https://github.com/PerfectlySoft/Perfect/blob/master/Examples/#examples) in the Examples directory for further instructions.

## [](https://github.com/PerfectlySoft/Perfect#more-information)More Information

For more information on the Perfect project, please visit [perfect.org](http://perfect.org).
