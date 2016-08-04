
[Original URL](https://medium.com/@richardeng/a-word-from-the-beegoist-d562ff8589d7)

> You have some familiarity with the Go language. I highly recommend you follow this Go tutorial.You've installed Go and Beego on your computer. There are plenty of good online resources to help...

[]()You have some familiarity with the Go language. I highly recommend you follow this [Go tutorial](http://tour.golang.org/).

[]()You've installed [Go](http://golang.org/) and [Beego](http://beego.me/) on your computer. There are plenty of good online resources to help you here (for [example](https://medium.com/@richardeng/in-the-beginning-61c7e63a3ea6)). It's really quite easy.

[]()You have basic knowledge of CSS, HTML, and databases. You have at least one database package installed on your computer such as [MySQL](http://www.mysql.com/) (Community Edition) or [SQLite](http://www.sqlite.org/). I have SQLite because it's much easier to use.

[]()You have some experience writing software; basic skills are assumed. If you studied computer programming in school, then you're off to a good start.

[]()You will be using your favourite programming editor in conjunction with the command line. I use [LiteIDE](https://code.google.com/p/liteide/) (on the Mac), but I can suggest alternatives such as [TextMate](http://macromates.com/) for the Mac, [Notepad++](http://notepad-plus-plus.org/) for Windows, and [vim](https://medium.com/@richardeng/back-to-the-future-9db24d6bcee1) for Linux.

[]()These basic assumptions define the target audience for the tutorial. If you're a programming veteran, though, you'll breeze through it and hopefully gain much useful knowledge, as well.

[]()First, we must create a Beego project. We'll call it '[ACME](http://en.wikipedia.org/wiki/Acme_Corporation)'. From the command line, change directory (cd) to $GOPATH/src and enter:

```
$ bee new acme
```

[]()The following directory structure will be created:

```
acme
....conf
....controllers
....models
....routers
....static
........css
........img
........js
....tests
....views
```

[]()Note that Beego is a MVC framework (Model/View/Controller), which means that your application will be separated into three general sections. _Model_ refers to the internal database structure of your application. _View_ is all about how your application looks on the computer screen; in our case, this includes HTML and CSS code. And _Controller_ is where you have your business logic and user interactions.

[]()You can immediately compile and run your application by changing directory (cd acme) and typing:

```
$ bee run
```

[]()In your browser, go to <http://localhost:8080> to see the running application. It doesn't do anything fancy right now; it simply greets you. But upon this foundation, we shall raise an impressive edifice.

[]()To follow along, you may [download the source code](https://github.com/horrido/acme) for this tutorial. Cd to $GOPATH/src and unzip the file. This will, however, replace the project you just created, since both use the same name ('acme'). I suggest you defer this step till later in the tutorial. Or rename your original project.

[]()The user account management component provides the following functionality:

[]()The essence of a web application is the mapping of URLs (webpages) to the server functions that will process the HTTP requests. This mapping is what generates the work flow in the application. In Beego, the mapping is defined within the 'router'. Here's the code for our router (look at router.go in the 'routers' directory):

```
beego.Router("/home", &controllers.MainController{})
beego.Router("/user/login/:back", &controllers.MainController{}, "get,post:Login")
beego.Router("/user/logout", &controllers.MainController{}, "get:Logout")
beego.Router("/user/register", &controllers.MainController{}, "get,post:Register")
beego.Router("/user/profile", &controllers.MainController{}, "get,post:Profile")
beego.Router("/user/verify/:uuid({[0-9A-F]{8}-[0-9A-F]{4}-4[0-9A-F]{3}-[89AB][0-9A-F]{3}-[0-9A-F]{12}})", &controllers.MainController{}, "get:Verify")
beego.Router("/user/remove", &controllers.MainController{}, "get,post:Remove")
beego.Router("/notice", &controllers.MainController{}, "get:Notice")
```

[]()For example, in the line for 'login', "get,post:Login" says that both the GET and POST operations are handled by the 'Login' function. The ':back' is a request parameter; in this case, it tells us what page to return to after successful login.

[]()In the line for 'verify', the ':uuid' is a request parameter that must match the [regular expression](http://en.wikipedia.org/wiki/Regular_expression) for a Version 4 UUID. The GET operation is handled by the 'Verify' function.

[]()More on this when we talk about controllers.

[]()Note that I've added '/home' to the first line in the router (it was originally '/'). This makes it convenient to go to the home page, which we often do in our application.

[]()The database model for a user account is represented by the following struct:

```
package models

import (
 "github.com/astaxie/beego/orm"
 "time"
)

type AuthUser struct {
 Id int
 First string
 Last string
 Email string `orm:"unique"`
 Password string
 Reg_key string
 Reg_date time.Time `orm:"auto_now_add;type(datetime)"`
}

func init() {
 orm.RegisterModel(new(AuthUser))
}
```

[]()Place this in models.go in the 'models' directory. Ignore the init() for the time being.

[]()'Id' is the primary key which is auto-incremented in the database. We also have 'First' and 'Last' names. 'Password' contains the _binary data_ for the [PBKDF2 hash](http://en.wikipedia.org/wiki/PBKDF2) of the _plaintext_ password; the 'string' type is the most convenient way to store the value in the database.

[]()'Reg_key' contains the [UUID](http://en.wikipedia.org/wiki/Universally_unique_identifier) string that is used for account verification (via email). 'Reg_date' is the timestamp indicating the time of registration.

[]()The funny-looking string literals associated with both 'Email' and 'Reg_date' are used to tell the database the special requirements of these fields. 'Email' must be a unique key. 'Reg_date' will be automatically assigned the date and time of database insertion.

[]()By the way, don't be scared of the PBKDF2 and UUID references. PBKDF2 is simply a way to securely store a user's password in the database. A UUID is a unique identifier that can be used to ensure the identity of the user for verification purposes.

[]()For our CSS template design, I've chosen the [Stardust](http://www.freewebtemplates.com/download/free-website-template/stardust-141989295/) theme (pictured at the start of this article). We will use its index.html as a basis for the view layout.

[]()Place the appropriate files from the Stardust theme into the 'css' and 'img' directories of 'static' directory. The link statement in the header of index.html must be amended to:

```
<link href="/static/css/default.css" rel="stylesheet" type="text/css" />
```

[]()And all references to image gifs and jpegs in index.html and default.css must point to '/static/img/'.

[]()The view layout contains a header section, a footer section, a sidebar section, and the central section where most of the action will take place. We will be using Go's templating facility which allows us to replace embedded codes, signified by '{{' and '}}', with actual HTML. Here's our basic-layout.tpl (.tpl for 'template'):

```
{{.Header}}
{{.LayoutContent}}
{{.Sidebar}}
{{.Footer}}
```

[]()Since every webpage in our application will need to adhere to this basic layout, we need a common method to set it up (look at default.go):

```
func (this *MainController) activeContent(view string) {
 this.Layout = "basic-layout.tpl"
 this.LayoutSections = make(map[string]string)
 this.LayoutSections["Header"] = "header.tpl"
 this.LayoutSections["Sidebar"] = "sidebar.tpl"
 this.LayoutSections["Footer"] = "footer.tpl"
 this.TplNames = view + ".tpl"

 sess := this.GetSession("acme")
 if sess != nil {
 this.Data["InSession"] = 1 // for login bar in header.tpl
 m := sess.(map[string]interface{})
 this.Data["First"] = m["first"]
 }
}
```

[]()The template parameters, such as '.Sidebar', correspond to the keys used in the LayoutSections map. '.LayoutContent' is a special, implicit template parameter. We'll get to the GetSession stuff further below.

[]()Of course, we need to create the various template files (such as footer.tpl) in the 'views' directory. From index.html, we can carve out the header section for header.tpl:

```
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="content-type" content="text/html; charset=utf-8" />
<title>StarDust by Free Css Templates</title>
<meta name="keywords" content="" />
<meta name="description" content="" />
<link href="/static/css/default.css" rel="stylesheet" type="text/css" />
</head>

<body>
<!-- start header -->
<div id="header-bg">
 <div id="header">
 <div align="right">{{if .InSession}}
 Welcome, {{.First}} [<a href="http://localhost:8080/logout">Logout</a>|<a href="http://localhost:8080/profile">Profile</a>]
 {{else}}
 [<a href="http://localhost:8080/login/home">Login</a>]
 {{end}}
 </div>
 <div id="logo">
 <h1><a href="#">StarDust<sup></sup></a></h1>
 <h2>Designed by FreeCSSTemplates</h2>
 </div>
 <div id="menu">
 <ul>
 <li class="active"><a href="http://localhost:8080/home">home</a></li>
 <li><a href="#">photos</a></li>
 <li><a href="#">about</a></li>
 <li><a href="#">links</a></li>
 <li><a href="#">contact </a></li>
 </ul>
 </div>
 </div>
</div>
<!-- end header -->
<!-- start page -->
<div id="page">
```

[]()I leave it as an exercise for you to carve out the sections for sidebar.tpl and footer.tpl.

[]()Note the lines in bold. I added them to facilitate a "login bar" at the top of every webpage. Once you've logged into the application, you will see the bar as so:

[]() ![](https://d262ilb51hltx0.cloudfront.net/max/800/1*1OpYy1ISYGUaBy0U_RJ75w.png)

[]()This login bar works in conjunction with the GetSession code snippet we saw in activeContent(). The logic is, if the user is logged in (ie, there is a non-nil session), then we set the InSession parameter to a value (any value), which tells the templating engine to use the "Welcome" bar instead of "Login". We also extract the user's first name from the session so that we can present the friendly affectation "Welcome, Richard".

[]()The home page, represented by index.tpl, uses the following snippet from index.html:

```
 <!-- start content -->
 <div id="content">
 <div class="post">
 <h1 class="title">Welcome to StarDust</h1>
 // to save space, I won't enter the remainder
 // of the snippet
 </div>
 <!-- end content -->
```

[]()The template files for the user module reside in the 'user' directory within 'view', just to keep things tidy. So, for example, the call to activeContent() for login is:

```
this.activeContent("user/login")
```

[]()A controller handles requests by handing them off to the appropriate function or 'method'. We only have one controller for our application and it's defined in default.go. The default method Get() for handling a GET operation is associated with our home page:

```
func (this *MainController) Get() {
 this.activeContent("index")

 //bin //boot //cdrom //dev //etc //home //initrd.img //initrd.img.old //lacie //lib //lib64 //lost+found //media //mnt //nfs //opt //proc //render //root //run //sbin //share //srv //sys //tmp //usr //var //vmlinuz //vmlinuz.old This page requires login
 sess := this.GetSession("acme")
 if sess == nil {
 this.Redirect("/user/login/home", 302)
 return
 }
 m := sess.(map[string]interface{})
 fmt.Println("username is", m["username"])
 fmt.Println("logged in at", m["timestamp"])
}
```

[]()I've made login a requirement for accessing this page. Logging in means creating a session, which by default expires after 3600 seconds of inactivity. A session is typically maintained on the client side by a 'cookie'.

[]()In order to support sessions in the application, the 'SessionOn' flag must be set to true. There are two ways to do this:

1. []()Insert 'beego.SessionOn = true' in the main program, main.go.
2. []()Insert 'sessionon = true' in the configuration file, app.conf, which can be found in the 'conf' directory.

[]()I chose #1\. (But note that I used the configuration file to set 'EnableAdmin' to true: 'enableadmin = true'. EnableAdmin allows you to use the Supervisor Module in Beego that keeps track of CPU, memory, Garbage Collector, threads, etc., via port 8088: <http://localhost:8088>.)

[]()The main program is also where we initialize the database to be used with the ORM (Object Relational Mapping) component. ORM makes it more convenient to perform database activities within our application. The main program's init():

```
func init() {
 orm.RegisterDriver("sqlite", orm.DR_Sqlite)
 orm.RegisterDataBase("default", "sqlite3", "acme.db")
 name := "default"
 force := false
 verbose := false
 err := orm.RunSyncdb(name, force, verbose)
 if err != nil {
 fmt.Println(err)
 }
}
```

[]()To use SQLite, we must import 'go-sqlite3', which can be installed with the command:

```
$ go get github.com/mattn/go-sqlite3
```

[]()As you can see in the code snippet, the SQLite driver must be registered and 'acme.db' must be registered as our SQLite database.

[]()Recall in models.go, there was an init() function:

```
func init() {
 orm.RegisterModel(new(AuthUser))
}
```

[]()The database model has to be registered so that the appropriate table can be generated. To ensure that this init() function is executed, you must import 'models' without actually using it within the main program, as follows:

```
import _ "acme/models"
```

[]()RunSyncdb() is used to autogenerate the tables when you start the program. (This is very handy for creating the database tables without having to **manually** do it in the database command line utility.) If you set 'force' to true, it will drop any existing tables and recreate them.

[]()User.go contains all the methods for handling login, registration, profile, etc. There are several third-party packages we need to import; they provide support for email, PBKDF2, and UUID. But first we must get them into our project...

```
$ go get github.com/alexcesaro/mail/gomail
$ go get github.com/twinj/uuid
```

[]()I originally got **github.com/gokyle/pbkdf2**, but this package was pulled from Github, so you can no longer get it. I've incorporated this package into my source under the 'utilities' folder, and the import is:

```
import pk "acme/utilities/pbkdf2"
```

[]()The 'pk' is a convenient alias so that I don't have to type the rather unwieldy 'pbkdf2'.

[]()It's pretty straightforward to use ORM. The basic pattern is to create an ORM object, specify the 'default' database, and select which ORM operation you want, eg,

```
o := orm.NewOrm()
o.Using("default")
err := o.Insert(&user) // or
err := o.Read(&user, "Email") // or
err := o.Update(&user) // or
err := o.Delete(&user)
```

[]()Note, however, that there is currently a bug where Insert() cannot accept '&user'. To insert a record, then, you must pass 'user' by value, not by reference:

```
err := o.Insert(user)
```

[]()Plaintext passwords should never be stored on the server. We use the PBKDF2 package to convert a password into a '[hash](http://en.wikipedia.org/wiki/Cryptographic_hash_function)' value to ensure security. The hash structure consists of two parts: 1) a 32-byte _hash_; and 2) a 16-byte _salt_, which is used to compute the hash.

[]()Comparing passwords, then, will be a matter of comparing their hash values. This is why password recovery is usually impossible at most websites; they'll ask you to reset your password in the event you forgot your old password.

[]()Storing large binary values (larger than, say, 64 bits) to a database is problematic. To save the password hash, which is a 48-byte value, we must first convert it to a string typeª. We use the 'bytes' and 'binary' packages for this. The idea is to "write" out the binary values for the Hash and Salt members of the _HashPassword_ struct into a byte buffer, and then convert the buffer to a string type. It's a neat trick that takes advantage of the fact that Go strings can hold 8 bits (or one full byte) of a character, even the null character.

[]()[ª It's possible to convert the hash to a _textual_ representation (ie, a string), but that would take up a lot more space and involve more complicated conversions.]

```
buf := new(bytes.Buffer)
err := binary.Write(buf, binary.LittleEndian, h.Hash)
if err != nil {
 flash.Error("Internal error")
 flash.Store(&this.Controller)
 return
}
err = binary.Write(buf, binary.LittleEndian, h.Salt)
if err != nil {
 flash.Error("Internal error")
 flash.Store(&this.Controller)
 return
}
b := buf.Bytes()
fmt.Printf("password hash/salt is: %x\n", b)
user.Password = string(b)
```

[]()Similarly, to "read" in the binary values for Hash and Salt from the byte buffer:

```
var y hStruct

ibuf := bytes.NewReader([]byte(user.Password))
err = binary.Read(ibuf, binary.LittleEndian, &y.Hash)
if err != nil {
 flash.Error("Internal error")
 flash.Store(&this.Controller)
 return
}
err = binary.Read(ibuf, binary.LittleEndian, &y.Salt)
if err != nil {
 flash.Error("Internal error")
 flash.Store(&this.Controller)
 return
}
```

[]()'hStruct' is a struct type with Hash and Salt as byte array members. This tells binary.Read how much data to "read".

[]()By the way, Beego provides a way to present notifications on your webpage through the use of 'flash'. Basically, you create a 'flash' object, give it your notification message, store the flash in the controller, and then retrieve the message in the template file, eg,

```
flash := beego.NewFlash()
flash.Error("You've goofed!") // or
flash.Notice("Well done!")
flash.Store(&this.Controller)
```

[]()And in your template file, reference the Error flash with:

```
{{if .flash.error}}
<h3>{{.flash.error}}</h3>
 
{{end}}
```

[]()Once the user posts a request (by pressing the Submit button, for example), our handler must extract and validate the form input. So, first, check that we have a POST operation:

```
if this.Ctx.Input.Method() == "POST" {
```

[]()Let's get a form element, say, email:

```
email := this.GetString("email")
```

[]()The string "email" is the same as in the HTML form:

```
<input name="email" type="text" />
```

[]()To validate it, we create a validation object, specify the type of validation, and then check to see if there are any errors:

```
valid := validation.Validation{}
valid.Email(email, "email") // must be a proper email address
if valid.HasErrors() {
 for _, err := range valid.Errors {
```

[]()What you do with the errors is up to you. I like to present all of them at once to the user, so as I go through the range of valid.Errors, I add them to a map of errors that will eventually be used in the template file. Hence, the full snippet:

```
if this.Ctx.Input.Method() == "POST" {
 email := this.GetString("email")
 password := this.GetString("password")
 valid := validation.Validation{}
 valid.Email(email, "email")
 valid.Required(password, "password")
 if valid.HasErrors() {
 errormap := []string{}
 for _, err := range valid.Errors {
 errormap = append(errormap, "Validation failed on "+err.Key+": "+err.Message+"\n")
 }
 this.Data["Errors"] = errormap
 return
 }
```

[]()We've looked at the major pieces of the controller. Now, we get to the meat of the application, the user management methods:

[]()Recall that we saw references to these functions in the router. The router associates each URL (and HTTP request) with the corresponding controller method.

[]()Let's look at the pseudocode for this method:

```
if the HTTP request is "POST" then
 Validate the form (extract the email address and password).
 Read the password hash from the database, keying on email.
 - this requires binary.Read()
 Compare the submitted password with the one on record.
 Create a session for this user.
endif
```

[]()In order to compare passwords, we need to give pk.MatchPassword() a variable with members 'Hash' and 'Salt' that are **byte slices**. Hence,

```
var x pk.PasswordHash

x.Hash = make([]byte, 32) // create a slice
copy(x.Hash, y.Hash[:32]) // copy array value from database
x.Salt = make([]byte, 16)
copy(x.Salt, y.Salt[:16])
if !pk.MatchPassword(password, &x) {
 flash.Error("Bad password")
 flash.Store(&this.Controller)
 return
}
```

[]()Creating a session is trivial, but we want to store some useful information in the session, as well. So we make a map and store first name, email address, and the time of login:

```
m := make(map[string]interface{})
m["first"] = user.First
m["username"] = email
m["timestamp"] = time.Now()
this.SetSession("acme", m)
this.Redirect("/"+back, 302) // go to previous page after login
```

[]()Incidentally, the name "acme" passed to SetSession is completely arbitrary; you just need to reference the same name to get the same session.

[]()This one is trivially easy. We delete the session and redirect to the home page.

```
if the HTTP request is "POST" then
 Validate the form.
 Create the password hash for the submitted password.
 Prepare new user record.
 Convert the password hash to string type.
 - this requires binary.Write()
 Generate a UUID and insert the user into database.
 Send a verification email.
 Flash a message on the notification page.
endif
```

[]()To send a verification email to the user, we use **gomail**...

```
link := "http://localhost:8080/user/verify/" + u // u is UUID
host := "smtp.gmail.com"
port := 587
msg := gomail.NewMessage()
msg.SetAddressHeader("From", "acmecorp@gmail.com", "ACME Corporation")
msg.SetHeader("To", email)
msg.SetHeader("Subject", "Account Verification for ACME Corporation")
msg.SetBody("text/html", "To verify your account, please click on the link: <a href=\""+link+"\">"+link+"</a><br><br>Best Regards,<br>ACME Corporation")
m := gomail.NewMailer(host, "youraccount@gmail.com", "YourPassword", port)
if err := m.Send(msg); err != nil {
 return false
}
```

[]()I chose Gmail as my email relay (you will need to open your own account). Note that Gmail ignores the "From" address (in our case, "acmecorp@gmail.com") because Gmail does not permit you to alter the sender address in order to prevent phishing.

[]()This special router method is for displaying a flash message on a notification page. It's not really a user module function; it's general enough that you can use it in many other places.

[]()We've already discussed all the pieces in this function. The pseudocode is:

```
Login required; check for a session.
Get user record from database, keyed on email (or username).
if the HTTP request is "POST" then
 Validate the form.
 if there is a new password then
 Validate the new password.
 Create the password hash for the new password.
 Convert the password hash to string type.
 endif
 Compare submitted current password with the one on record.
 Update the user record.
 - update the username stored in session
endif
```

[]()The verification email contains a link which, when clicked by the recipient, causes Verify() to process the UUID. Verify() attempts to read the user record, keyed on the UUID or registration key, and if it's found, then the registration key is removed from the database.

[]()Remove() is pretty much like Login(), except that instead of creating a session, you delete the user record from the database.

[]()I left out one user management method: What if the user has forgotten his password? We should provide a way to reset the password. I leave this as an exercise for you. All the pieces you need are in this tutorial. (Hint: You'll need to do it in a way similar to Registration verification. You should add a new Reset_key to the AuthUser table. And make sure the user email address exists in the database before you send the Reset email!)

[]()[Okay, so I'll give you the [exercise solution](https://github.com/horrido/acme-exercise). I'm not cruel.]

[]()Let's review what we've learned. We covered the mapping of URLs to request handlers in the router. We showed how to incorporate a CSS template design into our views. We discussed the ORM package, as well as the _bytes_ and _binary_ packages, and how they're used to perform database operations. We examined a number of third-party utilities useful in writing our application. The end result is a component useful in many scenarios.

[]()This is a great deal of material in a tutorial, but I believe it's the best way to get started in writing a _practical_ application.
