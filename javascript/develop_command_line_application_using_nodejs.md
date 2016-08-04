# Develop command line application using NodeJS

[Original URL](https://codeforgeek.com/2015/09/command-line-application-nodejs/)

> Node.js provides utilities to develop command line application. There are some cool application like express-geenrator and nodemo

[![Develop speedtest command line app using Node](https://codeforgeek.com/wp-content/uploads//2015/09/banner-640x216.jpg?07add7 "Develop speedtest command line app using Node")](https://codeforgeek.com/wp-content/uploads//2015/09/banner.jpg?07add7)

[![Learn Web Design, Coding & More. No Risk. No Contracts. Learn More](https://i.shareasale.com/image/43811/728x902.png)](http://shareasale.com/r.cfm?b=451343&u=1152768&m=43811&urllink=&afftrack=)

Node.js provides utilities to develop command line application. There are some cool application like **express-geenrator** and **[nodemon](http://codeforgeek.com/2014/09/update-code-without-restarting-node-server/)** which runs on command line.

In this tutorial we will learn how to use command line utilities in Node and develop sample internet speed test program using **speedtest.net** API.

![speedtest command line app](https://codeforgeek.com/wp-content/uploads//2015/09/speedtest.gif?07add7) npm install speedtestcli -g ==> Download it using **npm install speedtestcli -g** and test internet speed on your terminal.

[__DOWNLOAD](https://github.com/codeforgeek/speed-test-cli)

## Basic Hello %Your Name% Command line App.

This example will take your name as input and gives you output right in console with greeting. Let's first write a code to print **"Hello World"** in code.

**cli.js**

console.

<span class="me1">log</span>

<span class="br0">(</span>

<span class="st0">“Hello World”</span>

<span class="br0">)</span>

<span class="sy0">;</span>

If you save the code and run it via Node using.

You will be able to see the output in console. But this is not what we want. We want our input go like this.

And it should print

Let's develop this. First create **package.json** using **npm init** command OR you can copy this.

<span class="br0">{</span>

<br>


<span class="st0">“name”</span>

<span class="sy0">:</span>

 

<span class="st0">“HelloWorld”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“version”</span>

<span class="sy0">:</span>

 

<span class="st0">“1.0.0”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“description”</span>

<span class="sy0">:</span>

 

<span class="st0">“”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“author”</span>

<span class="sy0">:</span>

 

<span class="st0">“”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“license”</span>

<span class="sy0">:</span>

 

<span class="st0">“ISC”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“bin”</span>

<span class="sy0">:</span>

 

<span class="br0">{</span>

<br>


<span class="st0">“sayHello”</span>

<span class="sy0">:</span>

 

<span class="st0">“cli.js”</span>

<br>


<span class="br0">}</span>

<span class="sy0">,</span>

<br>


<span class="st0">“dependencies”</span>

<span class="sy0">:</span>

 

<span class="br0">{</span>

<span class="br0">}</span>

<br>

<span class="br0">}</span>

Did you notice this line.

"bin": {<br>
"sayHello": "cli.js"<br>
}

This line tells Node to execute particular file on this command. So its syntax is.

"bin" : {<br>
"your-command" : "file-to-execute"<br>
}

We need to modify cli.js to following.

**cli.js**

console.

<span class="me1">log</span>

<span class="br0">(</span>

<span class="st0">“Hello”</span>

<span class="sy0">,</span>

process.

<span class="me1">argv</span>

<span class="br0">[</span>

<span class="nu0">2</span>

<span class="br0">]</span>

<span class="br0">)</span>

<span class="sy0">;</span>

Save the code and run it using following command.

It will print

On screen.

To make it command line you need to add following in start of the main script.

Above line tell the System that the script should be executed with the first executable named **node** that's found in your current PATH. **/bin/env** is standard Unix utility that looks at your current environment.<br>
So final code should be like this.

**cli.js**

#

<span class="sy0">!/</span>

usr

<span class="sy0">/</span>

bin

<span class="sy0">/</span>

env node<br>
console.

<span class="me1">log</span>

<span class="br0">(</span>

<span class="st0">“Hello”</span>

<span class="sy0">,</span>

process.

<span class="me1">argv</span>

<span class="br0">[</span>

<span class="nu0">2</span>

<span class="br0">]</span>

<span class="br0">)</span>

<span class="sy0">;</span>

Now run following command to install it in your system as a command line application.

This is it. Once it is installed you can access it using following command on terminal.

### Developing Speedtest command line app.

We all know and use **speedtest.net** frequently to check Download and upload speed. They do provide web services too for external integration of speedtest features.

There is node module which wraps the API provided by speedtest.net ( Thanks to @ddsol for this ) called **speedtest-net**. We will be using that to develop our command line application.

Let's begin with Package.json file. Use **npm init** to Generate package.json for your application ( It's a good practice ). You may specify dependencies in the wizard or hit enter to get package.json with basic information.

You can install dependencies using.

npm install chalk progress speedtest-net –save

Here is my package.json.

**package.json**

<span class="br0">{</span>

<br>


<span class="st0">“name”</span>

<span class="sy0">:</span>

 

<span class="st0">“speedTestCLI”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“version”</span>

<span class="sy0">:</span>

 

<span class="st0">“1.0.0”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“description”</span>

<span class="sy0">:</span>

 

<span class="st0">“”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“author”</span>

<span class="sy0">:</span>

 

<span class="st0">“”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“license”</span>

<span class="sy0">:</span>

 

<span class="st0">“ISC”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“bin”</span>

<span class="sy0">:</span>

 

<span class="br0">{</span>

<br>


<span class="st0">“speedtest”</span>

<span class="sy0">:</span>

 

<span class="st0">“index.js”</span>

<br>


<span class="br0">}</span>

<span class="sy0">,</span>

<br>


<span class="st0">“dependencies”</span>

<span class="sy0">:</span>

 

<span class="br0">{</span>

<br>


<span class="st0">“chalk”</span>

<span class="sy0">:</span>

 

<span class="st0">“^1.1.1”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“progress”</span>

<span class="sy0">:</span>

 

<span class="st0">“^1.1.8”</span>

<span class="sy0">,</span>

<br>


<span class="st0">“speedtest-net”</span>

<span class="sy0">:</span>

 

<span class="st0">“^1.0.3”</span>

<br>


<span class="br0">}</span>

<br>

<span class="br0">}</span>

Install dependencies by using following command if you have not done already.

For ease, i select **speedtest** as command. You can change it as per your preference. Let's write some code for main file.

**index.js**

#

<span class="sy0">!/</span>

usr

<span class="sy0">/</span>

bin

<span class="sy0">/</span>

env node<br>

<span class="kw2">var</span>

 speedTest 

<span class="sy0">=</span>

 require

<span class="br0">(</span>

<span class="st0">‘speedtest-net’</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<br>

<span class="kw2">var</span>

 ProgressBar 

<span class="sy0">=</span>

 require

<span class="br0">(</span>

<span class="st0">‘progress’</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<br>

<span class="kw2">var</span>

 chalk 

<span class="sy0">=</span>

 require

<span class="br0">(</span>

<span class="st0">“chalk”</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<span class="kw2">var</span>

 test 

<span class="sy0">=</span>

 speedTest

<span class="br0">(</span>

<span class="br0">{</span>

maxTime

<span class="sy0">:</span>

<span class="nu0">5000</span>

<span class="br0">}</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<span class="kw2">var</span>

 bar

<span class="sy0">,</span>

pingTime

<span class="sy0">;</span>

<span class="co1">// Event triggered when best Server is found.</span>

test.

<span class="me1">on</span>

<span class="br0">(</span>

<span class="st0">‘testserver’</span>

<span class="sy0">,</span>

<span class="kw2">function</span>

<span class="br0">(</span>

server

<span class="br0">)</span>

 

<span class="br0">{</span>

<br>
pingTime 

<span class="sy0">=</span>

 server.

<span class="me1">bestPing</span>

<span class="sy0">;</span>

<br>

<span class="br0">}</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<span class="co1">// Invoke every time when download speed check begin</span>

<br>

<span class="co1">// Return completion value in percentage</span>

test.

<span class="me1">on</span>

<span class="br0">(</span>

<span class="st0">‘downloadprogress’</span>

<span class="sy0">,</span>

<span class="kw2">function</span>

<span class="br0">(</span>

pct

<span class="br0">)</span>

<span class="br0">{</span>

<br>
prog

<span class="br0">(</span>

<span class="st0">‘Checking Download Speed ’</span>

<span class="sy0">,</span>

pct

<span class="br0">)</span>

<span class="sy0">;</span>

<br>

<span class="br0">}</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<span class="co1">// Invoke every time when upload speed check begin</span>

<br>

<span class="co1">// Return completion value in percentage</span>

test.

<span class="me1">on</span>

<span class="br0">(</span>

<span class="st0">‘uploadprogress’</span>

<span class="sy0">,</span>

<span class="kw2">function</span>

<span class="br0">(</span>

pct

<span class="br0">)</span>

<span class="br0">{</span>

<br>
prog

<span class="br0">(</span>

<span class="st0">‘Checking Upload Speed ’</span>

<span class="sy0">,</span>

pct

<span class="br0">)</span>

<span class="sy0">;</span>

<br>

<span class="br0">}</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<span class="co1">// Invoke When final data recieved</span>

test.

<span class="me1">on</span>

<span class="br0">(</span>

<span class="st0">‘data’</span>

<span class="sy0">,</span>

<span class="kw2">function</span>

<span class="br0">(</span>

data

<span class="br0">)</span>

<span class="br0">{</span>

<br>
console.

<span class="me1">log</span>

<span class="br0">(</span>

chalk.

<span class="me1">cyan</span>

<span class="br0">(</span>

<span class="st0">“Ping : ”</span>

<span class="br0">)</span>

<span class="sy0">,</span>

Math.

<span class="me1">abs</span>

<span class="br0">(</span>

pingTime

<span class="br0">)</span>

<span class="sy0">,</span>

chalk.

<span class="me1">dim</span>

<span class="br0">(</span>

<span class="st0">‘ms’</span>

<span class="br0">)</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<br>
console.

<span class="me1">log</span>

<span class="br0">(</span>

chalk.

<span class="me1">cyan</span>

<span class="br0">(</span>

<span class="st0">“Download Speed : ”</span>

<span class="br0">)</span>

 

<span class="sy0">+</span>

 data.

<span class="me1">speeds</span>

.

<span class="me1">download</span>

 

<span class="sy0">+</span>

 chalk.

<span class="me1">dim</span>

<span class="br0">(</span>

<span class="st0">" MBps"</span>

<span class="br0">)</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<br>
console.

<span class="me1">log</span>

<span class="br0">(</span>

chalk.

<span class="me1">cyan</span>

<span class="br0">(</span>

<span class="st0">“Upload Speed : ”</span>

<span class="br0">)</span>

 

<span class="sy0">+</span>

 data.

<span class="me1">speeds</span>

.

<span class="me1">upload</span>

 

<span class="sy0">+</span>

 chalk.

<span class="me1">dim</span>

<span class="br0">(</span>

<span class="st0">" MBps"</span>

<span class="br0">)</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<br>

<span class="br0">}</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<span class="co1">// in case of error, exit.</span>

test.

<span class="me1">on</span>

<span class="br0">(</span>

<span class="st0">‘error’</span>

<span class="sy0">,</span>

<span class="kw2">function</span>

<span class="br0">(</span>

error

<span class="br0">)</span>

<span class="br0">{</span>

<br>
process.

<span class="me1">exit</span>

<span class="br0">(</span>

<span class="nu0">1</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<br>

<span class="br0">}</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<span class="coMULTI">/*<br>    function : prog<br>    params : type and percentage.<br>    task : update the progress bar.<br>*/</span>

<span class="kw2">function</span>

 prog

<span class="br0">(</span>

what

<span class="sy0">,</span>

pct

<span class="br0">)</span>

<span class="br0">{</span>

<br>


<span class="co1">// if its completed, terminate current progress.</span>

<br>


<span class="kw1">if</span>

 

<span class="br0">(</span>

pct

<span class="sy0">&gt;=</span>

<span class="nu0">100</span>

<span class="br0">)</span>

<span class="br0">{</span>

<br>


<span class="kw1">if</span>

 

<span class="br0">(</span>

bar

<span class="br0">)</span>

 bar.

<span class="me1">terminate</span>

<span class="br0">(</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<br>
bar

<span class="sy0">=</span>

<span class="kw2">null</span>

<span class="sy0">;</span>

<br>


<span class="kw1">return</span>

<span class="sy0">;</span>

<br>


<span class="br0">}</span>

<br>


<span class="co1">// if bar object is not created</span>

<br>


<span class="kw1">if</span>

 

<span class="br0">(</span>

<span class="sy0">!</span>

bar

<span class="br0">)</span>

 

<span class="br0">{</span>

<br>


<span class="kw2">var</span>

 green 

<span class="sy0">=</span>

 

<span class="st0">’<span class="es0">\u</span>001b[42m <span class="es0">\u</span>001b[0m’</span>

<span class="sy0">,</span>

<br>
red 

<span class="sy0">=</span>

 

<span class="st0">’<span class="es0">\u</span>001b[41m <span class="es0">\u</span>001b[0m’</span>

<span class="sy0">;</span>

<br>
bar 

<span class="sy0">=</span>

 

<span class="kw2">new</span>

 ProgressBar

<span class="br0">(</span>

<span class="st0">‘ ’</span>

<span class="sy0">+</span>

what

<span class="sy0">+</span>

<span class="st0">‘ [:bar] :percent’</span>

<span class="sy0">,</span>

 

<span class="br0">{</span>

<br>
complete

<span class="sy0">:</span>

 green

<span class="sy0">,</span>

<br>
incomplete

<span class="sy0">:</span>

 red

<span class="sy0">,</span>

<br>
clear

<span class="sy0">:</span>

 

<span class="kw2">true</span>

<span class="sy0">,</span>

<br>
width

<span class="sy0">:</span>

<span class="nu0">40</span>

<span class="sy0">,</span>

<br>
total

<span class="sy0">:</span>

 

<span class="nu0">100</span>

<br>


<span class="br0">}</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<br>


<span class="br0">}</span>

<br>


<span class="co1">// else update the bar with coming value.</span>

<br>
bar.

<span class="me1">update</span>

<span class="br0">(</span>

pct

<span class="sy0">/</span>

<span class="nu0">100</span>

<span class="br0">)</span>

<span class="sy0">;</span>

<br>

<span class="br0">}</span>

Run the program using

Install it in your system using.

And run your specified command in above case **speedtest** on terminal to see output.<br>
![speedtest command line app](https://codeforgeek.com/wp-content/uploads//2015/09/speedtest.gif?07add7) npm install speedtestcli -g

### Conclusion:

We learned how to develop a real-time command line application using Node.js. If you are developing framework OR working with any boilerplate generator application then command line utilities are very useful to you.

### Further reading:

**[Commander](https://www.npmjs.com/package/commander)** – NPM module designed for simple command line application solution.

* We hate spam a lot and there is no question that will do that ever.
