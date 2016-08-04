
[Original URL](https://medium.com/quick-mobile/revel-framework-introduction-en-700f83ea0c3b)

> When I started programming in GO I thought: "Man, it must be very nice to have a web application based on GO". Then I started to search how to do it. However, as soon as I realized that it was...

When I started programming in GO I thought: "Man, it must be very nice to have a web application based on GO". Then I started to search how to do it. However, as soon as I realized that it was extremally complicated, I decided to look for a framework to make it easier. That's how I end up founding Revel, a simple framework that solved my problem. This post is to introduce Revel to newcomers.

Building an environment using GO is a little bit problematic, so I created a docker image. Although, you can always do the old way and install it.

You can pull my docker image accessing: <https://hub.docker.com/r/walissoncasonatto/revel-docker/>

Or you can do the old way: <https://revel.github.io/tutorial/gettingstarted.html>

If you have chosen to use docker:

```
$ docker run --name docker-revel -e APP_NAME=myappname -v <path-to-application>:/root/go/src/myapps walissoncasonatto/revel-docker
```

If you have chosen to install it:

```
$ Revel new <new_application_name>
$ Revel run $GOPATH/src/<new_application_name>
```

> In this case, the app will be stored on the following gopath: $GOPATH/src/<new_application_name>.

> Now it is time to work!<br>
> We are going to do a simple calculator that gets two numbers and sums them.

> If you have chosen to use Docker, there is a problem that you might have. You can't change the files created by Docker, because Docker created them as root. Although, it will not be a problem if you run your editor as root or use the following commands to make them editable:

> find /path/to/base/dir -type d -exec chmod 777 {} +

> find /path/to/base/dir -type f -exec chmod 666 {} +

There is no need to stop Revel to edit the project, because it is auto-compiled on edit (in dev mode).

First, we need to set a route on <app_root>/conf/routes.

By default it has some routes added, so you can figure out how it works.

We are going to configure it in this way:

```
# Routes
# This file defines all application routes (Higher priority routes first)
# ~~~~

module:testrunner

GET / App.Index
GET /calc/ Calc.Index

# Ignore favicon requests
GET /favicon.ico 404

# Map static resources from the /app/public folder to the /public path
GET /public/*filepath Static.Serve(“public”)

# Catch all
* /:controller/:action :controller.:action
```

It has a simple syntax:

```
REQUSEST_TYPE PATH CONTROLLER.ACTION
GET / App.Index
```

Now that we have a route, we can create our controller. It should be located on <app_root_directory>/app/controllers .

Let's call it calc.go.

We need to add "package controller" in the beginning of our code, and by default all controllers should extend a class Controller located on Revel's library. The controller name needs to be the same declared on routes file.

```
package controllers

import (
 “github.com/revel/revel”
)

type Calc struct {
 *revel.Controller
}
```

Now we should make a method to render the index page. This method is the one called on routes file.

```
[...]
func (c Calc) Index() revel.Result {
 title := "calculator"
 return c.Render(title)
}
```

Revel pages can receive as many attributes as you want. Let's do the title be one of the attributes.

```
package controllers

import (
 "github.com/revel/revel"
)

type Calc struct {
 *revel.Controller
}

func (c Calc) Index() revel.Result {
 title := "calculator"
 return c.Render(title)
}
```

Let's create a basic one. By default they are located on: /<controller_name>/<action>

But first we need to create the calc's directory on: <app_root_directory>/app/views

Inside calc's directory we can create our Index.html.

> Be careful! GO is a case sensitive language.

```
{{template "header.html" .}}

<p> It Works!</p>

{{template "footer.html" .}}
```

Revel has a place to tests, it is located on:

<app_root_directory>/tests

For now, let's do the basic. We are going to create a test only to see if the index page is working.

```
package tests

import (
 "github.com/revel/revel/testing"
)

type CalcTest struct {
 testing.TestSuite
}

func (t *CalcTest) Before() {
 println("Set up")
}

func (t *CalcTest) TestCalcPageWork() {
 t.Get("/calc/")
 t.AssertOk()
 t.AssertContentType("text/html; charset=utf-8")
}

func (t *CalcTest) After() {
 println("Tear down")
}
```

Revel test classes should extend testing.TestSuite from Revel library.

If your Revel still running you can test it, but if it's not, you might run it again using _docker start <your_docker_container_name>_ or _revel run $GOPATH/<project_name>_

> Docker is set to do not override your application, so it's a great opportunity to stop installing applications and start using Docker.

You can access these links to test it:

- <http://localhost:9000>

- [http://docker_ip:9000](http://%3Cdocker_ip%3E:9000).

We can check our calc out at <http://localhost:9000/calc> and our tests at [http://localhost:

<span class="__cf_email__">[email protected]</span>](http://localhost:9000/@tests)

.

We did the basic, now it's time to make our calc a real calculator!

First we should add a POST route in our calc's actions.

```
POST /calc/:action Calc.:action
```

By using :action we are making it generic, so any action can be used if the method exists in the controller

> e.g.: "/calc/Sum" will call the Sum method on Calc's Controller.

Our routes file might look like this:

```
# Routes
# This file defines all application routes (Higher priority routes first)
# ~~~~

module:testrunner

GET / App.Index
GET /calc/ Calc.Index
POST /calc/:action Calc.:action

# Ignore favicon requests
GET /favicon.ico 404

# Map static resources from the /app/public folder to the /public path
GET /public/*filepath Static.Serve("public")

# Catch all
* /:controller/:action :controller.:action
```

Now we are able to code our Sum method. It is going to sum two values and redirect the total to the index:

```
[...]
func (c Calc) Sum(number1 int, number2 int) revel.Result {
 total := number1 + number2
 return c.Redirect(routes.Calc.Index(total))
}
```

Our index should receive our total:

```
[...]
func (c Calc) Index(total int) revel.Result {
 title := "calculator"
 return c.Render(title,total)
}
[...]
```

Probably you noticed that we are using the method _routes.Calc.Index_ without import it. In this way, it will never work! Let's import it.

```
package controllers

import (
 "github.com/revel/revel"
 "myapps/webapp2/app/routes"
)
```

> Docker creates a myapps directory before the directory of your application, e.g. my application calls webapp2\. If you are using Docker, your application directory might look like mine, but if you are not using Docker you can remove myapps from the path.

Our controller should look like this:

```
package controllers

import (
 "github.com/revel/revel"
 "myapps/webapp2/app/routes"
)

type Calc struct {
 *revel.Controller
}

func (c Calc) Index(total int) revel.Result {
 title := "calculator"
 return c.Render(title,total)
}

func (c Calc) Sum(number1 int, number2 int) revel.Result {
 total := number1 + number2
 return c.Redirect(routes.Calc.Index(total))
}
```

Last but not least, our view:

First we create a POST form to submit our values:

```
<form method="POST">

<input type="submit" value="Sum" formaction="{{url "Calc.Sum"}}">
</form>
```

Then we create a table to put our fields:

```
<table>
 <th>
 <td colspan="6">CALCULATOR</td>
 </th>
 <tr>
 <td colspan="2">
 <input type="text" name="number1">
 </td>
 <td colspan="2">
 <input type="text" name ="number2">
 </td>
 <td colspan="2">
 <input name="total">
 </td>
 </tr>
 <input type="submit" value="Sum" formaction="{{url "Calc.Sum"}}">

</table>
```

We also need to add the Revel integration. _For detailed information, check Revel documentation_. It's going to look like this:

```
{{template "header.html" .}}
<form method="POST">
 <table>
 <th>
 <td colspan="6">CALCULATOR</td>
 </th>
 <tr>
 {{with $number1 := field "number1" .}}
 <td colspan="2"><input type="text" name="{{$number1.Name}}" value = "{{$number1.Value}}" style="width:50px;"></td>
 {{end}}
 {{with $number2 := field "number2" .}}
 <td colspan="2"><input type="text" name ="{{$number2.Name}}" value = "{{$number2.Value}}" style="width:50px;"></td>
 {{end}}
 {{with $total := field "total" .}}
 <td colspan="2"><input name="{{$total.Name}}" value = "{{$total.Value}}" readonly="readonly" style="width:50px;"></td>
 {{end}}
 </tr>
 </table>
 <input type="submit" value="Sum" formaction="{{url "Calc.Sum"}}">
</form>
{{template "footer.html" .}}
```

That's all! We can access our calculator and check it working.

Revel doesn't have UnitTest support yet, but once it is a GO application you can use an GO library to test.

It is also possible to implement unittests using revel interface, however you can not get some important data like coverage or make it automated.

Let's add another test:

First we extract the Sum logic to make it testable:

```
[...]
func (c Calc) Sum(number1 int, number2 int) revel.Result {
 total := c.DoSum(number1,number2)
 return c.Redirect(routes.Calc.Index(total))
}
func (c Calc) DoSum(number1 int, number2 int) int{
 return number1+number2
}
```

On our test class we import the controllers:

```
package tests

import (
 "github.com/revel/revel/testing"
 "myapps/webapp2/app/controllers"
)
```

We need to add an attribute to represent the Calc controller:

```
type CalcTest struct {
 testing.TestSuite
 calc controllers.Calc
}
```

And now that we can code a method to test DoSum:

```
func (t *CalcTest) TestDoSum(){
 expected := 10
 current := t.calc.DoSum(3,7)
 t.Assert(expected == current)
}
```

By the end, our test's class end up like this:

```
package tests
import (
 "github.com/revel/revel/testing"
 "myapps/webapp2/app/controllers"
)

type CalcTest struct {
 testing.TestSuite
 calc controllers.Calc
}

func (t *CalcTest) Before() {
 println("Set up")
}

func (t *CalcTest) TestCalcPageWork() {
 t.Get("/calc/")
 t.AssertOk()
 t.AssertContentType("text/html; charset=utf-8")
}

func (t *CalcTest) TestDoSum(){
 expected := 10
 current := t.calc.DoSum(3,7)
 t.Assert(expected == current)
}

func (t *CalcTest) After() {
 println("Tear down")
}
```

That's all!

I believe you are introduced enough to make the subtraction method and the damn tests.
