# skavanagh/KeyBox

[Original URL](https://github.com/skavanagh/KeyBox)

> About A web-based ssh console to execute commands and manage multiple systems simultaneously. KeyBox allows you to share terminal commands and upload files to all your systems. Once the sessions have...

## [](https://github.com/skavanagh/KeyBox#about)About

A web-based ssh console to execute commands and manage multiple systems simultaneously. KeyBox allows you to share terminal commands and upload files to all your systems. Once the sessions have been opened you can select a single system or any combination to run your commands. Additional system administrators can be added and their terminal sessions and history can be audited. Also, KeyBox can manage and distribute public keys that have been setup and defined.

## [](https://github.com/skavanagh/KeyBox#screenshots)Screenshots

[![Terminals](https://github-camo.global.ssl.fastly.net/5e6a87e04a98cf5e696b59db179309af7c2c4f2a/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f36342f34322f36343432396337346531613562346439623764323663343930323832313530615f6d656469756d2e706e67)](https://github-camo.global.ssl.fastly.net/5e6a87e04a98cf5e696b59db179309af7c2c4f2a/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f36342f34322f36343432396337346531613562346439623764323663343930323832313530615f6d656469756d2e706e67)

[![More Terminals](https://github-camo.global.ssl.fastly.net/93acd508bdc19b1f847817d501083bc1f75f871d/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f61302f34342f61303434623365313163633161663435336538666535623937333162643661355f6d656469756d2e706e67)](https://github-camo.global.ssl.fastly.net/93acd508bdc19b1f847817d501083bc1f75f871d/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f61302f34342f61303434623365313163633161663435336538666535623937333162643661355f6d656469756d2e706e67)

[![Upload Files](https://github-camo.global.ssl.fastly.net/c6bcf3fbed95a5d2af16be1deb753e3172c7322e/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f65362f37362f65363736666535343262303831383863666631666462636565613135616466345f6d656469756d2e706e67)](https://github-camo.global.ssl.fastly.net/c6bcf3fbed95a5d2af16be1deb753e3172c7322e/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f65362f37362f65363736666535343262303831383863666631666462636565613135616466345f6d656469756d2e706e67)

[![Manage Systems](https://github-camo.global.ssl.fastly.net/c7dab0d5a0c8bcfda3a49abab3e077c6fae7c467/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f37312f65362f37316536343634373434616539356432643033616235626265356135373665315f6d656469756d2e706e67)](https://github-camo.global.ssl.fastly.net/c7dab0d5a0c8bcfda3a49abab3e077c6fae7c467/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f37312f65362f37316536343634373434616539356432643033616235626265356135373665315f6d656469756d2e706e67)

[![Manage Users](https://github-camo.global.ssl.fastly.net/96b9a4cf7a7dfc311d95322c3a0b8ebe7a46951f/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f63302f61332f63306133623735386338306333613633346533333237663535633432393366325f6d656469756d2e706e67)](https://github-camo.global.ssl.fastly.net/96b9a4cf7a7dfc311d95322c3a0b8ebe7a46951f/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f63302f61332f63306133623735386338306333613633346533333237663535633432393366325f6d656469756d2e706e67)

[![Define SSH Keys](https://github-camo.global.ssl.fastly.net/c5bb23254f8d556532ead1e9b2bf2102f8b52ff9/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f31362f66392f31366639346132373334663362353039646632646361396566653739636263355f6d656469756d2e706e67)](https://github-camo.global.ssl.fastly.net/c5bb23254f8d556532ead1e9b2bf2102f8b52ff9/68747470733a2f2f66726565636f64652e636f6d2f73637265656e73686f74732f31362f66392f31366639346132373334663362353039646632646361396566653739636263355f6d656469756d2e706e67)

## [](https://github.com/skavanagh/KeyBox#prerequisites)Prerequisites

Java JDK 1.7 or greater <http://www.oracle.com/technetwork/java/javase/overview/index.html>

Browser with Web Socket support <http://caniuse.com/websockets>

**Note: In Safari if using a self-signed certificate you must import the certificate into your Keychain. Select 'Show Certificate' -> 'Always Trust' when prompted in Safari

Must run on *nix with OpenSSH version 2

Maven 3 or greater ( Only needed if building from source ) <http://maven.apache.org>

## [](https://github.com/skavanagh/KeyBox#to-run-bundled-with-jetty)To Run Bundled with Jetty

If your not big on the idea of building from source...

Download keybox-jetty-vXX.XX.tar.gz

<https://github.com/skavanagh/KeyBox/releases>

Export environment variables

```
 export JAVA_HOME=/path/to/jdk
 export PATH=$JAVA_HOME/bin:$PATH
```

Start KeyBox

```
 ./startKeyBox.sh
```

How to Configure SSL in Jetty (it is a good idea to add or generate your own unique certificate)

<http://wiki.eclipse.org/Jetty/Howto/Configure_SSL>

## [](https://github.com/skavanagh/KeyBox#to-build-from-source)To Build from Source

Export environment variables

```
export JAVA_HOME=/path/to/jdk
export M2_HOME=/path/to/maven
export PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH
```

In the directory that contains the pom.xml run

```
mvn package jetty:run
```

**Note: Doing a mvn clean will delete the H2 DB and wipe out all the data.

## [](https://github.com/skavanagh/KeyBox#using-keybox)Using KeyBox

Open browser to <https://<whatever> ip>:8443

Login with

```
username:admin
password:changeme
```

Steps:

1. Create systems
2. Create profiles
3. Assign systems to profile
4. Assign profiles to users
5. Users can login to create sessions on assigned systems
6. Start composite-ssh sessions or create and execute a script across multiple sessions
7. Add additional public keys to systems
8. Audit session history

## [](https://github.com/skavanagh/KeyBox#author)Author

**Sean Kavanagh**

- [sean.p.kavanagh6@gmail.com](mailto:sean.p.kavanagh6@gmail.com)
- <https://twitter.com/spkavanagh6>
