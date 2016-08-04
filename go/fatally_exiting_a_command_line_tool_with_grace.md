# Fatally exiting a command line tool, with grace

[Original URL](https://medium.com/@matryer/golang-advent-calendar-day-three-fatally-exiting-a-command-line-tool-with-grace-874befeb64a4)

> Go is really great for writing command line tools because you can do some pretty powerful things, with very little code.Like C, the `main` function is where the program starts, and when the main...

---

Go is really great for writing command line tools because you can do some pretty powerful things, with very little code.

Like C, the `main` function is where the program starts, and when the main function exits, the program exits. Nice and simple. This means you can use the defer keyword to run code just before your program exits (such as closing files, cleaning up database connections, and even saying goodbye.)

A simple program that reads items from a database and writes them to a text file might look like this:

```go
package main

func main() {

 // dial a database...
 db, err := mgo.Dial("localhost")
 if err != nil {
  log.Fatalln(err)
 }
 defer db.Close()

 // open our destination file
 f, err := os.Open("dest.txt")
 if err != nil {
  log.Fatalln(err)
 }
 defer f.Close()

 // get a function to do the work for us
 doCopy(db, f)

}
```

The trouble is, `log.Fatalln` causes the program to exit immediately, not allowing any deferred functions to run. Similarly, calls to `os.Exit` would do the same. [See this happening for yourself](http://play.golang.org/p/x9dkjA-4Ok).

> DID YOU KNOW: Deferred functions are called in the reverse order in which they were added.

To solve this problem, we can use a variable to hold an error, and defer a function that will run last (since we defer it first,) that checks the error and exits with a suitable code.

Modify the main function:

```go
package main

func main() {
 var err error
 
 defer func(){
  if err != nil {
   log.Fatalln(err)
  }
 }()

 // dial a database...
 db, err := mgo.Dial("localhost")
 if err != nil {
  return
 }
 defer db.Close()

 // open our destination file
 f, err := os.Open("dest.txt")
 if err != nil {
  return
 }
 defer f.Close()

 // get a function to do the work for us
 doCopy(db, f)

}
```

Now, should something go wrong, the deferred calls to db.Close, and f.Close will be called before the main function exits. The error will still be logged out as before. [See this happening in our simplified version](http://play.golang.org/p/6pBCp_47cs).

Please post links to other solutions to these kinds of problems, or alternative solutions to this one, the little tips and tricks are great to have in the old toolbelt.
