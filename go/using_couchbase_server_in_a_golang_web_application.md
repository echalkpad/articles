# Using Couchbase Server In A GoLang Web Application

[Original URL](https://www.thepolyglotdeveloper.com/2016/08/using-couchbase-server-golang-web-application/)

> Not too long ago I wrote an article regarding how to create a RESTful API using the Go programming language, but in it I only used mock data rather than an actual database. What happens if we...

Not too long ago I wrote an article regarding how to [create a RESTful API using the Go programming language](https://www.thepolyglotdeveloper.com/2016/07/create-a-simple-restful-api-with-golang/), but in it I only used mock data rather than an actual database. What happens if we want to use a database with GoLang? What database, or more importantly, what kind of database should be used? Most APIs transfer data in JSON format, so it might make sense to store data in the same format. This means that a relational database might not make sense. Instead, NoSQL databases fit quite well when it comes to RESTful APIs. A popular NoSQL database that stores data in JSON format is the open source [Couchbase Server](http://www.couchbase.com).

We're going to take a look at how to include Couchbase Server into our RESTful web application written in the [Go](https://golang.org/) programming language.

The RESTful API example I wrote previously is going to be the foundation to this tutorial, so if you haven't checked it out, I definitely recommend you do. However, you won't be completely lost if you don't as I'm going to try to relay all the necessary information.

At this point I'm going to assume you have GoLang installed and configured on your machine.

## Creating a New Project with Dependencies

We're going to create a new GoLang project for simplicity. Using the Terminal (Mac and Linux) or Command Prompt (Windows), execute the following:

<span class="crayon-title">Create new GoLang Project</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-r">mkdir</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">p</span>
  <span class="crayon-v">$GOPATH</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">src</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">github</span>
  <span class="crayon-e">.com</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">nraboy</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">cbproject</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Essentially I just created those directories with the command above. If the command doesn't work on your operating system, create them manually.

Inside our project directory, we're going to be working with a file called **main.go**. It will be the only file in our project. Before we start coding, we should probably download any project dependencies.

From the Command Prompt or Terminal, execute the following:

<span class="crayon-title">Get External GoLang Dependencies</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">go </span>
  <span class="crayon-r">get</span>
  <span class="crayon-v">github</span>
  <span class="crayon-e">.com</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">satori</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">go</span>
  <span class="crayon-e">.uuid</span>
</p>
  <p>
  <span class="crayon-e">go </span>
  <span class="crayon-r">get</span>
  <span class="crayon-v">github</span>
  <span class="crayon-e">.com</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">couchbase</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">gocb</span>
</p>
  <p>
  <span class="crayon-e">go </span>
  <span class="crayon-r">get</span>
  <span class="crayon-v">github</span>
  <span class="crayon-e">.com</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">gorilla</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">mux</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The dependencies above include a package for generating UUID values that we'll use for document ids, the Couchbase Go SDK, and Mux for expanding the routing capabilities of our HTTP server.

## Configuring Couchbase Server for the GoLang Project

Before we can start coding we need to make sure Couchbase Server is configured and ready for use. We won't be seeing how to install Couchbase Server. If you are using a [Mac](https://www.youtube.com/watch?v=L1wNemXGd9Q), [Linux](https://www.youtube.com/watch?v=z8BcuXDJrDY), or [Windows](https://www.youtube.com/watch?v=dZpnANelV2M) machine, you can see one of the available getting started guides for installation. Instead we'll be configuring a bucket for storing all the NoSQL documents for our application.

The goal here is to create a Couchbase bucket called **restful-sample** that has appropriate indexes for performing [N1QL](http://www.couchbase.com/n1ql) queries via our application code.

Inside the Couchbase administrative dashboard, choose **Data Buckets** and then choose **Create New Data Bucket**. Give the new bucket a name and define a storage capacity.

[![Couchbase Server Create Bucket](https://www.thepolyglotdeveloper.com/wp-content/uploads/2016/07/couchbase-create-bucket.gif)](https://www.thepolyglotdeveloper.com/wp-content/uploads/2016/07/couchbase-create-bucket.gif)

Next we need to create indexes for this new bucket.

When it comes to creating indexes there are a few ways to do this. If you're using Couchbase 4.5 or higher you can use the Query Workbench. In the Query Workbench execute the following query:

<span class="crayon-title">Create Primary Index for N1QL Queries</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">CREATE </span>
  <span class="crayon-e">PRIMARY </span>
  <span class="crayon-e">INDEX </span>
  <span class="crayon-i">ON</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;restful&lt;/span&gt;&lt;span class="crayon-o"&gt;-&lt;/span&gt;&lt;span class="crayon-v"&gt;sample&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
  <span class="crayon-e">USING </span>
  <span class="crayon-v">GSI</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

At least one index is required to use N1QL. More indexes that have deeper complexity than the one I demonstrated will result in faster querying.

[![Couchbase Server Create Primary Index](https://www.thepolyglotdeveloper.com/wp-content/uploads/2016/07/couchbase-create-primary-index.gif)](https://www.thepolyglotdeveloper.com/wp-content/uploads/2016/07/couchbase-create-primary-index.gif)

If you don't have Couchbase Server 4.5 or higher installed, a different strategy must be used for creating indexes. You must use the Couchbase Query Shell (CBQ) that ships with Couchbase Server 4.0 and higher. For information on how to access it, you can visit [here](http://developer.couchbase.com/documentation/server/current/getting-started/first-n1ql-query.html). The same query used in the Query Workbench should be used in the shell.

At this point we can focus on the actual application.

## Designing the RESTful Web Application

With all the setup out of the way we can start coding the application. This application will have five basic endpoints for handling information about people. This is a standard create, retrieve, update, and delete (CRUD) application. Each endpoint will communicate with the NoSQL database differently.

Inside the project's **$GOPATH/src/github.com/nraboy/cbproject/main.go** file, add the following code:

<span class="crayon-title">$GOPATH/src/github.com/nraboy/cbproject/main.go</span>

<div class="crayon-nums-content">
  <p>100</p>
  <p>101</p>
  <p>102</p>
  <p>103</p>
  <p>104</p>
  <p>105</p>
  <p>106</p>
  <p>107</p>
  <p>108</p>
  <p>109</p>
</div> | <div class="crayon-pre">
  <p>
  <span class="crayon-st">package</span>
  <span class="crayon-e">main</span>
</p>
  <p>
  <span class="crayon-st">import</span>
  <span class="crayon-sy">(</span>
</p>
  <p>
  <span class="crayon-s">“encoding/json”</span>
</p>
  <p>
  <span class="crayon-s">“log”</span>
</p>
  <p>
  <span class="crayon-s">“net/http”</span>
</p>
  <p>
  <span class="crayon-s">“github.com/couchbase/gocb”</span>
</p>
  <p>
  <span class="crayon-s">“github.com/gorilla/mux”</span>
</p>
  <p>
  <span class="crayon-s">“github.com/satori/go.uuid”</span>
</p>
  <p>
  <span class="crayon-st">type</span>
  <span class="crayon-e">Person</span>
  <span class="crayon-st">struct</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">ID        </span>
  <span class="crayon-t">string</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“id,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
  <p>
  <span class="crayon-e">Firstname </span>
  <span class="crayon-t">string</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“firstname,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
  <p>
  <span class="crayon-e">Lastname  </span>
  <span class="crayon-t">string</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“lastname,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
  <p>
  <span class="crayon-e">Email     </span>
  <span class="crayon-t">string</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“email,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
  <p>
  <span class="crayon-st">type</span>
  <span class="crayon-e">N1qlPerson</span>
  <span class="crayon-st">struct</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">Person </span>
  <span class="crayon-i">Person</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“person”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-e">bucket <em>
</em></span>
  <em><span class="crayon-v">gocb</span><span class="crayon-sy">.</span><span class="crayon-e">Bucket</span></em>
</p>
  <em>
</em>
  <p>
  <em><span class="crayon-st">func</span><span class="crayon-e">GetPersonEndpoint</span><span class="crayon-sy">(</span><span class="crayon-i">w</span><span class="crayon-v">http</span><span class="crayon-sy">.</span><span class="crayon-v">ResponseWriter</span><span class="crayon-sy">,</span><span class="crayon-e">req </span></em>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Request</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-e">n1qlParams</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-st">interface</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">query</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">gocb</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewN1qlQuery</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“SELECT create<em>a_simple_restful_api_with_golang.md data.json data_ml devops docker embedded gaming github go html javascript linux mobile png programming science social tidy url_to_filename.csv web_dev FROM <code>restful-sample</code> AS person WHERE META(person).id = $1”</em></span>
  <em><span class="crayon-sy">)</span></em>
</p>
  <em>
  <p><span class="crayon-v">params</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">mux</span><span class="crayon-sy">.</span><span class="crayon-e">Vars</span><span class="crayon-sy">(</span><span class="crayon-v">req</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">params</span><span class="crayon-sy">[</span><span class="crayon-s">“id”</span><span class="crayon-sy">]</span><span class="crayon-sy">)</span></p>
</em>
  <p>
  <em><span class="crayon-v">rows</span><span class="crayon-sy">,</span><span class="crayon-v">
</span></em>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">bucket</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">ExecuteN1qlQuery</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">query</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">n1qlParams</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-e">row </span>
  <span class="crayon-e">N1qlPerson</span>
</p>
  <p>
  <span class="crayon-v">rows</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">One</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-o">&amp;</span>
  <span class="crayon-v">row</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">json</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewEncoder</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">w</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Encode</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">row</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Person</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">func</span>
  <span class="crayon-e">GetPeopleEndpoint</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">w</span>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ResponseWriter</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">req <em>
</em></span>
  <em><span class="crayon-v">http</span><span class="crayon-sy">.</span><span class="crayon-v">Request</span><span class="crayon-sy">)</span><span class="crayon-sy">{</span></em>
</p>
  <em>
  <p><span class="crayon-st">var</span><span class="crayon-i">person</span><span class="crayon-sy">[</span><span class="crayon-sy">]</span><span class="crayon-e">Person</span></p>
  <p><span class="crayon-v">query</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">gocb</span><span class="crayon-sy">.</span><span class="crayon-e">NewN1qlQuery</span><span class="crayon-sy">(</span><span class="crayon-s">“SELECT create<em>a_simple_restful_api_with_golang.md data.json data_ml devops docker embedded gaming github go html javascript linux mobile png programming science social tidy url_to_filename.csv web_dev FROM <code>restful-sample</code> AS person”</em></span><em><span class="crayon-sy">)</span></em></p>
  <em></em>
  <p><em><span class="crayon-v">rows</span><span class="crayon-sy">,</span><span class="crayon-v">
</span></em><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">bucket</span><span class="crayon-sy">.</span><span class="crayon-e">ExecuteN1qlQuery</span><span class="crayon-sy">(</span><span class="crayon-v">query</span><span class="crayon-sy">,</span><span class="crayon-v">nil</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-st">var</span><span class="crayon-e">row </span><span class="crayon-e">N1qlPerson</span></p>
  <p><span class="crayon-st">for</span><span class="crayon-v">rows</span><span class="crayon-sy">.</span><span class="crayon-e">Next</span><span class="crayon-sy">(</span><span class="crayon-o">&amp;</span><span class="crayon-v">row</span><span class="crayon-sy">)</span><span class="crayon-sy">{</span></p>
  <p><span class="crayon-v">person</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">person</span><span class="crayon-sy">,</span><span class="crayon-v">row</span><span class="crayon-sy">.</span><span class="crayon-v">Person</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-v">json</span><span class="crayon-sy">.</span><span class="crayon-e">NewEncoder</span><span class="crayon-sy">(</span><span class="crayon-v">w</span><span class="crayon-sy">)</span><span class="crayon-sy">.</span><span class="crayon-e">Encode</span><span class="crayon-sy">(</span><span class="crayon-v">person</span><span class="crayon-sy">)</span></p>
</em>
  <p>
  <em><span class="crayon-st">func</span><span class="crayon-e">CreatePersonEndpoint</span><span class="crayon-sy">(</span><span class="crayon-i">w</span><span class="crayon-v">http</span><span class="crayon-sy">.</span><span class="crayon-v">ResponseWriter</span><span class="crayon-sy">,</span><span class="crayon-e">req </span></em>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Request</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-e">person</span>
  <span class="crayon-e">Person</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-e">n1qlParams</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-st">interface</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">
  <em>
</em>
</span>
  <em><span class="crayon-o">=</span><span class="crayon-v">json</span><span class="crayon-sy">.</span><span class="crayon-e">NewDecoder</span><span class="crayon-sy">(</span><span class="crayon-v">req</span><span class="crayon-sy">.</span><span class="crayon-v">Body</span><span class="crayon-sy">)</span><span class="crayon-sy">.</span><span class="crayon-e">Decode</span><span class="crayon-sy">(</span><span class="crayon-o">&amp;</span><span class="crayon-v">person</span><span class="crayon-sy">)</span></em>
</p>
  <em>
  <p><span class="crayon-v">query</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">gocb</span><span class="crayon-sy">.</span><span class="crayon-e">NewN1qlQuery</span><span class="crayon-sy">(</span><span class="crayon-s">“INSERT INTO <code>restful-sample</code> (KEY, VALUE) VALUES ($1, {’firstname’: $2, ‘lastname’: $3, ‘email’: $4})”</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">uuid</span><span class="crayon-sy">.</span><span class="crayon-e">NewV4</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span><span class="crayon-sy">.</span><span class="crayon-t">String</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">person</span><span class="crayon-sy">.</span><span class="crayon-v">Firstname</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">person</span><span class="crayon-sy">.</span><span class="crayon-v">Lastname</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">person</span><span class="crayon-sy">.</span><span class="crayon-v">Email</span><span class="crayon-sy">)</span></p>
</em>
  <p>
  <em><span class="crayon-v">
</span></em>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">err</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">bucket</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">ExecuteN1qlQuery</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">query</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">n1qlParams</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-v">err</span>
  <span class="crayon-o">!=</span>
  <span class="crayon-e">nil</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">w</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">WriteHeader</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-cn">401</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">w</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Write</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-t">byte</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">err</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Error</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">return</span>
</p>
  <p>
  <span class="crayon-v">json</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewEncoder</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">w</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Encode</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">person</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">func</span>
  <span class="crayon-e">UpdatePersonEndpoint</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">w</span>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ResponseWriter</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">req <em>
</em></span>
  <em><span class="crayon-v">http</span><span class="crayon-sy">.</span><span class="crayon-v">Request</span><span class="crayon-sy">)</span><span class="crayon-sy">{</span></em>
</p>
  <em>
  <p><span class="crayon-st">var</span><span class="crayon-e">person</span><span class="crayon-e">Person</span></p>
  <p><span class="crayon-st">var</span><span class="crayon-e">n1qlParams</span><span class="crayon-sy">[</span><span class="crayon-sy">]</span><span class="crayon-st">interface</span><span class="crayon-sy">{</span><span class="crayon-sy">}</span></p>
  <p><span class="crayon-v">
  <em>
</em>
</span><em><span class="crayon-o">=</span><span class="crayon-v">json</span><span class="crayon-sy">.</span><span class="crayon-e">NewDecoder</span><span class="crayon-sy">(</span><span class="crayon-v">req</span><span class="crayon-sy">.</span><span class="crayon-v">Body</span><span class="crayon-sy">)</span><span class="crayon-sy">.</span><span class="crayon-e">Decode</span><span class="crayon-sy">(</span><span class="crayon-o">&amp;</span><span class="crayon-v">person</span><span class="crayon-sy">)</span></em></p>
  <em><p><span class="crayon-v">query</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">gocb</span><span class="crayon-sy">.</span><span class="crayon-e">NewN1qlQuery</span><span class="crayon-sy">(</span><span class="crayon-s">“UPDATE <code>restful-sample</code> USE KEYS $1 SET firstname = $2, lastname = $3, email = $4”</span><span class="crayon-sy">)</span></p><p><span class="crayon-v">params</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">mux</span><span class="crayon-sy">.</span><span class="crayon-e">Vars</span><span class="crayon-sy">(</span><span class="crayon-v">req</span><span class="crayon-sy">)</span></p><p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">params</span><span class="crayon-sy">[</span><span class="crayon-s">“id”</span><span class="crayon-sy">]</span><span class="crayon-sy">)</span></p><p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">person</span><span class="crayon-sy">.</span><span class="crayon-v">Firstname</span><span class="crayon-sy">)</span></p><p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">person</span><span class="crayon-sy">.</span><span class="crayon-v">Lastname</span><span class="crayon-sy">)</span></p><p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">person</span><span class="crayon-sy">.</span><span class="crayon-v">Email</span><span class="crayon-sy">)</span></p></em>
  <p><em><span class="crayon-v">
</span></em><span class="crayon-sy">,</span><span class="crayon-v">err</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">bucket</span><span class="crayon-sy">.</span><span class="crayon-e">ExecuteN1qlQuery</span><span class="crayon-sy">(</span><span class="crayon-v">query</span><span class="crayon-sy">,</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-st">if</span><span class="crayon-v">err</span><span class="crayon-o">!=</span><span class="crayon-e">nil</span><span class="crayon-sy">{</span></p>
  <p><span class="crayon-v">w</span><span class="crayon-sy">.</span><span class="crayon-e">WriteHeader</span><span class="crayon-sy">(</span><span class="crayon-cn">401</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-v">w</span><span class="crayon-sy">.</span><span class="crayon-e">Write</span><span class="crayon-sy">(</span><span class="crayon-sy">[</span><span class="crayon-sy">]</span><span class="crayon-t">byte</span><span class="crayon-sy">(</span><span class="crayon-v">err</span><span class="crayon-sy">.</span><span class="crayon-e">Error</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-st">return</span></p>
  <p><span class="crayon-v">json</span><span class="crayon-sy">.</span><span class="crayon-e">NewEncoder</span><span class="crayon-sy">(</span><span class="crayon-v">w</span><span class="crayon-sy">)</span><span class="crayon-sy">.</span><span class="crayon-e">Encode</span><span class="crayon-sy">(</span><span class="crayon-v">person</span><span class="crayon-sy">)</span></p>
</em>
  <p>
  <em><span class="crayon-st">func</span><span class="crayon-e">DeletePersonEndpoint</span><span class="crayon-sy">(</span><span class="crayon-i">w</span><span class="crayon-v">http</span><span class="crayon-sy">.</span><span class="crayon-v">ResponseWriter</span><span class="crayon-sy">,</span><span class="crayon-e">req </span></em>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Request</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-e">n1qlParams</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-st">interface</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">query</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">gocb</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewN1qlQuery</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“DELETE FROM <code>restful-sample</code> AS person WHERE META(person).id = $1”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">params</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">mux</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Vars</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">req</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">n1qlParams</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">n1qlParams</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">params</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“id”</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">
  <em>
</em>
</span>
  <em><span class="crayon-sy">,</span><span class="crayon-v">err</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">bucket</span><span class="crayon-sy">.</span><span class="crayon-e">ExecuteN1qlQuery</span><span class="crayon-sy">(</span><span class="crayon-v">query</span><span class="crayon-sy">,</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">)</span></em>
</p>
  <em>
  <p><span class="crayon-st">if</span><span class="crayon-v">err</span><span class="crayon-o">!=</span><span class="crayon-e">nil</span><span class="crayon-sy">{</span></p>
  <p><span class="crayon-v">w</span><span class="crayon-sy">.</span><span class="crayon-e">WriteHeader</span><span class="crayon-sy">(</span><span class="crayon-cn">401</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-v">w</span><span class="crayon-sy">.</span><span class="crayon-e">Write</span><span class="crayon-sy">(</span><span class="crayon-sy">[</span><span class="crayon-sy">]</span><span class="crayon-t">byte</span><span class="crayon-sy">(</span><span class="crayon-v">err</span><span class="crayon-sy">.</span><span class="crayon-e">Error</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-st">return</span></p>
  <p><span class="crayon-v">json</span><span class="crayon-sy">.</span><span class="crayon-e">NewEncoder</span><span class="crayon-sy">(</span><span class="crayon-v">w</span><span class="crayon-sy">)</span><span class="crayon-sy">.</span><span class="crayon-e">Encode</span><span class="crayon-sy">(</span><span class="crayon-o">&amp;</span><span class="crayon-e">Person</span><span class="crayon-sy">{</span><span class="crayon-sy">}</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-st">func</span><span class="crayon-e">main</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span><span class="crayon-sy">{</span></p>
  <p><span class="crayon-v">router</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">mux</span><span class="crayon-sy">.</span><span class="crayon-e">NewRouter</span><span class="crayon-sy">(</span><span class="crayon-sy">)</span></p>
</em>
  <p>
  <em><span class="crayon-v">cluster</span><span class="crayon-sy">,</span><span class="crayon-v">
</span></em>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">gocb</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Connect</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“couchbase://localhost”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">bucket</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">_</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">cluster</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">OpenBucket</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“restful-sample”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">GetPeopleEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“GET”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people/{id}”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">GetPersonEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“GET”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">CreatePersonEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“PUT”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people/{id}”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">UpdatePersonEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“POST”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people/{id}”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">DeletePersonEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“DELETE”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">log</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Fatal</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">ListenAndServe</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“:12345”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
</p>
</div>
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

There is a lot going on in the above code so it would be a good idea to break it down.

The first thing we're doing is importing all the dependencies that we downloaded earlier in this article. With the dependencies included in the project we can define two structs that will represent the JSON data that we work with. The `Person` struct will have the essential information about a particular person and it will be saved like this as a JSON document in the database. When querying data, result sets are wrapped in a parent JSON object, thus the need for the `N1qlPerson` struct. Data will look like this when queried:

<span class="crayon-title">JSON Data from Queries</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-s">“person”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-s">“id”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“1234”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“firstname”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Nic”</span>
  <span class="crayon-sy">,</span>
</p>
  <p>
  <span class="crayon-s">“lastname”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Raboy”</span>
  <span class="crayon-sy">,</span>
</p>
  <div id="crayon-57a63d2040844733201947-6" class="crayon-line crayon-striped-line">
  <span class="crayon-s">“email”</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">"<a href="https://www.thepolyglotdeveloper.com/cdn-cgi/l/email-protection" class="__cf_email__">[email protected]</a>"</span>
</div>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Inside the structs you'll notice the use of the `omitempty` tag. This means that if any property is empty or null, it will not be included in the JSON result.

Before we jump into the endpoint functions we have the following:

<span class="crayon-title">$GOPATH/src/github.com/nraboy/cbproject/main.go</span>

This `bucket` variable is defined outside the `main` function making it global to the application. This will represent our open Couchbase bucket and be used when querying data.

Let's skip down to the project's `main` function:

<span class="crayon-title">$GOPATH/src/github.com/nraboy/cbproject/main.go</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-st">func</span>
  <span class="crayon-e">main</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">mux</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewRouter</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">cluster</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">
  <em>
</em>
</span>
  <em><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">gocb</span><span class="crayon-sy">.</span><span class="crayon-e">Connect</span><span class="crayon-sy">(</span><span class="crayon-s">“couchbase://localhost”</span><span class="crayon-sy">)</span></em>
</p>
  <em>
</em>
  <p>
  <em><span class="crayon-v">bucket</span><span class="crayon-sy">,</span><span class="crayon-v">
</span></em>
  <span class="crayon-o">=</span>
  <span class="crayon-v">cluster</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">OpenBucket</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“restful-sample”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">GetPeopleEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“GET”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people/{id}”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">GetPersonEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“GET”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">CreatePersonEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“PUT”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people/{id}”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">UpdatePersonEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“POST”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">HandleFunc</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“/people/{id}”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">DeletePersonEndpoint</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Methods</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“DELETE”</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">log</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Fatal</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">ListenAndServe</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“:12345”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">router</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In the above `main` function we define our application router and establish a connection to our Couchbase cluster. In this case the Couchbase cluster is being run locally on my machine. With the Couchbase cluster connection established we can open a particular bucket. The bucket we use in this application will be called `restful-sample`.

There are five different routes in this application. There is a route for getting all documents, a route for getting a single document, a route for creating a document, a route for updating a document, and finally a route for deleting a document. Notice that each of these routes use `GET`, `PUT`, `POST`, or `DELETE`. Let's take a look at each of the endpoint functions that go with each of the routes.

<span class="crayon-title">$GOPATH/src/github.com/nraboy/cbproject/main.go</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-st">func</span>
  <span class="crayon-e">GetPersonEndpoint</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">w</span>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ResponseWriter</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">req *</span>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Request</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-e">n1qlParams</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-st">interface</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">}</span>
</p>
  <p>
  <span class="crayon-v">query</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">gocb</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewN1qlQuery</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“SELECT create<em>a_simple_restful_api_with_golang.md data.json data_ml devops docker embedded gaming github go html javascript linux mobile png programming science social tidy url_to_filename.csv web_dev FROM <code>restful-sample</code> AS person WHERE META(person).id = $1”</em></span>
  <em><span class="crayon-sy">)</span></em>
</p>
  <em>
  <p><span class="crayon-v">params</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">mux</span><span class="crayon-sy">.</span><span class="crayon-e">Vars</span><span class="crayon-sy">(</span><span class="crayon-v">req</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-v">n1qlParams</span><span class="crayon-o">=</span><span class="crayon-e">append</span><span class="crayon-sy">(</span><span class="crayon-v">n1qlParams</span><span class="crayon-sy">,</span><span class="crayon-v">params</span><span class="crayon-sy">[</span><span class="crayon-s">“id”</span><span class="crayon-sy">]</span><span class="crayon-sy">)</span></p>
</em>
  <p>
  <em><span class="crayon-v">rows</span><span class="crayon-sy">,</span><span class="crayon-v">
</span></em>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">bucket</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">ExecuteN1qlQuery</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">query</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">n1qlParams</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-e">row </span>
  <span class="crayon-e">N1qlPerson</span>
</p>
  <p>
  <span class="crayon-v">rows</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">One</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-o">&amp;</span>
  <span class="crayon-v">row</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">json</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewEncoder</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">w</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Encode</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">row</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Person</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The above `GetPersonEndpoint` function will get a single person from the database. The document id from the meta information is being compared against the document id parameter that was passed in with the route request. This query is a parameterized query to prevent SQL injection. We are returning JSON data with only the `Person` struct, not the entire `N1qlPerson` struct.

<span class="crayon-title">$GOPATH/src/github.com/nraboy/cbproject/main.go</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-st">func</span>
  <span class="crayon-e">GetPeopleEndpoint</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">w</span>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ResponseWriter</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">req *</span>
  <span class="crayon-v">http</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Request</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-i">person</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">Person</span>
</p>
  <p>
  <span class="crayon-v">query</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">gocb</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewN1qlQuery</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“SELECT create<em>a_simple_restful_api_with_golang.md data.json data_ml devops docker embedded gaming github go html javascript linux mobile png programming science social tidy url_to_filename.csv web_dev FROM <code>restful-sample</code> AS person”</em></span>
  <em><span class="crayon-sy">)</span></em>
</p>
  <em>
</em>
  <p>
  <em><span class="crayon-v">rows</span><span class="crayon-sy">,</span><span class="crayon-v">
</span></em>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">bucket</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">ExecuteN1qlQuery</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">query</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">nil</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">var</span>
  <span class="crayon-e">row </span>
  <span class="crayon-e">N1qlPerson</span>
</p>
  <p>
  <span class="crayon-st">for</span>
  <span class="crayon-v">rows</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Next</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-o">&amp;</span>
  <span class="crayon-v">row</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">person</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">person</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">row</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Person</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">json</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewEncoder</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">w</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Encode</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">person</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The above `GetPeopleEndpoint` function is similar to the previous, but this time we are expecting a slice of results rather than a single result.

The create, update, and delete functions follow pretty much the same strategy, making things very easy to maintain and understand.

At this point the applicant can be run. When it comes to testing you'll have to use cURL, [Postman](https://www.thepolyglotdeveloper.com/2015/01/using-postman-troubleshoot-restful-api-requests/), or similar since the web browser cannot typically test `PUT`, `POST`, or `DELETE` endpoints out of the box.

## Conclusion

You just saw how to take our previous [RESTful API GoLang example](https://www.thepolyglotdeveloper.com/2016/07/create-a-simple-restful-api-with-golang/) to the next level by including [Couchbase Server](http://www.couchbase.com) as our NoSQL document database. When using Couchbase, we are able to run SQL-like queries called N1QL queries to work with any data in the database. Although we don't have to use these N1QL queries, it makes things very easy and reduces a lot of potential parsing code.
