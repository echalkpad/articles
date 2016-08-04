# Raspberry Pi GUI Tutorial

[Original URL](https://www.baldengineer.com/raspberry-pi-gui-tutorial.html)

> My favorite Raspberry Pi add-on is the PiTFT from Adafruit. With it, you easily get a Raspberry Pi GUI interface and touch screen. The PiTFT software install is just a few things and it is good to go.

![Raspberry Pi GUI Tutorial](https://www.baldengineer.com/wp-content/uploads/2016/02/raspberry-pi-gui-tutorial-hero-v3-760x403.jpg) 

<span class="dropcap" title="M">M</span>

y favorite Raspberry Pi add-on is the [PiTFT from Adafruit](https://www.adafruit.com/products/2097). With it, you easily get a Raspberry Pi GUI interface and touch screen. The [PiTFT software install](https://learn.adafruit.com/adafruit-pitft-3-dot-5-touch-screen-for-raspberry-pi/easy-install) is just a few things and it is good to go.

[![Adafruit PiTFT - Click for more info](https://www.baldengineer.com/wp-content/uploads/2016/02/adafruit-pitft-2097-05-300x225.jpg)](https://www.adafruit.com/products/2097) Image from adafruit.com

This screen is what I needed in my IoT project. The Pi+Screen will act as the primary controller for all of my things. The problem is I didn't know much about writing GUI applications in Linux. So what could I do to create a Raspberry Pi GUI?

Python is popular in Pi projects, so I decided to stick with it and find out what GUI toolkits are ready to go. "Ready to go" means they install easily on Raspian and work well on the Pi.

Here is how I got Qt5 for Python up and running to create a Raspberry Pi GUI.

## Raspberry Pi GUI Libraries

If you start with this article on dice.com, you'll find [five potential python toolkits](http://insights.dice.com/2014/11/26/5-top-python-guis-for-2015/).

Not listed there is [TkInter](https://wiki.python.org/moin/TkInter), which is the most used Python toolkit. If you have Python running on your system, you have TkInter. I decided not to use it though because the widgets have a very dated look to them.

The other toolkit I considered was [wxPython](http://wxpython.org). The UI widgets have a modern feel, though they not entirely native looking. Also plenty of documentation exists. However, I had some trouble getting [wxGlade](http://wxglade.sourceforget.net) running, which is a WYSIWYG GUI editor.

This lead me to Qt.

## Why Qt for Raspberry Pi GUI

In college, I used Qt for my senior design project: an electronic notebook. It was a PC with a 10" touch screen. You could write on it, and it'd save what you write, about ten years before Apple did it.

Having a little bit of familiarity with Qt, I looked into its Python bindings and was happy. I decided on the [PyQt binding from Riverbank](https://www.riverbankcomputing.com/software/pyqt/intro) to go along with Qt5.

### Note about licensing PyQt and Qt

PyQt is available as GPL or Commercial License while Qt is available under LGPL or Commercial license. I know even the mere mention of "commercial" will cause some people to turn away. And that's fine. Qt is modern, and it's free (since I'm not developing anything commercial.)

If you want "pure" open source, then you might be happier with [wxPython](http://wxPython.org).

## Development and Production

While the Raspberry Pi is a perfectly capable computer, I found developing on a 3.2" TFT a bit cumbersome. So I wanted to use my laptop "for development" and then deploy the code to my Pi when done.

In my case, I found it pretty easy to duplicate the Python/Qt environment on both.

## Installing Python3

It wasn't clear to me if Qt5 worked with the python2.7\. I ran into problems and decided to use python3 instead.

### Mac (aka PC) Instructions

Whether you're on a Mac, Windows, or Linux, get python3 and Qt5 installed. I don't have much to say about Windows or Linux but on the Mac, install python3 and Qt5 with [homebrew](http://brew.sh/).

Simple command:

```
brew install python3
brew install qt5
```

If you know all the directories, you can probably get away without re-installing these. However, I like that with the homebrew install; they can find each other.

Next you need PyQt5, which installation varies by operation system.

On Mac, use homebrew:

```
brew install pyqt5
```

On Linux, check your package manager. The [PyQt5 download page](https://riverbankcomputing.com/software/pyqt/download5) has binaries for Windows.

### Raspberry Pi Instructions

Assuming you're running Raspian Jessie, you can install all three with apt-get:

```
sudo apt-get install python3 python3-pyqt5
```

I'm no apt-get expert, but "python3-pyqt5" might be enough to drag along python3.

## Download QtCreator

On the PC you plan to develop from, download and install QtCreator. You can [download that from Qt](http://www.qt.io/download/), after answering some questions about how you plan to use it.

Believe it or not, I think getting python3, Qt5, and PyQt5 installed is the hardest part. After that, it's just a matter of copy/paste to get a GUI up and running.

## Making a GUI in QtCreator

Start a new project in QtCreator and select "Desktop Application."

[![QtCreator New Project Window](https://www.baldengineer.com/wp-content/uploads/2016/02/01-qtc-new-project-300x198.png)](https://www.baldengineer.com/?attachment_id=5757#main)

Where you save it, is up to you. (_Note, OSX when selecting a directory, the "New Project" window gets hidden. It's behind the initial QtCreator window._) On kit selection, I have been sticking with "Desktop Qt 5.5.1 clang 64bit." No need to select the others. QtCreator is going to try and create a C++ program, but the only thing we are interested in is the MainWindow.ui file. So double-click that to open the GUI Editor.

[![QtCreator Select Kits](https://www.baldengineer.com/wp-content/uploads/2016/02/02-qtc-select-kits-300x173.png)](https://www.baldengineer.com/?attachment_id=5758#main)

Last you need to name the class for your program. For this tutorial, leave it as "MainWindow." That way the name will match up with the Python stuff later.

[![QtCreator Name Classes MainWindow.ui](https://www.baldengineer.com/wp-content/uploads/2016/02/03-qtc-name-class-mainwindow-300x173.png)](https://www.baldengineer.com/?attachment_id=5759#main)

Finally, select none for version control and QtCreator going to create a C++ framework.

[![QtCreator Open MainWindow.UI from Code Window](https://www.baldengineer.com/wp-content/uploads/2016/02/04-qtc-code-window-annotated-300x223.jpg)](https://www.baldengineer.com/?attachment_id=5760#main)

The only thing we are interested in is the MainWindow.ui file. So double-click that to open the GUI Editor. At this point, you're going to add the GUI elements to create whatever GUI you want. I'm not going to go into depth about the different widgets or how they work. Plenty of better tutorials on those.

[![05-qtc-GUI editor](https://www.baldengineer.com/wp-content/uploads/2016/02/05-qtc-GUI-editor-300x199.png)](https://www.baldengineer.com/?attachment_id=5761#main)

Instead, here are the critical things to know when creating your Raspberry Pi GUI.

1. Set the Window to your Screen Size
2. Pay attention to the widget name
3. Set defaults for elements

### 1\. Window Size / Screen Size

In my case, I am using the [3.5" PiTFT](https://www.adafruit.com/products/2097). Using screen means I have 460 by 320 pixels available. This resolution is taking into account LXDE's menubar. So I made sure to set my Window to that size.

### 2\. Widget Name

[![qtc-button-naming](https://www.baldengineer.com/wp-content/uploads/2016/02/qtc-button-naming-300x146.png)](https://www.baldengineer.com/?attachment_id=5762#main)Here's where my hardware background shows. I'm not sure what the right naming convention to use for widget elements. However, 25 years ago when I was forced to learn VisualBasic, I learned the "xxxNamey" convention. In this case, "xxx" is a three letter abbreviation for the widget style and "y" is the numeric element. So for a button it might be "btnOK1" or "btnRedOn1" and a Text Box would be "txtName1".

If you know a better method, let me know. I encourage this one because it makes the code read easier later on.

Don't stick to the default widget names QtCreator provides.

In this code I created two buttons "On" and "Off" named "btnOn" and "btnOff" respectively.

### 3\. Set Defaults

Even if you plan for your program/script to fill in values in the GUI, make sure you set appropriate defaults. For example "PushButton" is a label text for the button. You CAN change in your code, but why not set it now?

## Create the GUI Code

[Download Tutorial Code](https://www.baldengineer.com/wp-content/uploads/2016/02/SampleProgram.zip)

Once you have drawn your GUI elements, it is time to generate the matching Python code. Now, don't worry if your GUI isn't "done." You can repeat the following steps as many times as you want.

In QtCreator, just save your GUI. It will update the MainWindow.ui file. In a terminal (or command shell for Windows), we're going to make use of the pyuic5 utility. On Mac and Unix, here's your command:

```
pyuic5 mainwindow.ui > mainwindow_auto.py
```

Name the result whatever you want, I keep the name mostly the same. Each time you update the GUI in QtCreator, you'll want to run pyuic5 to update the "auto.py" file.

Okay, you're asking, are we done yet? Do we have a GUI? Yes and no. This code will run and create a GUI, but that's not quite enough yet.

## Add Basic Python Code

Create a new Python file in the same directory as your mainwindow_auto.py, with this initialization code. Not that it matters really, but I name my main python a very clever name: "main.py". I know, right?

```
# always seem to need this
import sys

# This gets the Qt stuff
import PyQt5
from PyQt5.QtWidgets import *

# This is our window from QtCreator
import mainwindow_auto

# create class for our Raspberry Pi GUI
class MainWindow(QMainWindow, mainwindow_auto.Ui_MainWindow):
 # access variables inside of the UI's file
 def __init__(self):
 super(self.__class__, self).__init__()
 self.setupUi(self) # gets defined in the UI file

# I feel better having one of these
def main():
 # a new app instance
 app = QApplication(sys.argv)
 form = MainWindow()
 form.show()
 # without this, the script exits immediately.
 sys.exit(app.exec_())

# python bit to figure how who started This
if __name__ == "__main__":
 main()
```

With just those bits, you can run your new Python script and have basic GUI functions. Of course buttons and other widgets won't know what to do yet, but you can click them.

```
python3 main.py
```

And here's my GUI:

[![Raspberry Pi GUI running](https://www.baldengineer.com/wp-content/uploads/2016/02/Raspberry-Pi-GUI-running-1024x785.png)](https://www.baldengineer.com/?attachment_id=5763#main)

### Add Handlers

Remember when I said to pay attention to the widget names you create? This bit of code is why. Now we need to create handlers in the Python code for each button and widget. In your main.py file, you need to add some functions to your MainWindow class.

```
# create class for our Raspberry Pi GUI
class MainWindow(QMainWindow, mainwindow_auto.Ui_MainWindow):
 # access variables inside of the UI's file

 ### functions for the buttons to call
 def pressedOnButton(self):
 print ("Pressed On!")

 def pressedOffButton(self):
 print ("Pressed Off!")

 def __init__(self):
 super(self.__class__, self).__init__()
 self.setupUi(self) # gets defined in the UI file

 ### Hooks to for buttons
 self.btnOn.clicked.connect(lambda: self.pressedOnButton())
 self.btnOff.clicked.connect(lambda: self.pressedOffButton())
```

Replace the MainWindow class from the minimal example before with this code. When it runs a message will be printed on the console with each button press.

In this case, I'm only going to provide button examples. With a little bit of Google-fu, you can find example code for other widget types.

[![PyQt5 with button presses](https://www.baldengineer.com/wp-content/uploads/2016/02/Button-Pressing-300x152.png)](https://www.baldengineer.com/?attachment_id=5765#main)

## Running on startup

Okay if you've already configured the Pi to boot into X-Windows when it starts up, you might want to know how to make your python program run. Well first make sure your "main.py" and "mainwindow_auto.py" files are in the same directory. In my case, I leave them in my "pi" user's home directory.

Edit this file:

```
nano /home/pi/.config/lxsession/LXDE-pi/autostart
```

And add this line:

```
@/usr/bin/python3 /home/pi/main.py
```

Now issue a sudo reboot and it should load. (Note, you can use the "which" command to verify the location of the python3 binary.)

## Download The Entire project

In case you want to download my Qt and Python code, here is a download button.

[Download Tutorial Code](https://www.baldengineer.com/wp-content/uploads/2016/02/SampleProgram.zip)

## Conclusion

As I said, I think the most difficult thing in this tutorial is getting all of the pieces installed--in two places.

That isn't to say creating the proper handlers for the widgets is easy. However, there are tons (and tons) of help on how to build Qt programs--even if it the code is in C++.
