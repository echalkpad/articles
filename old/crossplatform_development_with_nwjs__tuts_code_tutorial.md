# Cross-Platform Development With NW.js - Tuts+ Code Tutorial

[Original URL](https://code.tutsplus.com/tutorials/cross-platform-development-with-nwjs--cms-23281)

> More applications are taking advantage of web technologies. For example, Brackets, Peppermint, and Pinegrow are programmer editors made from HTML, JavaScript, and CSS. This allows for using familiar...

More applications are taking advantage of web technologies. For example, [Brackets](http://brackets.io/), [Peppermint](http://osxpeppermint.com/), and [Pinegrow](http://pinegrow.com/) are programmer editors made from HTML, JavaScript, and CSS. This allows for using familiar tools, but also makes applications more cross-platform in nature. In this tutorial, I will show you how to use NW.js for making a simple programmer's editor that you can use in Windows, Mac OS X, and Linux.

## Introduction and Downloading NW.js

[NW.js](http://nwjs.io/), originally known as Node Webkit, is the packaging of Node.js and WebKit HTML render in a package for running local applications. The NW.js version is using [io.js](https://iojs.org/), a more current version of the V8 JavaScript engine with more ES6 support. Since io.js is 100% compatible with the latest Node.js, all libraries and programs using Node.js are usable with io.js as well.

To get started, download all three OS versions from [NW.js](http://nwjs.io/) or just the versions you want to run the project. I will be developing on my MacBook Air, but you can use any system you want. The project is **Fun Editor**: an easy to use, single document code editor. It is built with the Linux motto: A single task done well!

## Getting the Parts

To get started, [node](http://nodejs.org/) or [io.js](https://iojs.org/) has to be installed on your system. Once you install node or io.js, the `npm` command will be on your system. This command is for installing different JavaScript libraries.

The first library is [Bower](http://bower.io/). Type the following in a command line prompt:

```
npm install -g bower
```

On some systems, you might need to use `sudo` before the `npm` command to run it in super user mode.

The `bower` command is a package manager for many web technologies. It gives an easy way to add web related items to your project.

To do custom actions to the DOM, this editor will make use of [Zepto.js](http://zeptojs.com/) JavaScript library instead of jQuery. Since Fun Editor will use the library for DOM work only, Zepto fits the bill at a fraction of the size.

Create a new directory for the project. In the directory with the command line, type the following:

```
bower install less
bower install zepto
```

There will now be a new directory called `bower_components`. In this directory, bower installed the `less` and `zepto` libraries. That was much easier than finding the web site and downloading.

The [Ace](http://ace.c9.io/#nav=about) JavaScript library is the base for this editor. It is a flexible and easy to use editor in JavaScript designed for embedding into web sites. To install, type the following in the terminal in our project directory:

```
git clone git://github.com/ajaxorg/ace.git
```

There is now a new directory `ace`. All of the sources for the library are in this directory. The library needs to be compiled to the compact format to speed up loading. In the command line, type:

```
cd ace
npm install
node Makefile.dryice.js full -m --target ./build
```

These commands put you into the `ace` directory, install all needed libraries for the `ace` editor, and create the minimized library files in the `build` sub-directory.

This project will use the `node-watch` library to know if the file in the editor has changed. This library activates a callback function whenever the specified file changes. To install this library in to our project, the project directory needs to be a node package directory. In the command line in the project directory, type the following:

```
cd ..
npm init
```

The `npm` program will ask several questions about the project. Once you answer the questions, a `package.json` file is in the project directory. The `npm` command will store the names of all libraries installed in this file. That way, giving the project file to someone else enables them to create the same working environment.

Now to install the `node-watch` library, type the following:

```
npm install node-watch --save
```

Once finished, there is a `node_modules` directory with the library inside. The `–save` flag told `npm` to save the library into the project and not globally.

The last piece of software to install is [Emmet](http://emmet.io/). You cannot have a code editor without Emmet. Get the Emmet code from [Emmet on GitHub](https://raw.githubusercontent.com/nightwing/emmet-core/master/emmet.js) and save it to the file `emmet.js` in the `js` directory.

Now that all the pieces are in place in the project folder, everything is ready to put it together!

## Putting It All Together

The first item needed for a NW.js project is its project file. Unfortunately, the node project file uses the same file name. Since the node project file is not needed during development, it can be moved aside until needed again. In the command line in the project directory, type the following:

```
mv package.json node.package.json
touch package.json
```

These commands moved the original `package.json`file to `node.package.json` and created an empty file called `package.json.` In the `package.json`file, type the following:

```
{
 "description": "A very small code editor.",
 "main": "main.html",
 "name": "Fun Editor",
 "version": "1.0",
 "window": {
 "height": 600,
 "width": 650,
 "show": false,
 "title": "Fun Editor",
 "toolbar": false,
 "icon": "icon.png"
 }
}
```

This file tells NW.js how to launch the program. The different fields are:

### **description**

This should be a short description of the program and what it does.

### **main**

This is the main HTML file to open. This file should contain all the HTML for the main page of the program.

### **name**

This is the name of the program.

### **version**

This should be a version number for the program.

### **window**

This is a json array of values to describe the User Interface of the program. This array has the following items:

### **height**

This is a number of pixels for the height of the program window when started.

### **width**

This is the number of pixels for the width of the program window when started.

### **show**

This is a Boolean telling NW.js whether or not to show the main windows upon loading. I have it set to _true_. If you set it to _false_, make sure there is some way to activate it later.

### **title**

This is the default title for the program. It will be used when loaded, until some code in the program changes it.

### **toolbar**

This Boolean tells NW.js whether or not to have a toolbar. Since FunEditor is a minimalistic editor, this is set to _false_. But, if you ever need to use the Dev Tools to debug your editor, you can set this to _true_ and an icon for the Dev Tools will be there.

### **icon**

This is a relative path of the icon to use for the program. The project directory should be the base for the reference to this file.

Since a `main.html` file is in the configuration, it is the next file made. In the project directory, create the `main.html` file and put this code in it:

```
<!DOCTYPE html>
<html>

 <head>
        <title>Fun Editor</title>
        <script type="text/javascript" src="bower_components/zepto/zepto.min.js"></script>
        <script type="text/javascript" src="js/emmet.js"></script>
        <script type="text/javascript" src="ace/build/src-min-noconflict/ace.js"></script>
        <script type="text/javascript" src="ace/build/src-min-noconflict/ext-emmet.js"></script>
        <script type="text/javascript" src="ace/build/src-min-noconflict/ext-language_tools.js"></script>
        <script type="text/javascript" src="ace/build/src-min-noconflict/ext-spellcheck.js"></script>
        <script type="text/javascript" src="ace/build/src-min-noconflict/keybinding-vim.js"></script>
        <script type="text/javascript" src="js/FunEditor.js"></script>
        <link rel="stylesheet/less" type="text/css" href="less/default.less">
        <script type="text/javascript" src="bower_components/less/dist/less.min.js"></script>
    </head>

    <body>
        <div id="editor"></div>

        <div class="info">
            <span id="editMode" class="statuslineitem">Normal</span>
            <span class="title statuslineitem">
            <span class="arrow-right-editMode statuslineitem"></span>
            <span id="title">No file</span>
            </span>
            <span class="mode statuslineitem">
                <span class="arrow-right-title statuslineitem"></span>
            <span id="mode">JavaScript</span>
            </span>
            <span class="linenum statuslineitem">
                <span class="arrow-right-mode statuslineitem"></span>
            <span id="linenum">1</span>
            </span>
            <span class="colnum statuslineitem">
                <span class="arrow-right-linenum statuslineitem"></span>
            <span id="colnum">1</span>
            </span>
            <span class="arrow-right-colnum statuslineitem"></span>
        </div>

        <input style="display:none;" id="openFile" type="file" />
        <input style="display:none;" id="saveFile" type="file" />
    </body>
</html>
```

That is the main program. It is a simple HTML page that is mostly a list of JavaScript files to load, one Less file for styling everything, a div with an `id` of `editor` for the editor, and a div with an `id` of `info` and some spans for doing the status line. I took the styling for the status line from my tutorial on [Getting Spiffy With Powerline](https://computers.tutsplus.com/tutorials/getting-spiffy-with-powerline--cms-20740). It doesn't use Powerline--it just looks like it. At the bottom are two inputs that do not get displayed. These are for the NW.js open file dialog and save file dialog.

Good coding practices would say to load the JavaScript at the end of the page. Since the window for the page is not shown until the program tells it to show, it does not matter.

To make the HTML look right, styling is the next item to create. Create a new directory called `less` in the project directory. Then create the file `default.less` and add this to it:

```
@StatusLineEditMode: rgb(98, 105, 255);
@StatusLineTitle: rgb(98, 247, 255);
@StatusLineMode: rgb(98, 255, 149);
@StatusLineLineNum: rgb(224, 217, 87);
@StatusLineColNum: rgb(87, 212, 224);
@StatusLineBackground: white;

body {
 margin: 0;
 padding: 0;
 margin-top: 0;
 overflow: hidden;
}

#editor {
 position: absolute;
 top: 0px;
 bottom: 25px;
 left: 0;
 right: 0;
 margin: 0px;
 padding: 0px;
}

.info {
 position: absolute;
 display: inline;
 bottom: 0px;
 font-family: monospace;
 white-space: nowrap;
 bottom: 0;
 background: @StatusLineBackground;
 padding: 0px;
 height: 25px;
 left: 0;
 width: 100%;
}

.statuslineitem {
 height: 25px;
 line-height: 25px;
 text-align: center;
 vertical-align: middle;
 margin: 0px;
 padding-top: 0px;
 padding-bottom: 0px;
 padding-right: 3px;
 padding-left: 0px;
 border: 0px;
 float: left;
}

.arrow-right-editMode {
 width: 0;
    height: 0;
    border-top: 13px solid transparent;
    border-bottom: 13px solid transparent;

    border-left: 13px solid @StatusLineEditMode;
    margin: 0px;
    padding: 0px;
    margin-left: 0px;
}

.arrow-right-title {
    width: 0;
    height: 0;
    border-top: 13px solid transparent;
    border-bottom: 13px solid transparent;

    border-left: 13px solid @StatusLineTitle;
    margin: 0px;
    padding: 0px;
    margin-left: 0px;
}

.arrow-right-mode {
    width: 0;
    height: 0;
    border-top: 13px solid transparent;
    border-bottom: 13px solid transparent;

    border-left: 13px solid @StatusLineMode;
    margin: 0px;
    padding: 0px;
    margin-left: 0px;
}

.arrow-right-linenum {
    width: 0;
    height: 0;
    border-top: 13px solid transparent;
    border-bottom: 13px solid transparent;

    border-left: 13px solid @StatusLineLineNum;
    margin: 0px;
    padding: 0px;
    margin-left: 0px;
}

.arrow-right-colnum {
    width: 0;
    height: 0;
    border-top: 13px solid transparent;
    border-bottom: 13px solid transparent;

    border-left: 13px solid @StatusLineColNum;
    margin: 0px;
    padding: 0px;
}

#editMode {
 background: @StatusLineEditMode;
 padding-left: 5px;
}

#title {
 background: @StatusLineTitle;
 padding-left: 5px;
}

#mode {
 background: @StatusLineMode;
 padding-left: 5px;
}

#linenum {
 background: @StatusLineLineNum;
 padding-left: 5px;
}

#colnum {
 background: @StatusLineColNum;
 padding-left: 5px;
}

#editMode {
 background: @StatusLineEditMode;
 padding-left: 5px;
}

.title {
 background: @StatusLineTitle;
}

.mode {
 background: @StatusLineMode;
}

.linenum {
 background: @StatusLineLineNum;
}

.colnum {
 background: @StatusLineColNum;
}
```

This styling information is to make the editor div position absolute and take up all the browser window area except for 25 px at the bottom. That area is for the status line.

At the top of the file, you will notice several Less variable definitions. Since the same colors get used in more than one place (i.e. the span for line number and its "arrow" span afterwards), I made them into a Less variable. That way, I only have to change one item to change all of them. Stylus or SASS could be used as well, but Less allows for dynamically changing them in the code. Great for theme changing down the road.

Now for the main program file. In the `js` directory, create a file called `FunEditor.js` and place the following code:

```
//
// Program:     Fun Editor
//
// Description:     This is a basic editor built using NW.js.
//          This is more of a project to learn how to use
//          NW.js, but I am finding that I really like
//          the editor!
//
// Author:      Richard Guay (raguay@customct.com)
// License:         MIT
//

//
// Class:       FunEditor
//
// Description:     This class contains the information and functionality
//          of the Fun Editor. There should be only one instance
//          of this class per editor.
//
// Class Variables:
//                  editor      Keeps the Editor object
//                  menu        keeps the menu object for the pop-up
//                              menu.
//                  menuEdit        Edit menu for the popup menu
//                  menuEditMain    The Edit menu for the main menu
//                  menuFile        File menu for the popup menu
//                  menuFileMain    File menu for the main menu
//                  nativeMenuBar   The menu bar for OSX Main menu
//                  hasWriteAccess  boolean for if the file is
//                                  writable or not.
//                  origFileName    Last file name opened.
//                  watch       The node-watch library object.
//                  osenv       The osenv library object.
//                  gui             The NW.js gui library
//                              object.
//                  fs          The fs library object.
//                  clipboard       The clipboard library object.
//
function FunEditor() {}

FunEditor.prototype.filesOpened = 0;
FunEditor.prototype.saving = false;
FunEditor.prototype.editor = null;
FunEditor.prototype.menu = null;
FunEditor.prototype.menuEdit = null;
FunEditor.prototype.menuFile = null;
FunEditor.prototype.menuEditMain = null;
FunEditor.prototype.menuFileMain = null;
FunEditor.prototype.nativeMenuBar = null;
FunEditor.prototype.fileEntry = null;
FunEditor.prototype.hasWriteAccess = false;
FunEditor.prototype.origFileName = "";
FunEditor.prototype.theme = {};
FunEditor.prototype.lastCursor = { line: 0, col: 0 };
FunEditor.prototype.watch = require("node-watch");
FunEditor.prototype.gui = require("nw.gui");
FunEditor.prototype.fs = require("fs");
FunEditor.prototype.osenv = require("osenv");
FunEditor.prototype.os = require("os");

var FE = new FunEditor();

FunEditor.prototype.clipboard = FE.gui.Clipboard.get();
FunEditor.prototype.win = FE.gui.Window.get();

//
// Function:    handleDocumentChange
//
// Description:     This function is called whenever the document
//          is changed. This function will get the title set,
//          remove the old document name from the window
//          list, set the syntax highlighting based on the
//          file extension, and update the status line.
//
// Inputs:
//          title   Title of the new document
//
FunEditor.prototype.handleDocumentChange = function(title) {
     //
     // Setup the default syntax highlighting mode.
     //
     var mode = "ace/mode/javascript";
     var modeName = "JavaScript";

     //
     // Set the new file name. If the title is blank, reflect that for
     // setting a new one later.
     //
     this.fileEntry = title;

     //
     // Set the syntax highlighting based on the file ending.
     //
     if (title) {
        //
        // Set up file watching with node-watch. The file being edited is
        // watched for changes. If the file changes, then reload the file
        // into the editor.
        //
        this.watch(this.fileEntry, function() {
            //
            // The file changed. Load it into the editor. Needs implemented:
            // ask the user if they want the file to be reloaded.
            //
            if(! FE.saving ) {
                FE.readFileIntoEditor(FE.fileEntry);
            }
        });

        //
        // If there is a title, then setup everything by that title.
        // The title will be the file name.
        //
        if(this.os.platform() == "win32") {
         title = title.match(/[^\\]+$/)[0];
        } else {
         title = title.match(/[^/]+$/)[0];
        }
        if (this.origFileName.indexOf(title) == -1) {
            //
            // Remove whatever the old file was loaded and put in
            // the new file.
            //
            this.origFileName = title;
        }

        //
        // Check for OS permissions for writing. NOTE: Not implemented.
        //
        this.hasWriteAccess = true;

        //
        // Set the document's title to the file name.
        //
        document.getElementById("title").innerHTML = title;
        document.title = title;

        //
        // Set the syntax highlighting mode based on extension of the file.
        //
        if (title.match(/\.js$/)) {
            mode = "ace/mode/javascript";
            modeName = "JavaScript";
        } else if (title.match(/\.html$/)) {
            mode = "ace/mode/html";
            modeName = "HTML";
        } else if (title.match(/\.css$/)) {
            mode = "ace/mode/css";
            modeName = "CSS";
        } else if (title.match(/\.less$/)) {
            mode = "ace/mode/less";
            modeName = "LESS";
        } else if (title.match(/\.md$/)) {
            mode = "ace/mode/markdown";
            modeName = "Markdown";
        } else if (title.match(/\.ft$/)) {
            mode = "ace/mode/markdown";
            modeName = "FoldingText";
        } else if (title.match(/\.markdown$/)) {
            mode = "ace/mode/markdown";
            modeName = "Markdown";
        } else if (title.match(/\.php$/)) {
            mode = "ace/mode/php";
            modeName = "PHP";
        }
    } else {
        //
        // Setting an empty document. Leave syntax highlighting as the last
        // file.
        //
        document.getElementById("title").innerHTML = "[no document loaded]";
        this.origFileName = "";
    }

    //
    // Tell the Editor and setup the status bar with the syntax highlight mode.
    //
    this.editor.getSession().setMode(mode);
    document.getElementById("mode").innerHTML = modeName;
};

//
// Function: setCursorLast
//
// Description: Set the cursor to the last stored state.
//
FunEditor.prototype.setCursorLast = function() {
 this.editor.moveCursorTo(this.lastCursor.line, this.lastCursor.col);
}

//
// Function:        newFile
//
// Description:     This function is called to set the global
//          variables properly for a new empty file.
//
// Inputs:
//
FunEditor.prototype.newFile = function() {
    this.fileEntry = null;
    this.hasWriteAccess = false;
    this.handleDocumentChange(null);
    this.editor.setValue("");
};

//
// Function:    readFileIntoEditor
//
// Description:     This function handles the reading of the file
//          contents into the editor. If reading fails, a
//          log entry is created.
//
// Inputs:
//          theFileEntry    The path and file name
//
FunEditor.prototype.readFileIntoEditor = function(theFileEntry) {
    this.fs.readFile(theFileEntry, function(err, data) {
        if (err) {
            console.log("Error Reading file.");
        }

        //
        // Set the file properties.
        //
        FE.handleDocumentChange(theFileEntry);

        //
        // Set the file contents.
        //
        FE.editor.setValue(String(data));

        //
        // Remove the selection.
        //
        FE.editor.session.selection.clearSelection();

        //
        // Put the cursor to the last know position.
        //
        FE.setCursorLast();
    });
};

//
// Function:    writeEditorToFile
//
// Description:     This function takes what is in the editor
//          and writes it out to the file.
//
// Inputs:
//          theFileEntry    File to write the contents to.
//
FunEditor.prototype.writeEditorToFile = function(theFileEntry) {
    var str = this.editor.getValue();
    this.fs.writeFile(theFileEntry, str, function(err) {
        if (err) {
            console.log("Error Writing file.");
            return;
        }
    });
};

//
// Function:    copyFunction
//
// Description:     This function takes the current selection and copies it
//          to the clipboard.
//
FunEditor.prototype.copyFunction = function() {
    this.clipboard.set(this.editor.getCopyText());
};

//
// Function:    cutFunction
//
// Description:     This function cuts out the current selection and copies it
//          to the clipboard.
//
FunEditor.prototype.cutFunction = function() {
    this.clipboard.set(this.editor.getCopyText());
    this.editor.session.replace(this.editor.selection.getRange(),"");
};

//
// Function:    pasteFunction
//
// Description:     This function takes the clipboard and pastes it to the
// current location.
//
FunEditor.prototype.pasteFunction = function() {
    this.editor.session.replace(this.editor.selection.getRange(), this.clipboard.get());
};

//
// Function:    openFile
//
// Description:     This function opens the select a file dialog for opening
//          into the editor.
//
FunEditor.prototype.openFile = function() {
    $("#openFile").trigger("click");
};

//
// Function:    saveFile
//
// Description:     This function saves to the currently open file or
//          opens the save file dialog.
//
FunEditor.prototype.saveFile = function() {
 this.saving = true;
    if (this.fileEntry && this.hasWriteAccess) {
        this.writeEditorToFile(this.fileEntry);
    } else {
        $("#saveFile").trigger("click");
    }
    this.saving = false;
};

//
// Function:    initMenus
//
// Description: This function setups the right click menu and system used
//          in the editor.
//
// Inputs:
//
FunEditor.prototype.initMenus = function() {
    this.menu = new this.gui.Menu();
    this.menuFile = new this.gui.Menu();
    this.menuEdit = new this.gui.Menu();
    this.menuFile.append(new this.gui.MenuItem({
        label: "New",
        click: function() {
            FE.newFile();
        }
    }));
    this.menuFile.append(new this.gui.MenuItem({
        label: "Open",
        click: function() {
            FE.openFile();
        }
    }));
    this.menuFile.append(new this.gui.MenuItem({
        label: "Save",
        click: function() {
            FE.saveFile();
        }
    }));

    this.menuEdit.append(new this.gui.MenuItem({
        label: "Copy",
        click: function() {
            FE.copyFunction();
        }
    }));
     this.menuEdit.append(new this.gui.MenuItem({
         label: "Cut",
         click: function() {
            FE.cutFunction();
         }
     }));
     this.menuEdit.append(new this.gui.MenuItem({
         label: "Paste",
         click: function() {
            FE.pasteFunction();
         }
     }));

     this.menuFileMain = new this.gui.Menu();
     this.menuEditMain = new this.gui.Menu();
     this.menuFileMain.append(new this.gui.MenuItem({
         label: "New",
         click: function() {
            FE.newFile();
         }
     }));
     this.menuFileMain.append(new this.gui.MenuItem({
         label: "Open",
         click: function() {
            FE.openFile();
         }
     }));
     this.menuFileMain.append(new this.gui.MenuItem({
        label: "Save",
        click: function() {
            FE.saveFile();
        }
     }));

     this.menuEditMain.append(new this.gui.MenuItem({
        label: "Copy",
        click: function() {
            FE.copyFunction();
        }
     }));
     this.menuEditMain.append(new this.gui.MenuItem({
        label: "Cut",
        click: function() {
            FE.cutFunction();
        }
     }));
     this.menuEditMain.append(new this.gui.MenuItem({
        label: "Paste",
        click: function() {
            FE.pasteFunction();
        }
     }));

     this.menu.append(new this.gui.MenuItem({
        label: "File",
        submenu: FE.menuFile
     }));

     this.menu.append(new this.gui.MenuItem({
        label: "Edit",
        submenu: FE.menuEdit
     }));

     //
     // Create the main Mac menu also.
     //
     this.nativeMenuBar = new this.gui.Menu({
        type: "menubar"
     });

     if(this.os.platform() == "darwin") {
         this.nativeMenuBar.createMacBuiltin("Fun Editor", {
            hideEdit: true,
            hideWindow: true
         });
    }

     this.nativeMenuBar.append(new this.gui.MenuItem({
        label: "File",
        submenu: FE.menuFileMain
     }));

     this.nativeMenuBar.append(new this.gui.MenuItem({
        label: "Edit",
        submenu: FE.menuEditMain
     }));
     this.win.menu = this.nativeMenuBar;

    //
    // Add the menu to the contextmenu event listener.
    //
    document.getElementById("editor").addEventListener("contextmenu",
        function(ev) {
            ev.preventDefault();
            FE.menu.popup(ev.x, ev.y);
            return false;
        }
    );
};

//
// Function:        onChosenFileToOpen
//
// Description:     This function is called whenever a open
//          file dialog is closed with a file selection.
//          This is an automatically made function in
//          NW.js that needs to be set by your app.
//
// Inputs:
//          theFileEntry        The path to the file selected.
//
onChosenFileToOpen = function(theFileEntry) {
    FE.readFileIntoEditor(theFileEntry);
};

//
// Function:        onChosenFileToSave
//
// Description:     When a file is selected to save into, this
//          function is called. It is originally set by
//          NW.js.
//
// Inputs:
//          theFileEntry        The path to the file selected.
//
onChosenFileToSave = function(theFileEntry) {
     FE.writeEditorToFile(theFileEntry);
};

//
// Function:        onload
//
// Description:     This function is setup by NW.js to be
//                  called when the page representing the application
//                  is loaded. The application overrides this by
//                  assigning it's own function.
//
//                  Here, we initialize everything needed for the
//                  Editor. It also loads the initial document for
//                  the editor, any plugins, and theme.
//
// Inputs:
//
onload = function() {

     //
     // Initialize the context menu.
     //
     FE.initMenus();

     //
     // Set the change function for saveFile and openFile.
     //
     $("#saveFile").change(function(evt) {
        onChosenFileToSave($(this).val());
    });
    $("#openFile").change(function(evt) {
        onChosenFileToOpen($(this).val());
    });

    FE.editor = ace.edit("editor");
    FE.editor.$blockScrolling = Infinity;
    FE.editor.setTheme("ace/theme/solarized_dark");
    FE.editor.getSession().setMode("ace/mode/javascript");
    FE.editor.setKeyboardHandler("ace/keyboard/vim");
    FE.editor.setOption("enableEmmet", true);
    FE.editor.setOption("selectionStyle","text");
    FE.editor.setOption("highlightActiveLine",true);
    FE.editor.setOption("cursorStyle","slim");
    FE.editor.setOption("autoScrollEditorIntoView",true);
    FE.editor.setOption("tabSize",4);
    FE.editor.setOption("enableSnippets",true);
    FE.editor.setOption("spellcheck",true);
    FE.editor.setOption("wrap",true);
    FE.editor.setOption("enableBasicAutocompletion",true);
    FE.editor.setOption("enableLiveAutocompletion",false);
    FE.editor.commands.addCommand({
        name: "myCopy",
        bindKey: {win: "Ctrl-C", mac: "Command-C"},
        exec: function(editor) {
            FE.copyFunction();
        },
        readOnly: false
    });
    FE.editor.commands.addCommand({
        name: "myPaste",
        bindKey: {win: "Ctrl-V", mac: "Command-V"},
        exec: function(editor) {
            FE.pasteFunction();
        },
        readOnly: false
    });
    FE.editor.commands.addCommand({
        name: "myCut",
        bindKey: {win: "Ctrl-X", mac: "Command-X"},
        exec: function(editor) {
            FE.cutFunction();
        },
        readOnly: false
    });
    FE.editor.commands.addCommand({
        name: "mySave",
        bindKey: {win: "Ctrl-S", mac: "Command-S"},
        exec: function(editor) {
            FE.saveFile();
        },
        readOnly: false
    });

    //
    // Tie into the Vim mode save function. FE one took some digging to find!
    //
    FE.editor.state.cm.save = function() {
        FE.saveFile();
    }

    //
    // Setup on events listeners. The first one is listen for cursor
    // movements to update the position in the file in the status line.
    // Next, setup the listener for Vim mode changing to update the
    // status line. Lastly, run function on window closing to remove
    // the current file from the open file list.
    //
    FE.editor.on("changeStatus", function() {
        //
        // Get the current cursor to set the row and column.
        //
        var cursor = FE.editor.selection.lead;
        document.getElementById("linenum").innerHTML = cursor.row + 1;
        document.getElementById("colnum").innerHTML = cursor.column + 1;

        //
        // Save a copy of the cursor location.
        //
        FE.lastCursor.line = cursor.row;
        FE.lastCursor.col = cursor.column;

        //
        // Get the text mode to set the Normal, Visual, or Insert vim
        // modes in the status line.
        //
        var mode = FE.editor.keyBinding.getStatusText(editor);
        if (mode == "") {
            document.getElementById("editMode").innerHTML = "Normal";
        } else if (mode == "VISUAL") {
            document.getElementById("editMode").innerHTML = "Visual";
        } else if (mode == "INSERT") {
            document.getElementById("editMode").innerHTML = "Insert";
        }
    });

    //
    // Capture the Ace editor's copy and paste signals to get
    // or put to the system clipboard.
    //
    FE.editor.on("copy",function(text) {
        FE.clipboard.set(text);
    });
    FE.editor.on("paste", function(e) {
        e.text = FE.clipboard.get();
    });

    //
    // Capture the window close and make
    // sure the file has been saved.
    //
     FE.win.on("close", function() {
        //
        // Make sure the contents are saved.
        //
        if (this.fileEntry && this.hasWriteAccess) {
            FE.saveFile();
         }

        //
        // Close the program.
        //
        this.close(true);
     });

     //
     // Setup for having a new empty file loaded.
     //
     FE.newFile();
     onresize();

     //
     // Show the program and set the focus (focus does not work!).
     //
     FE.win.show();
     FE.win.focus();
};

//
// Function:        onresize
//
// Description:     Another NW.js function that is called every time
//          the application is resized.
//
// Inputs:
//
onresize = function() {
};
```

At the top of the file, you will see the declaration of the FunEditor object and some variables. After the variables, there are three `require` statements. These statements load the `node-watch` library for watching a file change, the `nw.gui` library which allows for interaction with the graphical user interface, the `fs` library which allows access to the file system, and the `os` library for working with the operating system. The `gui`, `fs`, and `os` libraries are part of the NW.js program, while the `node-watch` is a `npm` downloaded library_**_.

With the `gui` library, the `FunEditor.clipboard` variable contains the clipboard object for accessing the system clipboard. Likewise, the `FunEditor.win` variable contains the window object for the main windows created by NW.js for this program.

After the libraries and global variables are loaded, several helper functions are created. All functions that start with `FunEditor` are functions for running the editor and are not specific to NW.js. The other functions are required by NW.js.

I described each function here:

### FunEditor.handleDocumentChange

This function loads the editor with the correct syntax highlighting based on the document loaded. It also sets the window title and the document name in the status line. When the window title is set, it checks the operating system being used with `FunEditor.os.platform()`. If it is a Windows operating system, the search for the last directory separator has to be different since Windows uses `\`, while OS X and Linux use `/`.

This function also sets up file watching using `FunEditor.watch`. When the file given to the function is changed, the callback function reloads the file in to the editor.

### FunEditor.setCursorLast

This function will set the cursor to the last saved location. The location is saved every time the cursor is moved and recorded in the status line. Whenever the file is reloaded after a changed, this function is used to put the cursor back to the last known location.

### FunEditor.newFile

This function is for creating a new, blank file.

### FunEditor.readFileIntoEditor

This function uses the `FunEditor.fs` library reference variable to read in the file from the file system and place it into the editor. It then clears the selection and puts the cursor at the last known location.

### FunEditor.writeEditorToFile

This function takes the current state of the editor and saves it to the file system using the `FunEditor.fs` variable.

### FunEditor.copyFunction

This function uses the NW.js library to take the currently selected text and put it in to the system clipboard.

### FunEditor.cutFunction

This function is the same as `FunEditor.copyFunction`, except that it deletes the selected text from the editor.

### FunEditor.pasteFunction

This function takes the clipboard contents using the NW.js library for the clipboard and pastes it to the current cursor location in the editor.

### FunEditor.openFile

This function opens the system open file dialog by triggering a click on the hidden input element. This is particular to NW.js as well.

### FunEditor.saveFile

This function saves the editor contents to the file if it was loaded from a file and that file has write access. Otherwise, it opens the system save as dialog for the user to select a file. This also works by triggering a click on the hidden `saveFile` element.

### FunEditor.initMenus

This function creates the graphical menu elements for an in-context menu and the system menu. This makes use of the `FunEditor.gui` library.

The main menu for Mac OS X has added functionality. In that section, the `FunEditor.os` library tells the software platform. If it is Mac OS X, then perform the extra functions needed for the main menu.

### Onchosenfiletoopen

This NW.js defined function gets the file selected when the **Open** file dialog gets closed. This function will then load that file into the editor using the `FunEditor.readFileIntoEditor` function.

### Onchosenfiletosave

This NW.js defined function gets the file selected when the **Save As** file dialog gets closed. This function will then save the editor contents to the given file using the `FunEditor.writeEditorToFile` function.

### Onload

This NW.js function is called whenever everything defined in the `main.html` file gets loaded into NW.js. It's equivalent to the `document.onload = function(){};` or the jQuery `$(document).ready(funcition(){});`.

This function gets the editor ready with the desired preferences. The theme is solarized dark, the keyboard to vim layout, active line highlighting, etc. The editor keyboard shortcuts for copy, paste, cut, and save are set with their Windows and Mac defaults. The ace editor takes care of cross-platform issues.

Once the editor is initialized, listeners for the `changeStatus`, `copy`, `paste`, and `save` editor events are set up. These allow the editor to enforce the use of the system clipboard inside of the ace editor and update the line and column information in the status line. The `changeStatus` listener updates the vim mode and current cursor location.

I also dug into the save function that Vim mode has (`:w`). This took some work, but I finally figured it out as well. Ace editor borrows code from Code Mirror to create the Vim keyboard layout. It works, but is not a robust solution.

### Onresize

This NW.js defined function gets called whenever the window is resized. For the FunEditor, nothing needs to be done.

## Running On Different Platforms

That's all the programming for the editor. Now, to run it on each platform. If you haven't already downloaded the NW.js for each platform, do it now and follow the instructions for each platform. Take all the files in the project directory and compress them in to a zip archive. When done, change the name of the archive to `FunEditor.nw`.

On every platform, you can always go to the command line and run the _**_ `nw` command in the project directory. Unfortunately, the basic install does not put the executable file in the system path. Therefore, depending on the shell you are using, you can create an `nw` alias to the executable file. While developing, I use this in the project directory to test:

```
nw .
```

Once I have the `FunEditor.nw` created, I use:

```
nw FunEditor.nw
```

Once verified working, I start the packaging for my other systems.

## Mac

Once NW.js gets installed on the Mac, you will have the `nwjs.app` file in your **Applications** directory. In the directory in which you compressed the application, you can run the program with the following command line:

```
/Applications/nwjs.app/Contents/MacOS/nwjs FunEditor.nw
```

To make a clickable package, change the `FunEditor.nw` file to `app.nw`. Copy the `/Applications/nwjs.app` to `/Applications/FunEditor.app`. In **Finder**, right click on the application and select **Show Contents**. Place the `app.nw` file in the **Resources** directory and change the icon to one that you like. I include one in the download. Remember to keep the `nw.icns` file name.

To get the proper name in the menu, you will have to change the `info.plist` file. Open it up and change the contents to:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
 <key>BuildMachineOSBuild</key>
    <string>12F45</string>
    <key>CFBundleDevelopmentRegion</key>
    <string>en</string>
    <key>CFBundleDisplayName</key>
    <string>FunEditor</string>
    <key>CFBundleExecutable</key>
    <string>nwjs</string>
    <key>CFBundleIconFile</key>
    <string>nw.icns</string>
    <key>CFBundleIdentifier</key>
    <string>io.nwjs.nw</string>
    <key>CFBundleInfoDictionaryVersion</key>
    <string>6.0</string>
    <key>CFBundleName</key>
    <string>FunEditor</string>
    <key>CFBundlePackageType</key>
    <string>APPL</string>
    <key>CFBundleShortVersionString</key>
    <string>1.0</string>
    <key>CFBundleVersion</key>
    <string>1.0</string>
    <key>DTSDKBuild</key>
    <string>12F37</string>
    <key>DTSDKName</key>
    <string>macosx10.8</string>
    <key>DTXcode</key>
    <string>0511</string>
    <key>DTXcodeBuild</key>
    <string>5B1008</string>
    <key>LSFileQuarantineEnabled</key>
    <false/>
    <key>LSMinimumSystemVersion</key>
    <string>10.6.0</string>
    <key>NSPrincipalClass</key>
    <string>NSApplication</string>
    <key>NSSupportsAutomaticGraphicsSwitching</key>
    <true/>
    <key>SCMRevision</key>
    <string>df30fb73b312044486237d93cf96f3606862f2a3</string>
</dict>
</plist>
```

I took the `info.plist` file for NW.js, removed everything that is specific for it launching files with the `nw` extension, changed the version to 1.0, and changed the name to `FunEditor`.

![FunEditor on Mac OS X](https://cms-assets.tutsplus.com/uploads/users/71/posts/23281/image/Cross-PlatformDevNodeWebkit-02.jpg) FunEditor on Mac OS X Once that is done, you have a clickable application for **FunEditor**.

## Windows

The easiest way to run the editor on Windows is to use a batch file. Create a file `FunEditor.bat` somewhere in your system path. Type the following into the file:

```
<path to nw.exe>\nw.exe <path to FunEditor>\FunEdit.nw
```

![FunEditor on Windows 7](https://cms-assets.tutsplus.com/uploads/users/71/posts/23281/image/Cross-PlatformDevNodeWebkit-03.jpg) FunEditor on Windows 7<br>
When you double click the batch file, the FunEditor will open.

## Linux

Once you have the NW.js program downloaded, make sure the directory with it is on your path. Create a script file that will call the NW.js program with your program. Create a file in your path called `FunEditor` with this:

```
#!/usr/bin/bash
nwjs="<path to nwjs directory>/nwjs";
fe="<path to the FunEditor.nw file>";

$nwjs $fe
```

Save and set to executable permissions with:

```
chmod a+x FunEditor
```

If you run that command now, it should launch the editor for Linux!

![FunEditor on Arch Linux](https://cms-assets.tutsplus.com/uploads/users/71/posts/23281/image/Cross-PlatformDevNodeWebkit-01.jpg) FunEditor on Arch Linux On my Arch Linux, FunEditor is fun to use!

## Conclusion

With this project, you learned how to take advantage of [NW.js](http://nwjs.io/) to create an editor runnable on Windows, Linux, and Mac OS X. This project has much room for improvements. Have fun making this little editor into your dream editor and use it on every operating system!
