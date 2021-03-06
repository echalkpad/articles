# Dartino Project

[Original URL](https://dartino.github.io/sdk/)

> Dartino is an experimental project to enable highly productive development for embedded devices. It is powered by the Dart language and a fast, lean runtime. This page walks you through running your...

Dartino is an **experimental** project to enable highly productive development for embedded devices. It is powered by the [Dart language](https://www.dartlang.org/docs/dart-up-and-running/ch02.html) and a fast, lean runtime.

This page walks you through running your first program on Dartino. Note that this early version of Dartino only supports a single embedded device – the Raspberry Pi 2 – and that the only supported client operating systems supported for development are MacOS and Linux (sorry, no Windows support).

If you just want to see what Dartino programs look like, take a peek at our [samples page](https://dartino.github.io/sdk/samples.html).

## What you will need

To develop embedded programs with Dartino, you will need the following:

- A developer PC (running MacOS or Linux) where you write the programs

- A Raspberry Pi 2 embedded device for running the programs

- A MicroSD card to hold the operating system for the Raspberry Pi 2, and some kind of SD card reader

- A network connection between the developer PC and the Raspberry Pi 2

- Optional: A breadboard and a collection of components for running some of the samples (will be discussed later)

![What you need photo](https://storage.googleapis.com/fletch-archive/images/setup.jpg)

## Installing the SDK

First download the SDK. This is available as a '.zip' archive; pick the one that matches the OS of the PC you will be using for development:

- [Dartino SDK for MacOS (64-bit)](https://storage.googleapis.com/fletch-archive/channels/dev/release/latest/sdk/fletch-sdk-macos-x64-release.zip)
- [Dartino SDK for Linux (64-bit)](https://storage.googleapis.com/fletch-archive/channels/dev/release/latest/sdk/fletch-sdk-linux-x64-release.zip)

Unzip the SDK, and add the 'fletch' command to the path by typing the below in a terminal window:

```
cd $HOME
unzip Downloads/fletch-sdk-macos-x64-release.zip
export "PATH=$PATH:$HOME/fletch-sdk/bin"
```

Test if the 'fletch' command works; it should print a version number to the console:

```
fletch --version
```

## Running your first program

Let's go ahead and run our first Dartino program. This is a simple program that prints Hello. In your command line type:

```
cd $HOME/fletch-sdk/
fletch run samples/general/hello.dart
```

This runs the program in hello.dart on your local machine. You should see a message that says `Created settings file...`, and then output resembling this:

```
Hello from Darwin running on michael-pc2.
```

Try to open `hello.dart` in your favorite editor. We recommend the [Atom editor](https://atom.io/) by Github with the [Dart plugin](https://github.com/dart-atom/dartlang/). Pretty easy to read, right? (Note: you will get some Analyzer warnings in Atom as we don't fully support it yet. You can ignore those.)

### Dartino and sessions

But what actually happened when we asked the `fletch` command to run `hello.dart`? By default `fletch` is connected to a _local session_, which is connected to a local VM (Virtual Machine) running on your developer PC. When you ask `fletch` to run the program, it compiles the program to byte code, and then passes it to the local Dartino VM for execution. The VM passes back the result, and Dartino prints it to your command line.

![Dartino architecture diagram](https://storage.googleapis.com/fletch-archive/images/Fletch-architecture.png)

Now let's get things running in a _remote session_ that is connected to your Raspberry Pi 2!

## Preparing your Raspberry Pi 2

First we need to prepare an SD card with an image that contains the standard Raspbian operating system, and the Dartino binaries for Raspberry Pi 2.

This step uses a script that automates the download of Raspbian, and performs the needed configuration. If you prefer to configure the image manually, see the [manual install instructions](https://dartino.github.io/sdk/manual-install.html).

Start by opening a terminal window, and enter the following command (_note_: you will be prompted to enter your password as the script performs system level operations):

```
platforms/raspberry-pi2/flash-sd-card
```

The script will take you through the following steps:

1. _Specify which SD card to use_: You will be asked to remove all SD cards, and then insert _just_ the one you wish to use for Dartino. **_Important_**: Everything on this SD card will be erased during the installation process.

2. _Specify device name_: The name your Raspberry Pi 2 device will be given on the network.

3. _Specify IP address_: If you are using automatically allocated IP addresses assigned by a router with DHCP just press enter. To specify a static IP, for example when using a direct connection (see below), enter the desired IP address.

4. _Downloading_: Download of the Raspbian base image. This can take up to 10 minutes depending on the speed of your Internet connection. A progress indicator will be shown.

5. _Flashing_: Flashing of the image onto the SD card. This can take up to 10 minutes depending on the speed of your SD card. A progress indicator will be shown.

## Connecting your Raspberry Pi 2 to the network

Once the steps in the script are completed, and you see the `Finished flashing the SD card` message, remove the SD card from your PC and insert it into the Raspberry Pi 2.

Next, we need to ensure your PC can communicate with the Raspberry over the network.

### Option 1: Router-based connection

In this option, both your PC and Raspberry are connected to the same networking router.

1. Connect the Raspberry to your router with an Ethernet cable.

2. Connect your PC to the same router.

### Option 2: Direct connection

If you do not wish to use a router, or if the Raspberry is not permitted to join your network, you can connect it directly to your PC.

1. Connect the Raspberry to your PC with an Ethernet cable. If your PC does not have an empty Ethernet port, you can use an USB Ethernet adapter.

### Testing the connection

After connecting using option 1 or 2, test the connection by running this command:

```
fletch show devices
```

If the connection is working, you should see output resembling `Device at 192.168.1.2 raspberrypi.local`.

## Running on the Raspberry Pi 2

Let's make our Hello program run again, this time on the Raspberry Pi 2\. All we need is to specify to the `fletch run` command that we want to run in a remote session. Type the following on your local developer PC:

```
fletch run samples/general/hello.dart in session remote
```

The first time you run in the remote session you will be asked to specify the IP address. Select from the list of devices discovered on the network, or manually enter the IP address, e.g. `192.168.2.2`.

You should see output resembling this on your screen:

```
Hello from Linux running on raspberrypi.
```

Did you notice the difference from when we ran in the local session? As before Dartino compiled the hello.dart program to byte code, but this time rather than passing it to the local VM via the local session it passed it to the Raspberry Pi via the remote session. On the Raspberry Pi, the Dartino VM Agent made sure that a VM (Virtual Machine) was spun up, and the program was executed on it. The result of the program (the printing to the console) was passed back by the VM to the 'fletch' command on the developer PC, and it was printed to the local console.

## Next steps

Ready for some more fun? Take a look at our [samples](https://dartino.github.io/sdk/samples.html), and read more about the [fletch command](https://dartino.github.io/sdk/tool.html).

And don't forget to send us some [feedback](https://dartino.github.io/sdk/feedback.html), and ask some [questions](https://dartino.github.io/sdk/faq.html).
