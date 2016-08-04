
[Original URL](https://medium.com/@kyawmyintthein/go-programming-for-beginner-map-function-part-3-c374eea78bfe)

> Map is a collections of key/value pair and also known as an associative array, hash, table and dictionary. Map can look up the value with associated key. Go also support map as built-in type.

![](https://cdn-images-1.medium.com/max/600/1*-IJJI9lCV8ykMHSS_JjTDw.png)

**Map** is a collections of key/value pair and also known as an associative array, hash, table and dictionary. **Map** can look up the **value** with associated **key**. **Go** also support map as built-in type.

Define a **map** in Go Programming:

```
var x map[string]int
```

The map type is represented as **map** keyword, followed by the **key type in brackets** and the **value type**.

In "x" map, "[string]" is key type and "int" is value type.

You can set **value** to "x" **map** by:

```
x["key1"] = 10
```

So, you can also retrieve the **value from map** by using **key**.

```
fmt.Println(x["key1"])
10
```

You can use **make** built-in function to create a **map**.

```
var x = make(map[string]int)
```

You can delete the **map value** by **key**:

```
delete(x, "key1")
```

If you retrieve the **map value** with **invalid key**, that will return zero value for the value type (which for strings is the empty string). You can check the that condition with:

```
if x["key2"] == ""{
 //do something
}
```

Go provide better way to check as follow:

```
value, ok := x["key2"] 
```

You can also define map by shorter way:

```
x := map[string]int{
 "key1": 1",
 "key2": 2",
}
```

A **function** is a type of procedure or routine which returns a value, and a procedure, which performs some operation but does not return a value.

You already use function in **Go programming** which is,

```
func main() {}
```

main() function is an entry point of the **Go program**.

You can also create your own function with **func** keyword, followed by the function's name. for example:

```
func sum() {}
```

In a function, you can accept parameters as follow:

```
func sum(a int,b int) {}
```

sum function accept 2 parameters with "int" type. You can also use shorter way if your parameter data type is same.

```
func sum(a,b int) {}
```

You can also return value or function from a function. But, you need to define return type.

```
func sum(a,b int) int{}
```

In Go programming, you can return multiple value from a function

```
func sum(a,b int) (int,error){}
```

"sum" function will return "int" and error type.

Finally You can have the function body which is a series of statements between curly braces.

```
import "strconv"
func sum(a,b int) (int,error){

 var err error 
 var total int
 if _, err = strconv.Atoi(a); err != nil {
 return total, err
 }

 if _, err = strconv.Atoi(b); err != nil {
 return total, err
 }

 total = a + b
 return total, err

}
```

In "sum" function, we check the parameters value are type by using "strconv" built-in package and sum the two parameter and return the total and error value. There is not error, error type will return nil.

You can call "sum" function as follow:

```
t, err := sum(1,2)
```

Functions are built up in a "stack". Suppose we had this program:

```
func main() {
 fmt.Println(f1())
}
func f1() int {
 return f2()
}
func f2() int {
 return 1
}
```

![](https://cdn-images-1.medium.com/max/800/1*-BL4CZ-AspNCeWT1UqOebQ.png)

credit: <http://www.golang-book.com/book/intro/7> **Variadic Functions**

You can accept zero or more parameter by using ... value in function.

```
func f2(args ...int){
 fmt.Println(args)
}
```

**Closure**

It is possible to create functions inside of functions:

```
func intSeq() func() int {
 i := 0
 return func() int {
 i += 1
 return i
 }
}
newInts := intSeq()
fmt.Println(newInts())
fmt.Println(newInts())

Result:
1
2
```

intSeq function is closure function, which returns another function, which we define anonymously in the body of intSeq. The returned function _closes over_ the variable i to form a closure.

**Recursion**

A function able to call itself is recursion

```
func factorial(x uint) uint {
 if x == 0 {
 return 1
 }
 return x articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js MITIE netdata png pngquant start-thesrc thesrc ucii factorial(x-1)
}
```

**Defer** is special statement in Go programming, which schedules a function call to be run after the function completes. For example, in file read/write program, you need to close the file after read/write. At that time, you can use defer statement.

```
f, _ := os.Open(filename)
defer f.Close()
```

**Panic** is a function to cause a run time error. For example,

```
f, err:= os.Open(filename)

if err != nil{
 panic(err) // if error occur, program will stop here
}
defer f.Close()
```

**Recover** We can handle a run-time panic with the built-in recover function. recover stops the panic and returns the value that was passed to the call to **panic**.

```
f, err:= os.Open(filename)

if err != nil{
 panic(err) // if error occur, program will stop here recoverErr:= recover()
 fmt.Println(recoverErr)
}
defer f.Close()
```
