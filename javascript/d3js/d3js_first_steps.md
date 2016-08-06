# D3.js First Steps

[Original URL](https://www.dashingd3js.com/d3js-first-steps)

> HTML Setup For now, you will just use a text file and the web browser. You will start with with a static page of HTML. Then you will add d3.js. Create a folder named d3js_projects. Create an HTML...

## HTML Setup

For now, you will just use a text file and the web browser. You will start with with a static page of HTML. Then you will add d3.js.

Create a folder named **d3js_projects**. Create an HTML file in the folder named **project_1.html**.

Start with a basic HTML webpage:

```
1<!DOCTYPE html>
2<html>
3 <head>
4 </head>
5 <body>
6 <p>Hello!</p>
7 </body>
8</html>
```

Which shows up in the browser:

![Project 1 Browser Snapshot](https://www.dashingd3js.com/assets/project_1_browser_snapshot_600x198-2cba509f305474195e2e1b6ebfeba7a1.png)

## D3.js Setup

To get the main D3.js JavaScript file go to the [D3.js Website](http://d3js.org/). After the first paragraph on the page, you will see a section with links to the latest version. Download the latest version **d3.v2.min.js**. Save this file in the **d3js_projects** folder.

The file **d3.v2.min.js** is saved in the same folder as the HTML file so that it can be referenced it easily. We reference the JavaScript file from the head of the HTML file. Our updated HTML file now looks like this:

```
 1<!DOCTYPE html>
 2<html>
 3 <head>
 4 <script type="text/javascript" src="d3.v2.min.js"></script>
 5 </head>
 6 <body>
 7 <p>Hello!</p>
 8 </body>
 9</html>
```

## Source File Setup Test

To test our D3.js setup we open the inspect element tool kit. In the Element tab of the Webkit Inspector, we open all of the elements so that we can see the whole HTML structure. We then hover over the d3.vs.min.js src.

![Check D3.js Installation](https://www.dashingd3js.com/assets/d3.js.installation.check-f7c889989666e39df3e4810a79b58184.png)

When we click on the link, it takes us to the sources tab. This will show the D3.js minified code.

![D3.js Source Installation Check](https://www.dashingd3js.com/assets/d3.js.source.check-900d3cdb612daeb1230da6fe887a6433.png)

If the last step did not work it probably means that the HTML file and the D3.js JavaScript file are not in the same directory.

## JavaScript Console Setup Test

The last test will take place in the JavaScript Console. This test tells us if D3.js is loaded correctly for our use in the JavaScript Console.

In this snapshot, locate the "Console" tab in the Webkit Inspector:

![Webkit Inspector](https://www.dashingd3js.com/assets/d3.js.installation.check-f7c889989666e39df3e4810a79b58184.png)

When you click on the tab, it will show you a blank screen where you can type and evaluate JavaScript.

![Chrome Webkit Inspector JavaScript Console](https://www.dashingd3js.com/assets/JavaScript_Console_600x170-cc672b9fee143263c59b8e6979dc493e.png)

In the JavaScript console, type the following:

This will cause a popup alert to pop up and say "Hello!". This is what it looks like:

![Chrome Webkit Inspector JavaScript Console Alert Test](https://www.dashingd3js.com/assets/JavaScript_Consoler_Alert_600x335-4d6a58e67ff00f4f73c8c41cd0549cf0.png)

Now to test if D3.js is loaded correctly. Type a lowercase "d3" into the Console followed by a period:

If we have D3.js installed correctly, the following should appear:

![D3.js JavaScript Console Test](https://www.dashingd3js.com/assets/d3.js.javascript.console_300x420-4bbabdbb399f4b1b8fe4b62dee3b2c5b.png)

If all the tests passed and you were able to load D3.js correctly, you are ready to get started.
