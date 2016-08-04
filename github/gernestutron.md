# gernest/utron

[Original URL](https://github.com/gernest/utron)

> utron

## [](https://github.com/gernest/utron#utron---)utron [![GoDoc](https://camo.githubusercontent.com/9c6a9b96aaba7959296a14d5b58d002e3b703571/68747470733a2f2f676f646f632e6f72672f6769746875622e636f6d2f6765726e6573742f7574726f6e3f7374617475732e737667)](https://godoc.org/github.com/gernest/utron) [![Coverage Status](https://camo.githubusercontent.com/b4813fb384cdb06f11d790b0b41e4c097fae7b08/68747470733a2f2f636f766572616c6c732e696f2f7265706f732f6765726e6573742f7574726f6e2f62616467652e7376673f6272616e63683d6d617374657226736572766963653d676974687562)](https://coveralls.io/github/gernest/utron?branch=master) [![Build Status](https://camo.githubusercontent.com/68e8ea3728ce8498271acbbc1bc1b5e93aec1b0a/68747470733a2f2f7472617669732d63692e6f72672f6765726e6573742f7574726f6e2e737667)](https://travis-ci.org/gernest/utron)

`utron` is a lightweight MVC framework in Go(Golang) for building fast, scallable and robust database driven web applications.

## [](https://github.com/gernest/utron#features)Features

- postgesql ,mysql and foundation database support
- modular (you can choose which component to use)
- middlewares support. All alice compatible Middlewares works out of the box
- gopher spirit (write golang, use all the golang libraries you like)
- lightweight. only MVC nothing more
- multiple configuration files support( currently json,yaml and toml)

## [](https://github.com/gernest/utron#overview)Overview

`utron` is a lightweight MVC framework. It is based on the premise of simplisity, relevance and elegancy.

- simplisity. The design is simple, easy to understand and less layers from the standard library. It is in the spirit of the project that, users should understand the whole framework in a single day.

- relevance. `utron` doesn;t assume anything. We focus on things that matter, this way, we are able to achieve good maintanance and keeping the system well organized, well planned and sweet like.

- elegancy. `utron` uses the best practises used in golang. We are not afraid of heights, its just we need a parachute in our backpack. The source code is heavily documented, any trick should be well explained and well tested.

## [](https://github.com/gernest/utron#motivation)Motivation

After ywo years of playing with golang. I have looked on some of my projects and asked myself, how golang is that?

So, `utron` is my reimagination of lightweight MVC, that maintains the golang spirit. and works seamlessly with the current libraries.

## [](https://github.com/gernest/utron#installation)Installation

```
$ go get github.com/gernest/utron
```

## [](https://github.com/gernest/utron#the-mvc)The MVC

There is nothing revolutionary about MVC that `utron` brings on the table.

- M is for models, it is the data structures that helps in data persistance, utron uses an already established Object Relational Mapper for golang [gorm](https://github.com/jinzhu/gorm). So if you are familiar with gorm then you good on the M part.

- V is for Views. Views are templates, that renders the final output. `utron` uses golang standard templates. so you don't have to learn anything new, just the text/template package to master views.

- C is for controllers.This is where the aplication logic stands, in order to achieve modularity, there are some facts that utron enforces to controllers. This subject is explained in more details below.

With the power of composition and inheriance, `utron` achieves a beautiful MVC workflow. I recomend you read the source code, it is well documented so as to demistify any magical unicorns.

We will create a TODO List application in `utron` to explore all components that makes `utron` MVC tick the source code of the final application is included in this repository and can be found here [utron todoMVC](https://github.com/gernest/utron/blob/master/fixtures/todo)

## [](https://github.com/gernest/utron#todo-list-application-with-utron)TODO list application with `utron`

## [](https://github.com/gernest/utron#project-structure)Project structure

This is the structure of the `todo` list application that will showcase how you can build web apps with `utron`.

```
todo
├── config
│ ├── app.json
│ ├── app.toml
│ └── app.yml
├── controllers
│ └── todo.go
├── models
│ └── todo.go
├── static
│ └── todo.css
├── views
│ ├── error.html
│ └── index.html
└── main.go

5 directories, 9 files
```

I have included three configuration files for clarity, but you are better off with just one.

## [](https://github.com/gernest/utron#configurations)Configurations

utron support yaml,json and toml configurations. In our todo app, we put the configuration files in the config directory. I have included all three formats for clarity, you can be just fine with either one of them.

`utron` searches for a file named `app.json`, or `app.yml` or `app.toml` in the config directory. The first to be found is the one to be used.

This is the content of `config/app.json` file

```
{
 "app_name": "utron web app",
 "base_url": "http://localhost:8090",
 "port": 8090,
 "verbose": false,
 "static_dir": "static",
 "view_dir": "views",
 "database": "postgres",
 "database_conn": "postgres://postgres:postgres@localhost/todo"
}
```

setting       | details
------------- | ---------------------------------------------------------------
app_name      | application name
base_url      | the base url to use in your views
port          | port number where the server will be listening to
verbose       | true value will make every state information logged to stdout
static_dir    | directory to serve static files e.g images,js or css
view_dir      | directory to look for views
database      | the name of the database you use, e.g postgres,mysql,foundation
database_conn | connection string to your database

If you havent specified explicity the location of the configuration directory, then it defaults to the directory named `config` in the current working directory.

## [](https://github.com/gernest/utron#models)Models

`utron` uses [gorm](https://github.com/jinzhu/gorm) library as its Object Relational Mapper. So, you wont need to learn anything fancy. In our todo app, we need to define `Todo` model that will be used to store our todo details.

In the file `models/todo.go` we define our todo model like this

```
package models

import (
 "time"

 "github.com/gernest/utron"
)

type Todo struct {
 ID int `schema: "-"`
 Body string `schema:"body"`
 CreatedAt time.Time `schema:"-"`
 UpdatedAt time.Time `schema:"-"`
}

func init() {
 utron.RegisterModels(&Todo{})
}
```

Notice that, we need to register our model, by calling `utron.RegisterModels(&Todo{})` in the `init` function otherwise `utron` wont be aware of the model.

`utron` will automatically create the table `todos` if it doesnt exist yet.

Dont be confused by the `schema` tag, I just added them since we will use [schema](https://github.com/gorilla/schema) package to decode form values(this has nothing to do with `utron`, you can use whatever form library you fancy)

## [](https://github.com/gernest/utron#controllers)Controllers

`utron` controllers are structs that implement `Controller` interface. To help make `utron` usable, `utron` provides `BaseController` which implements `Cntroller` interface and offers additional conveniences to help in composing reusable code.

You get all the benefits of `BaseController` by embeding it in your struct. our `TODO` Controller is in the `controller/todo.go`

```
package controllers

import (
 "net/http"
 "strconv"

 "github.com/gernest/utron"
 "github.com/gernest/utron/fixtures/todo/models"
 "github.com/gorilla/schema"
)

var decoder = schema.NewDecoder()

type TODO struct {
 *utron.BaseController
 Routes []string
}

func (t *TODO) Home() {
 todos := []*models.Todo{}
 t.Ctx.DB.Order("created_at desc").Find(&todos)
 t.Ctx.Data["List"] = todos
 t.Ctx.Template = "index"
 t.HTML(http.StatusOK)
}
func (t *TODO) Create() {
 todo := ⊧.Todo{}
 req := t.Ctx.Request()
 req.ParseForm()
 if err := decoder.Decode(todo, req.PostForm); err != nil {
 t.Ctx.Data["Message"] = err.Error()
 t.Ctx.Template = "error"
 t.HTML(http.StatusInternalServerError)
 return
 }

 t.Ctx.DB.Create(todo)
 t.Ctx.Redirect("/", http.StatusFound)
}

func (t *TODO) Delete() {
 todoID := t.Ctx.Params["id"]
 ID, err := strconv.Atoi(todoID)
 if err != nil {
 t.Ctx.Data["Message"] = err.Error()
 t.Ctx.Template = "error"
 t.HTML(http.StatusInternalServerError)
 return
 }
 t.Ctx.DB.Delete(⊧.Todo{ID: ID})
 t.Ctx.Redirect("/", http.StatusFound)
}

func NewTDDO() *TODO {
 return &TODO{
 Routes: []string{
 "get;/;Home",
 "post;/create;Create",
 "get;/delete/{id};Delete",
 },
 }
}

func init() {
 utron.RegisterController(NewTDDO())
}
```

Note we regisster our controller by calling `utron.RegisterController(NewTDDO())` in the `init` function so as to make `utron` aware of our controller. See Routing section below for more explanation of what the controller is doing.

## [](https://github.com/gernest/utron#routing)Routing

By regisstering a controller, there are two ways of assigning routes.

### [](https://github.com/gernest/utron#case-1--vanilla-routing)case 1- vanilla routing

By registering a Controller, routes are autogenerated for the controller methods. The format is `/:controler/:method` where `:controller` is the lowercase name of the Controller, and `:method` is its method in lowercase

so `(*TODO)Hello()` will map to `/todo/hello`

### [](https://github.com/gernest/utron#case-2--specifying-routes-field)case 2- Specifying Routes field

The user controller can define a field named `Routes` it should be of type `[]string`, then you can assign routes by appending route string to the Routes field.

This is a better explation from comments on the `router.go` file.

```
 // if there is any field named Routes, and it is of signature []string
 // then the field's value is used to overide the patterns defined earlier.
 //
 // It is not necessary for every user implementation to define method named Routes
 // If we cant find it then we just ignore its use( fallback to defaults).
 //
 // Route strings, are of the form "httpMethods;path;method"
 // where httMethod: is a coma separated http method strings
 // e.g GET,POST,PUT.
 // The case does not matter, you can use lower case or upper case charaters
 // or even mixed case, that is get,GET,gET and GeT will all be treated as GET
 //
 // path: Is a url path or pattern, utron uses gorilla mux package. So, everything you can do
 // with gorilla mux url path then you can do here.
 // e.g /hello/{world}
 // Don't worry about the params, they will be accessibe via .Ctx.Params field in your
 // controller.
 //
 // method: The name of the user Controller method to execute for this route.
```

So, that explains much the following lines in our `todo` app in `controllers/todo.go`

```
func NewTDDO() *TODO {
 return &TODO{
 Routes: []string{
 "get;/;Home",
 "post;/create;Create",
 "get;/delete/{id};Delete",
 },
 }
}
```

## [](https://github.com/gernest/utron#views)Views

`utron` views are golang templates. This is the content of `views/index.html``

```
<!DOCTYPE html>
<html>
<head lang="en">
 <meta charset="UTF-8">
 <title>Utron Todo MVC</title>
 <link href="/static/todo.css" rel="stylesheet">
</head>
<body>
<form method="post" action="/create">
 <table>
 <tr>
 <th>
 Create A TODO
 </th>
 </tr>
 <tr>
 <td>
 <input name="body">
 </td>
 <td>
 <button type="submit">create</button>
 </td>
 </tr>
 </table>
</form>
<table>
 <tr>
 <th>
 My TODO LIST
 </th>
 </tr>
 {{range $k,$v:=.List}}
 <tr>
 <td>
 {{$v.ID}}
 </td>
 <td>
 {{$v.Body}}
 </td>
 <td>
 <a href="/delete/{{$v.ID}}">
 <button>Delete</button>
 </a>
 </td>
 </tr>
 {{end}}
</table>
</body>
</html>
```

Note that we have access to `.List` in our view. This is set in the controller, additionally you can access the application configuration via `.Config` context.

Above is a simple golang template to render our `todo` list application.

## [](https://github.com/gernest/utron#the-maingo-file)The main.go file

```
package main

import (
 "github.com/gernest/utron"
 _ "github.com/gernest/utron/fixtures/todo/controllers"
 _ "github.com/gernest/utron/fixtures/todo/models"
)

func main() {
 utron.Run()
}
```

## [](https://github.com/gernest/utron#running-the-todo-app)Running the TODO app

In case you want to run the app we just created, it is included in this repository in [fixtures/todo](https://github.com/gernest/utron/blob/master/fixtures/todo)

- Prerequistite

  - a working database connection(postresql,mysql or foundation)
  - golang toolchain installed( and the `go` command is in your system PATH)

step 1 Install utron which will also include the todo app

```
$ go get github.com/gernest/utron
```

step 2 cd into the todo app directory

```
$ cd $GOPATH/src/github.com/gernest/utron/fixtures/todo
```

step 3 install dependency

```
$ go get github.com/gorilla/schema
```

step 4 edit `config/app.json` by setting database and database_conn to your values

step 5 run the app

```
go run main.go
```

If you see something like this

```
$ 2015/09/15 18:27:24 >>INFO>> staring server at http://localhost:8090
```

Then everything is okay, open `http://localhost:8090` on your browser to start writing your todos. If you experience anything different better open an issue.

## [](https://github.com/gernest/utron#screenshot)screenshot

[![todo app with utron](https://github.com/gernest/utron/raw/master/fixtures/todo.png)](https://github.com/gernest/utron/blob/master/fixtures/todo.png)

## [](https://github.com/gernest/utron#contributing)Contributing

Start with clicking the star button to make the author and his neighbors happy. Then fork it and submit a pull request for whatever change you want to be added to this project.

Or Open an issue for any questions.

## [](https://github.com/gernest/utron#author)Author

Geofrey Ernest [geofreyernest@live.com](mailto:geofreyernest@live.com)

twitter : [@gernesti](https://twitter.com/gernesti)

Facebook : [Geofrey Ernest](https://www.facebook.com/geofrey.ernest.35)

## [](https://github.com/gernest/utron#are-you-hiring)Are you hiring?

I have 2 years experience working with golang and 5 years doing web developement. I don't have any juicy CV because I'm in Tanzania, and almost everyone i graduated with has no job, never had one and in doubts if he/she will ever land one(unless of course you are lucky which I'm not)

And if you are a recruiter, any tips on where I'm doing it wrong will be highly appreciated, because I have my full trust that code will get me out of this abysmal poverty.

## [](https://github.com/gernest/utron#aknowledgement)Aknowledgement

These are amzaing projects that made `utron` possible

- [goriila mux](https://github.com/gorilla/mux)
- [ita](https://github.com/gernest/ita)
- [gorm](https://github.com/jinzhu/gorm)
- [alice](https://github.com/justinas/alice)
- [golang](http://golang.org)

## [](https://github.com/gernest/utron#roadmap)Roadmap

- Fix a lot of typos( didnt tell you thet English is my third language).

## [](https://github.com/gernest/utron#licence)Licence

This project is released under MIT licence see [LICENCE](https://github.com/gernest/utron/blob/master/LICENCE) for more details.
