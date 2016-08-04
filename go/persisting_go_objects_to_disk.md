
[Original URL](https://medium.com/@matryer/golang-advent-calendar-day-eleven-persisting-go-objects-to-disk-7caf1ee3d11d)

> Today we're going to write a simple package that lets us save Go objects to disk, and read them back.We'll expose two main functions:func Save(path string, v interface{}) errorfunc Load(path string,...

Today we're going to write a simple package that lets us save Go objects to disk, and read them back.

We'll expose two main functions:

```
func Save(path string, v interface{}) error

func Load(path string, v interface{}) error
```

We'll specify a path and a target object in both cases, and have our package Save and Load that object to the location on disk.

Since we don't want our users to have to worry about how they use our package (they might well want to be loading and saving things in many goroutines at the same time), we are going to make our code safe.

We're going to use a mutual exclusion lock provided to us via the sync.Mutex type from the standard library.

Inside both of our functions, we'll lock (and then unlock) this mutex to make sure we don't try and read and write from files at the same time.

Create a file called persist.go, and add the mutex:

```
package persist

import "sync"

var lock sync.Mutex
```

Let's get started by writing our Save function:

```
// Save saves a representation of v to the file at path.
func Save(path string, v interface{}) error {
 lock.Lock()
 defer lock.Unlock()
}
```

We are locking our mutex first, and deferring the unlocking for when the function exits.

Next, we're going to create a file using os.Create, and defer the closing of that.

```
// Save saves a representation of v to the file at path.
func Save(path string, v interface{}) error {
 lock.Lock()
 defer lock.Unlock()
 f, err := os.Create(path)
 if err != nil {
 return err
 }
 defer f.Close()
}
```

If an error occurs, we'll return it. Since we've already deferred the unlocking of the mutex, we can be sure that we don't keep things locked up.

Marshalling data refers to encoding it into bytes. Bytes can then be written to the file and unmarshalled later. For now, we're going to use JSON -- because it's perfect for this sort of thing, but we don't want our package to insist on that.

Add the following code to the top of persist.go:

```
// Marshal is a function that marshals the object into an
// io.Reader.
// By default, it uses the JSON marshaller.
var Marshal = func(v interface{}) (io.Reader, error) {
 b, err := json.MarshalIndent(v, "", "\t")
 if err != nil {
 return nil, err
 }
 return bytes.NewReader(b), nil
}
```

This is an interesting way of defining functions in Go. Marshal is actually a variable, which can be modified by outside code and which will (provided the function is of the same type) allow people to replace the marshaller with their own.

This one uses json.MarshalIndent which will turn the v object into nicely formatted JSON for us. The 'interface' for the function takes an object, and returns an io.Reader -- from which the bytes will be read.

Let's update the Save function to use our new Marshal method:

```
// Save saves a representation of v to the file at path.
func Save(path string, v interface{}) error {
 lock.Lock()
 defer lock.Unlock()
 f, err := os.Create(path)
 if err != nil {
 return err
 }
 defer f.Close()
 r, err := Marshal(v)
 if err != nil {
 return err
 }
 _, err = io.Copy(f, r)
 return err
}
```

Here we call Marshal, passing in the object and getting back an io.Reader -- which we then write into the file using io.Copy.

> The first argument is the number of bytes that were copied, which we're not interested in -- so we use the underscore to dismiss the argument.

io.Copy is a helper function that copies everything from an io.Reader into an io.Writer.

As you've already figured out -- unmarshalling is the process of turning bytes back into an object. We'll use the same trick:

```
// Unmarshal is a function that unmarshals the data from the
// reader into the specified value.
// By default, it uses the JSON unmarshaller.
var Unmarshal = func(r io.Reader, v interface{}) error {
 return json.NewDecoder(r).Decode(v)
}
```

Unmarshal takes an io.Reader (where the bytes will come from), and a target object, and just passes those to the json package of the standard library.

Now we can save data -- let's implement the Load function:

```
// Load loads the file at path into v.
// Use os.IsNotExist() to see if the returned error is due
// to the file being missing.
func Load(path string, v interface{}) error {
 lock.Lock()
 defer lock.Unlock()
 f, err := os.Open(path)
 if err != nil {
 return err
 }
 defer f.Close()
 return Unmarshal(f, v)
}
```

Firstly we lock our mutex (just in case people try to Load data that's also being Saved at the same time), and defer the unlocking in the same way we did for our Save function.

We use os.Open to open the path for reading, before passing the file to Unmarshal. The closing of the file is deferred, so it will get tidied up once Unmarshal has finished with it.

```
type obj struct {
 Name string
 Number int
 When time.Time
}

func main() {

 o := &obj{
 Name: "Mat",
 Number: 47,
 When: time.Now(),
 }

 if err := Save("./file.tmp", o); err != nil {
 log.Fatalln(err)
 }

 // load it back
 var o2 obj
 if err := Load("./file.tmp", o2); err != nil {
 log.Fatalln(err)
 }

 // o and o2 are now the same
 // and check out file.tmp - you'll see the JSON file

}
```

I'm one of those teachers at school that gives you homework even though it's the holidays.

See if you can think up a new pair of Marshal/Unmarshal functions that'll write the data in some different format.
