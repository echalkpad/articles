# Swift.org

[Original URL](https://swift.org/getting-started/#using-the-lldb-debugger)

> Here, you'll find information about the how to use the Swift programming language. If you're new to Swift, check out A Swift Tour in The Swift Programming Language, for a quick...

Here, you'll find information about the how to use the Swift programming language.

If you're new to Swift, check out [A Swift Tour](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/GuidedTour.html#//apple_ref/doc/uid/TP40014097-CH2-ID1) in _The Swift Programming Language_, for a quick introduction to the most important concepts and features of the language.

## Installing Swift

The first step to using Swift is to download and install the compiler and other required components. Go to the [Download](https://swift.org/download) page and follow the instructions for your target platform.

In order to follow along with the examples below, make sure to add Swift to your `$PATH`.

### On OS X

The default location for the downloadable toolchain on OS X is `/Library/Developer/Toolchains`. You can make the tools available for use from the terminal with the following command:

```
$ export PATH=/Library/Developer/Toolchains/swift-latest.xctoolchain/usr/bin:"${PATH}"
```

### On Linux

First, install clang:

```
$ sudo apt-get install clang
```

If you installed the Swift toolchain on Linux to a directory other than the system root, you will need to run the following command, using the actual path of your Swift installation:

```
$ export PATH=/path/to/Swift/usr/bin:"${PATH}"
```

You can verify that you are running the expected version of Swift by entering the `swift` command and passing the `--version` flag:

```
$ swift --version
Apple Swift version 2.2-dev (LLVM ..., Clang ..., Swift ...)
```

The `-dev` suffix on the version number is used to indicate that it's a _development_ build, not a released version.

## Using the REPL

If you run the `swift` command without any other arguments, you'll launch the REPL, an interactive shell that will read, evaluate, and print the results of any Swift code you enter.

```
$ swift
Welcome to Apple Swift version 2.2\. Type :help for assistance.
 1>
```

Interacting with the REPL is a great way to experiment with Swift. For example, if you enter the expression `1 + 2`, the result of the expression, `3`, is printed on the next line:

You can assign values to constants and variables, and use them in subsequent lines. For instance, the `String` value `Hello, world!` can be assigned to the constant `greeting`, and then passed as an argument to the `print(_:)` function:

```
 2> let greeting = "Hello!"
greeting: String = "Hello!"
 3> print(greeting)
Hello!
```

If you enter an invalid expression, the REPL will print an error showing where the problem occurred:

```
let answer = "forty"-"two"
error: binary operator '-' cannot be applied to two 'String' operands
let answer = "forty"-"two"
 ~~~~~~~^~~~~~
```

You can use the up-arrow and down-arrow keys (`↑` and `↓`) to cycle through previous lines entered into the REPL. This allows you to make a slight change to a previous expression without retyping the entire line, and is especially convenient for fixing errors like the one in the previous example:

```
let answer = "forty-two"
answer: String = "forty-two"
```

Another useful feature of the REPL is that it can automatically suggest functions and methods that can be used in a particular context. For example, if you enter `re` after a dot operator on a `String` value and then hit the tab key (`⇥`), the REPL will give a list of available completions like `removeAtIndex(_:)` and `replaceRange(_:with:)`:

```
5> "Hi!".re⇥
Available completions:
    removeAll() -> Void
    removeAll(keepCapacity: Bool) -> Void
    removeAtIndex(i: Index) -> Character
    removeRange(subRange: Range<Index>) -> Void
    replaceRange(subRange: Range<Index>, with: C) -> Void
    replaceRange(subRange: Range<Index>, with: String) -> Void
    reserveCapacity(n: Int) -> Void
```

If you start a block of code, such as when iterating over an array with a `for-in` loop, the REPL will automatically indent the next line, and change the prompt character from `>` to `.` to indicate that code entered on that line will only be evaluated when the entire code block is evaluated.

```
 6> let numbers = [1,2,3]
numbers: [Int] = 3 values {
 [0] = 1
 [1] = 2
 [2] = 3
}
 7> for n in numbers.reverse() {
 8\. print(n)
 9\. }
3
2
1
```

All of the functionality of Swift is available to you from the REPL, from writing control flow statements to declaring and instantiating structures and classes.

You can also import any available system modules, such as `Darwin` on OS X and `Glibc` on Linux:

### On OS X

```
1> import Darwin
2> arc4random_uniform(10)
$R0: UInt32 = 4
```

### On Linux

```
1> import Glibc
2> random() % 10
$R0: Int32 = 4
```

## Using the Build System

Swift's build system provides a convention-based system for building libraries and executables, and sharing code across different projects.

These examples assume you have made `swift` available in your path; see [Installing](https://swift.org/getting-started/#installing-swift) for more information. Once available, you can invoke the build system using `swift build`:

```
$ swift build --help
OVERVIEW: Build sources into binary products
...
```

### Creating a Package

To create a new Swift package, first create and navigate to a new directory named `Hello`:

Every package must have a manifest file called `Package.swift` in its root directory. If the manifest file is blank, the package manager will build the package using conventional defaults. Create an empty manifest using:

When using the defaults, the package manager expects all source code to be contained in a `Sources/` subdirectory. Create that with:

### Building an Executable

By default, a directory that contains a file called `main.swift` will compile that file into a binary executable with the name of the package.

In this example, the package will produce an executable named `Hello` that outputs "Hello, world!".

Create a file called `main.swift` in the `Sources/` directory and using your editor of choice, enter the following code:

Compile the package by running the `swift build` command:

After the command finishes, the built products will be available in the `.build` directory. Run the `Hello` program with the following command:

```
$ .build/debug/Hello
Hello, world!
```

As a next step, let's define a new `sayHello(_:)` function in a new source file, and have the executable call that instead of calling `print(_:)` directly.

### Working with Multiple Source Files

Create a new file in the `Sources/` directory called `Greeter.swift`, and enter the following code:

```
func sayHello(name: String) {
 print("Hello, \(name)!")
}
```

The `sayHello(_:)` function takes a single `String` argument and prints our "Hello" greeting before, substituting the word "World" with the function argument.

Now, open `main.swift` again, and replace the existing contents with the following code:

```
if Process.arguments.count != 2 {
 print("Usage: hello NAME")
} else {
 let name = Process.arguments[1]
 sayHello(name)
}
```

Rather than using a hardcoded name as before, `main.swift` now reads from the command line arguments. And instead of invoking `print(_:)` directly, `main.swift` now calls the `sayHello(_:)` method. Because the method is part of the `Hello` module, no `import` statement is necessary.

Run `swift build` and try out the new version of `Hello`:

```
$ swift build
$ .build/debug/Hello `whoami`
```

> To learn about the Swift Package Manager, including how to build modules, import dependencies, and map system libraries, see the [Swift Package Manager](https://swift.org/package-manager) section of the website.

## Using the LLDB Debugger

You can use the LLDB debugger to run Swift programs step-by-step, set breakpoints, and inspect and modify program state.

As an example, consider the following Swift code, which defines a `factorial(_:)` function, and prints the result of calling that function:

```
func factorial(n: Int) -> Int {
 if n <= 1 { return n }
 return n * factorial(n - 1)
}

let number = 4
print("\(number)! is equal to \(factorial(number))")
```

Create a file named `Factorial.swift` with the code above, and run the `swiftc` command, passing the filename as a command line argument, along with the `-g` option to generate debug information. This will create an executable named `Factorial` in the current directory.

```
$ swiftc -g Factorial.swift
$ ls
Factorial.dSYM
Factorial.swift
Factorial*
```

Instead of running the `Factorial` program directly, run it through the LLDB debugger by passing it as a command line argument to the `lldb` command.

```
$ lldb Factorial
(lldb) target create "Factorial"
Current executable set to 'Factorial' (x86_64).
```

This will start an interactive console that allows you to run LLDB commands.

> For more information about LLDB commands, see the [LLDB Tutorial](http://lldb.llvm.org/tutorial.html).

Set a breakpoint on line 2 of the `factorial(_:)` function with the `breakpoint set` (`b`) command, to have the process break each time the function is executed.

```
(lldb) b 2
Breakpoint 1: where = Factorial`Factorial.factorial (Swift.Int) -> Swift.Int + 12 at Factorial.swift:2, address = 0x0000000100000e7c
```

Run the process with the `run` (`r`) command. The process will stop at the call site of the `factorial(_:)` function.

```
(lldb) r
Process 40246 resuming
Process 40246 stopped
* thread #1: tid = 0x14dfdf, 0x0000000100000e7c Factorial`Factorial.factorial (n=4) -> Swift.Int + 12 at Factorial.swift:2, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
 frame #0: 0x0000000100000e7c Factorial`Factorial.factorial (n=4) -> Swift.Int + 12 at Factorial.swift:2
 1 func factorial(n: Int) -> Int {
-> 2 if n <= 1 { return n }
 3 return n articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii factorial(n - 1)
 4 }
 5
 6 let number = 4
 7 print("\(number)! is equal to \(factorial(number))")
```

Use the `print` (`p`) command to inspect the value of the `n` parameter.

The `print` command can evaluate Swift expressions as well.

```
(lldb) p n articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii n
(Int) $R1 = 16
```

Use the `backtrace` (`bt`) command to show the frames leading to `factorial(_:)` being called.

```
(lldb) bt
* thread #1: tid = 0x14e393, 0x0000000100000e7c Factorial`Factorial.factorial (n=4) -> Swift.Int + 12 at Factorial.swift:2, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii frame #0: 0x0000000100000e7c Factorial`Factorial.factorial (n=4) -> Swift.Int + 12 at Factorial.swift:2
 frame #1: 0x0000000100000daf Factorial`main + 287 at Factorial.swift:7
 frame #2: 0x00007fff890be5ad libdyld.dylib`start + 1
 frame #3: 0x00007fff890be5ad libdyld.dylib`start + 1
```

Use the `continue` (`c`) command to resume the process until the breakpoint is hit again.

```
(lldb) c
Process 40246 resuming
Process 40246 stopped
* thread #1: tid = 0x14e393, 0x0000000100000e7c Factorial`Factorial.factorial (n=3) -> Swift.Int + 12 at Factorial.swift:2, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
 frame #0: 0x0000000100000e7c Factorial`Factorial.factorial (n=3) -> Swift.Int + 12 at Factorial.swift:2
 1 func factorial(n: Int) -> Int {
-> 2 if n <= 1 { return n }
 3 return n articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii factorial(n - 1)
 4 }
 5
 6 let number = 4
 7 print("\(number)! is equal to \(factorial(number))")
```

Use the `print` (`p`) command again to inspect the value of the `n` parameter for the second call to `factorial(_:)`.

Use the `breakpoint disable` (`br di`) command to disable all breakpoints and the `continue` (`c`) command to have the process run until it exits.

```
(lldb) br di
All breakpoints disabled. (1 breakpoints)
(lldb) c
Process 40246 resuming
4! is equal to 24
Process 40246 exited with status = 0 (0x00000000)
```

Now that you've been introduced to the Swift REPL, build system, and debugger, here are a few suggestions for what to do next:
