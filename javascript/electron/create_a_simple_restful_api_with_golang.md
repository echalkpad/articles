# Create A Simple RESTful API With GoLang

[Original URL](https://www.thepolyglotdeveloper.com/2016/07/create-a-simple-restful-api-with-golang/)

> Most modern applications have separation between the backend and the frontend layers. The backend is typically a RESTful API and is critical part of full stack development. These APIs are...

Most modern applications have separation between the backend and the frontend layers. The backend is typically a RESTful API and is critical part of full stack development. These APIs are generally further broken down into a collection of routes, often referred to as endpoints. Building applications like this is often very clean and maintainable in comparison to mashing everything into a single application.

I have been creating RESTful APIs with a variety of programming languages, for example [Node.js](https://www.thepolyglotdeveloper.com/2015/10/create-a-simple-restful-api-with-node-js/) and Java, but lately I've been doing a lot of research on the [Go](https://golang.org) programming language. It is fast and very solid programming language that every seems to be talking about. Because of this it only made sense to see what it took to build a RESTful API with Go, often referred to as GoLang.

We're going to see what it takes to build a simple API that does basic CRUD operations using the Go programming language.

For simplicity, we're not going to be using a database in this example. We're going to be hard-coding some data which can be referred to as mock data. This will allow us to focus on core material like defining endpoints and doing basic operations.

At this point we're going to assume Go is already installed and configured on your machine. From the Terminal (Mac and Linux) or Command Prompt (Windows), execute the following:

<span class="crayon-title">Create New GoLang Project</span>

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
  <span class="crayon-v">restapi</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

If your command line doesn't support any of the above, just manually create the directory any way you see fit.

In the freshly created project, you'll want to create a file that contains our `main` function. In this example we'll be using **$GOPATH/src/github.com/nraboy/restapi/myproject.go**.

Inside this GoLang file we want to add the following code. It is a lot of code, but don't worry, we're going to break it down next.

<span class="crayon-title">$GOPATH/src/github.com/nraboy/restapi/myproject.go</span>

 | <div class="crayon-pre">
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
  <span class="crayon-s">“github.com/gorilla/mux”</span>
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
  <span class="crayon-e">Address   <em>
</em></span>
  <em><span class="crayon-i">Address</span><span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“address,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span></em>
</p>
  <em>
  <p><span class="crayon-st">type</span><span class="crayon-e">Address</span><span class="crayon-st">struct</span><span class="crayon-sy">{</span></p>
  <p><span class="crayon-e">City  </span><span class="crayon-t">string</span><span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“city,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span></p>
  <p><span class="crayon-e">State </span><span class="crayon-t">string</span><span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“state,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span></p>
  <p><span class="crayon-st">var</span><span class="crayon-i">people</span><span class="crayon-sy">[</span><span class="crayon-sy">]</span><span class="crayon-e">Person</span></p>
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
  <span class="crayon-st">for</span>
  <span class="crayon-v">
  <em>
</em>
</span>
  <em><span class="crayon-sy">,</span><span class="crayon-v">item</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-st">range</span><span class="crayon-e">people</span><span class="crayon-sy">{</span></em>
</p>
  <em>
  <p><span class="crayon-st">if</span><span class="crayon-v">item</span><span class="crayon-sy">.</span><span class="crayon-v">ID</span><span class="crayon-o">==</span><span class="crayon-v">params</span><span class="crayon-sy">[</span><span class="crayon-s">“id”</span><span class="crayon-sy">]</span><span class="crayon-sy">{</span></p>
  <p><span class="crayon-v">json</span><span class="crayon-sy">.</span><span class="crayon-e">NewEncoder</span><span class="crayon-sy">(</span><span class="crayon-v">w</span><span class="crayon-sy">)</span><span class="crayon-sy">.</span><span class="crayon-e">Encode</span><span class="crayon-sy">(</span><span class="crayon-v">item</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-st">return</span></p>
  <p><span class="crayon-v">json</span><span class="crayon-sy">.</span><span class="crayon-e">NewEncoder</span><span class="crayon-sy">(</span><span class="crayon-v">w</span><span class="crayon-sy">)</span><span class="crayon-sy">.</span><span class="crayon-e">Encode</span><span class="crayon-sy">(</span><span class="crayon-o">&amp;</span><span class="crayon-e">Person</span><span class="crayon-sy">{</span><span class="crayon-sy">}</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-st">func</span><span class="crayon-e">GetPeopleEndpoint</span><span class="crayon-sy">(</span><span class="crayon-i">w</span><span class="crayon-v">http</span><span class="crayon-sy">.</span><span class="crayon-v">ResponseWriter</span><span class="crayon-sy">,</span><span class="crayon-e">req <em>
</em></span><em><span class="crayon-v">http</span><span class="crayon-sy">.</span><span class="crayon-v">Request</span><span class="crayon-sy">)</span><span class="crayon-sy">{</span></em></p>
  <em><p><span class="crayon-v">json</span><span class="crayon-sy">.</span><span class="crayon-e">NewEncoder</span><span class="crayon-sy">(</span><span class="crayon-v">w</span><span class="crayon-sy">)</span><span class="crayon-sy">.</span><span class="crayon-e">Encode</span><span class="crayon-sy">(</span><span class="crayon-v">people</span><span class="crayon-sy">)</span></p></em>
  <p><em><span class="crayon-st">func</span><span class="crayon-e">CreatePersonEndpoint</span><span class="crayon-sy">(</span><span class="crayon-i">w</span><span class="crayon-v">http</span><span class="crayon-sy">.</span><span class="crayon-v">ResponseWriter</span><span class="crayon-sy">,</span><span class="crayon-e">req </span></em><span class="crayon-v">http</span><span class="crayon-sy">.</span><span class="crayon-v">Request</span><span class="crayon-sy">)</span><span class="crayon-sy">{</span></p>
  <p><span class="crayon-v">params</span><span class="crayon-o">:</span><span class="crayon-o">=</span><span class="crayon-v">mux</span><span class="crayon-sy">.</span><span class="crayon-e">Vars</span><span class="crayon-sy">(</span><span class="crayon-v">req</span><span class="crayon-sy">)</span></p>
  <p><span class="crayon-st">var</span><span class="crayon-e">person </span><span class="crayon-i">Person</span></p>
</em>
  <p>
  <em><span class="crayon-v">
</span></em>
  <span class="crayon-o">=</span>
  <span class="crayon-v">json</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewDecoder</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">req</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Body</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Decode</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-o">&amp;</span>
  <span class="crayon-v">person</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">person</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ID</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">params</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“id”</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-v">people</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">person</span>
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
  <span class="crayon-v">people</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">func</span>
  <span class="crayon-e">DeletePersonEndpoint</span>
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
  <span class="crayon-st">for</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">item</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-st">range</span>
  <span class="crayon-e">people</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-v">item</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ID</span>
  <span class="crayon-o">==</span>
  <span class="crayon-v">params</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“id”</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">people</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">index</span>
  <span class="crayon-o">+</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">break</span>
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
  <span class="crayon-v">people</span>
  <span class="crayon-sy">)</span>
</p>
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
  <span class="crayon-v">people</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">Person</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">ID</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“1”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Firstname</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Nic”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Lastname</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Raboy”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Address</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">&amp;</span>
  <span class="crayon-e">Address</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">City</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Dublin”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">State</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“CA”</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">people</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">Person</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">ID</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“2”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Firstname</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Maria”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Lastname</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Raboy”</span>
  <span class="crayon-sy">}</span>
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
  <span class="crayon-s">“/people/{id}”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">CreatePersonEndpoint</span>
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
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

So what is happening in the above code?

The first thing you'll notice is that we're importing various dependencies. We'll be working with JSON data so the `encoding/json` dependency is required. While we'll be working with HTTP requests, the `net/http` dependency is not quite enough. The `mux` dependency is a helper to not only make endpoints easier to create, but also give us more features. Since this is an external dependency, it must be downloaded like follows:

<span class="crayon-title">Get mux Dependency for Go</span>

 | <div class="crayon-pre">
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
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

More information on mux can be found in the [official documentation](http://www.gorillatoolkit.org/pkg/mux).

With the dependencies imported we want to create the `struct` objects that will house our data. The data we plan to store will be people data:

<span class="crayon-title">$GOPATH/src/github.com/nraboy/restapi/myproject.go</span>

 | <div class="crayon-pre">
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
  <span class="crayon-e">Address   *</span>
  <span class="crayon-i">Address</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“address,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

You'll notice we are defining what properties exist in the struct, but we are also defining tags that describe how the struct will appear as JSON. Inside each of the tags there is an `omitempty` parameter. This means that if the property is null, it will be excluded from the JSON data rather than showing up as an empty string or value.

Inside the `Person` struct there is an `Address` property that is a pointer. This will represent a nested JSON object and it must be a pointer otherwise the `omitempty` will fail to work. So what does `Address` look like?

<span class="crayon-title">$GOPATH/src/github.com/nraboy/restapi/myproject.go</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-st">type</span>
  <span class="crayon-e">Address</span>
  <span class="crayon-st">struct</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">City  </span>
  <span class="crayon-t">string</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“city,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
  <p>
  <span class="crayon-e">State </span>
  <span class="crayon-t">string</span>
  <span class="crayon-sy">
  <code>&lt;/span&gt;&lt;span class="crayon-v"&gt;json&lt;/span&gt;&lt;span class="crayon-o"&gt;:&lt;/span&gt;&lt;span class="crayon-s"&gt;“state,omitempty”&lt;/span&gt;&lt;span class="crayon-sy"&gt;</code>
</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Again, this is a nested structure that is not too different from the previous.

Because we're not using a database, we want to create a public variable that is global to the project. This variable will be a slice of `Person` and contain all the data used in this application.

Our easiest endpoint is probably the `GetPeopleEndpoint` because it will only return the full `person` variable to the frontend. Where things start to change is when we want to insert, delete, or get a particular record.

<span class="crayon-title">$GOPATH/src/github.com/nraboy/restapi/myproject.go</span>

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
  <span class="crayon-st">for</span>
  <span class="crayon-v">_</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">item</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-st">range</span>
  <span class="crayon-e">people</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-v">item</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ID</span>
  <span class="crayon-o">==</span>
  <span class="crayon-v">params</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“id”</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">{</span>
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
  <span class="crayon-v">item</span>
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
  <span class="crayon-o">&amp;</span>
  <span class="crayon-e">Person</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In the above `GetPersonEndpoint` we are trying to get a single record. Using the mux library we can get any parameters that were passed in with the request. We then loop over our global slice and look for any ids that match the id found in the request parameters. If a match is found, use the JSON encoder to display it, otherwise create an empty JSON object.

In reality, your endpoints will communicate with a database and will probably involve a lot less application logic. This is because you'll be using some form of querying instead.

The `CreatePersonEndpoint` is a bit different because we'll be receiving JSON data to work with in the request.

<span class="crayon-title">$GOPATH/src/github.com/nraboy/restapi/myproject.go</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-st">func</span>
  <span class="crayon-e">CreatePersonEndpoint</span>
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
  <span class="crayon-st">var</span>
  <span class="crayon-e">person </span>
  <span class="crayon-i">Person</span>
</p>
  <p>
  <span class="crayon-v">_</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">json</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">NewDecoder</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">req</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">Body</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">Decode</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-o">&amp;</span>
  <span class="crayon-v">person</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">person</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ID</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">params</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“id”</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-v">people</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">person</span>
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
  <span class="crayon-v">people</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In the above we decode the JSON data that was passed in and store it in a `Person` object. We assign the new object an id based on what mux found and then we append it to our global slice. In the end, our global array will be returned and it should include everything including our newly added piece of data.

In the scenario of this example application, the method for deleting data is a bit different as well.

<span class="crayon-title">$GOPATH/src/github.com/nraboy/restapi/myproject.go</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-st">func</span>
  <span class="crayon-e">DeletePersonEndpoint</span>
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
  <span class="crayon-st">for</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">item</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">=</span>
  <span class="crayon-st">range</span>
  <span class="crayon-e">people</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-v">item</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">ID</span>
  <span class="crayon-o">==</span>
  <span class="crayon-v">params</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-s">“id”</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">people</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">index</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">index</span>
  <span class="crayon-o">+</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-o">:</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-st">break</span>
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
  <span class="crayon-v">people</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In this case, we have the `DeletePersonEndpoint` looping through the data similarly to the `GetPersonEndpoint` we saw earlier. The difference is that instead of printing the data, we need to remove it. When the id to be deleted has been found, we can recreate our slice with all data excluding that found at the index.

Finally we end up in our runnable `main` function that brings the application together.

<span class="crayon-title">$GOPATH/src/github.com/nraboy/restapi/myproject.go</span>

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
  <span class="crayon-v">people</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">Person</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">ID</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“1”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Firstname</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Nic”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Lastname</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Raboy”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Address</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">&amp;</span>
  <span class="crayon-e">Address</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">City</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Dublin”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">State</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“CA”</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">people</span>
  <span class="crayon-o">=</span>
  <span class="crayon-e">append</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">people</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">Person</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">ID</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“2”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Firstname</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Maria”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">Lastname</span>
  <span class="crayon-o">:</span>
  <span class="crayon-s">“Raboy”</span>
  <span class="crayon-sy">}</span>
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
  <span class="crayon-s">“/people/{id}”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">CreatePersonEndpoint</span>
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
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In the above code we first create our new router and add two objects to our slice to get things started. Next up we have to create each of the endpoints that will call our endpoint functions. Notice we are using `GET`, `POST`, and `DELETE` where appropriate. We are also defining parameters that can be passed in.

At the very end we define that our server will run on port `12345`, which at this point brings our project to a close. Run the project and give it a shot. You might need a tool like [Postman](https://www.thepolyglotdeveloper.com/2015/01/using-postman-troubleshoot-restful-api-requests/) or cURL to test all the endpoints.

## Conclusion

You just saw how to build a very simple RESTful API using the Go programming language. While we used mock data instead of a database, we saw how to create endpoints that do various operations with JSON data and GoLang slices. By creating an API your application will become easy to maintain and be expandable to web, mobile and IoT as each of these platforms will only require a different frontend that we had not created in this example.
