
[Original URL](https://medium.com/@matryer/golang-advent-calendar-day-seventeen-io-reader-in-depth-6f744bb4320b)

> Single method interfaces end up being extremely powerful -- more so than even the language creators thought.This article aims to convince you to use io.Reader in your own code wherever you can.Let's...

Single method interfaces end up being extremely powerful -- more so than even the language creators thought.

> This article aims to convince you to use io.Reader in your own code wherever you can.

Let's have a look at io.Reader:

```
type Reader interface {
 Read(p []byte) (n int, err error)
}
```

Pretty simple; a Reader is any type that implements the `Read` method.

> Check out the comments from the standard library documentation for a little more context for [Reader](https://golang.org/pkg/io/#Reader).

For those unfamiliar with these interfaces; you pass in a slice of bytes, and Read is asked to fill it with its data -- which is does until it runs out of data. It returns the number of bytes read (in `n`) or an error if something goes wrong. Additionally, if it has finished reading, it will return a special marker error called `io.EOF` (end of file).

There are many kinds of Reader types available in the standard library, and you've almost certainly used at least one of them.

If you open a file for reading, the object returned is an `os.File`, which is a Reader (it implements the Read method):

```
var r io.Reader
var err error
r, err = os.Open("file.txt")
```

You can also make a Reader from a normal string using `strings.NewReader`:

```
var r io.Reader
r = strings.NewReader("Read will return these bytes")
```

The body data from an `http.Request` is a Reader:

```
var r io.Reader
r = request.Body
```

A bytes.Buffer is a Reader:

```
var r io.Reader
var buf bytes.Buffer
r = &buf
```

There are many more throughout the standard library -- and in most third-party packages too, since it's good practice to use them wherever you can.

Now we have a few Reader types -- let's explore ways in which they can be used.

You can read from them directly (this turns out to be the least useful use case):

```
p := make([]byte, 256)
n, err := r.Read(p)
```

`[ioutil.ReadAll](https://golang.org/pkg/io/ioutil/#ReadAll)` lets you read everything from a Reader, and get the raw []byte data:

```
b, err := ioutil.ReadAll(r)
```

`[io.Copy](https://golang.org/pkg/io/#Copy)` lets you read ALL bytes from an io.Reader, and write it to an io.Writer:

```
n, err := io.Copy(w, r)
```

The JSON decoder lets you decode directly from a Reader:

```
err := json.NewDecoder(r).Decode(v)
```

If you're reading bytes that have been gzipped, you can wrap the io.Reader in a gzip.Reader:

```
r = gzip.NewReader(r)
```

Now reading from the new reader will decompress as you read.

If you're designing a package or utility (even if it's an internal thing that nobody will ever see) rather than taking in strings or []byte slices, consider taking in an io.Reader if you can for data sources. Because suddenly, your code will work with every type that implements io.Reader.

So this:

```
func Reverse(s string) (string, error)
```

Could become:

```
func Reverse(r io.Reader) io.Reader
```

Then if someone wants to use it with a string, they can:

```
r = Reverse(strings.NewReader("Make me backwards"))
```

But they can also use it with a file:

```
f, err := os.Open("file.txt")
if err != nil {
 log.Fatalln(err)
}
r = Reverse(f)
```

Or a web request:

```
func handle(w http.ResponseWriter, r *http.Request) {

 rev := Reverse(r.Body)

 // etc...
}
```

Use io.Reader (and io.Writer) whenever you're dealing with streams of data. And this goes for all single method interfaces from the standard library.
