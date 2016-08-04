# yodlr/CoreGI

[Original URL](https://github.com/yodlr/CoreGI)

> WebUI for monitoring CoreOS clusters including fleet and etcd Built with love by the Yodlr team What is CoreGI CoreGI came out of our need for a one-stop dashboard for viewing the stat

[![DockerHub Stats](https://camo.githubusercontent.com/27d56cbc2dd19e7db19b76161add8e667cf6b04b/687474703a2f2f646f636b6572692e636f2f696d6167652f796f646c722f636f72656769)](https://camo.githubusercontent.com/27d56cbc2dd19e7db19b76161add8e667cf6b04b/687474703a2f2f646f636b6572692e636f2f696d6167652f796f646c722f636f72656769)

WebUI for monitoring CoreOS clusters including fleet and etcd

[![CoreGI](https://raw.githubusercontent.com/yodlr/CoreGI/master/site/img/coregi_logo.png)](https://raw.githubusercontent.com/yodlr/CoreGI/master/site/img/coregi_logo.png)

Built with love by the [Yodlr](https://getyodlr.com) team

## [](https://github.com/yodlr/CoreGI#what-is-coregi)What is CoreGI

CoreGI came out of our need for a one-stop dashboard for viewing the status of the applications running in our CoreOS clusters, monitoring etcd keys, and managing rolling software updates. We're pro cli-tools, but have found that simple web-apps provide improved visibility into complex systems.

[![CoreGI Dashboard](https://raw.githubusercontent.com/yodlr/CoreGI/master/docs/images/coregi-units.png)](https://raw.githubusercontent.com/yodlr/CoreGI/master/docs/images/coregi-units.png)

## [](https://github.com/yodlr/CoreGI#coregi-runs-in-coreos)CoreGI runs in CoreOS

CoreGI was created to be as simple as possible and deploying CoreGI is as easy as running a docker container. CoreGI can also be installed into a **CoreOS cluster** using a CoreOS service file. Take a look at our CoreOS [service file](https://github.com/yodlr/CoreGI/blob/master/services/coregi.service) for an example. CoreGI can also be ran as an application using Node.js by using environment variables to change the default options, see [Getting Started](https://github.com/yodlr/CoreGI#getting-started).

We have setup automated Docker Hub builds for CoreGI [here](https://registry.hub.docker.com/u/yodlr/coregi/)

## [](https://github.com/yodlr/CoreGI#getting-started)Getting Started

### [](https://github.com/yodlr/CoreGI#coreos)CoreOS

```
git clone https://github.com/yodlr/CoreGI.git
cd CoreGI
fleetctl start services/coregi.service
```

Our example service file is setup to be a Global service listening on port 3000\. This can be modified in the CoreOS service file which can be found [here](https://github.com/yodlr/CoreGI/blob/master/services/coregi.service).

### [](https://github.com/yodlr/CoreGI#docker)Docker

```
$ docker run --name coregi -p 3000:3000 yodlr/coregi:latest
```

### [](https://github.com/yodlr/CoreGI#nodejs)Node.js

To change default options, see [options](https://github.com/yodlr/CoreGI#coregi-options). Example with custom option:

```
$ FLEET_BINARY=/usr/local/bin/fleetctl npm start
```

## [](https://github.com/yodlr/CoreGI#coregi-options)CoreGI Options

CoreGI default options. To change the default options, simply set the value of any of the following environment variables.

```
PORT = 3000
FLEET_BINARY = '/usr/bin/fleetctl'
FLEET_TUNNEL = '172.17.42.1' //If set, will remove FLEET_ENDPOINT option.
FLEET_ENDPOINT = 'http://172.17.42.1:4001'
ETCD_HOST = '172.17.42.1'
ETCD_PORT = 4001
LOG_LEVEL = 'info'
```

## [](https://github.com/yodlr/CoreGI#coregi-features)CoreGI Features

CoreGI currently supports querying Fleet for listing of **machines**, **units**, **unit-files** and Etcd for listing of **keys**. CoreGI also exposes a **REST API** with the following endpoints:

- GET /api/machines
- GET /api/units
- GET /api/unitFiles
- GET /api/keys

### [](https://github.com/yodlr/CoreGI#future-features)Future Features

Some of the features we're thinking/planning on implementing include:

- Unit logs
- Unit status
- Start/stop/load/unload units
- Monitoring and editing etcd keys
- Rolling deployment of units

### [](https://github.com/yodlr/CoreGI#coregi-is-built-with)CoreGI is built with:

- [Node.js](http://nodejs.org/)
- [AngularJS](https://angularjs.org/)
- [Bootstrap](http://getbootstrap.com/)
- [Docker](https://www.docker.com/)
