# The Swift Programming Language: The Basics

[Original URL](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID309)

> Swift is a new programming language for iOS and OS X app development. Nonetheless, many parts of Swift will be familiar from your experience of developing in C and Objective-C. Swift provides its own...

[]()[]() []()[]() Swift is a new programming language for iOS and OS X app development. Nonetheless, many parts of Swift will be familiar from your experience of developing in C and Objective-C.

Swift provides its own versions of all fundamental C and Objective-C types, including `Int` for integers, `Double` and `Float` for floating-point values, `Bool` for Boolean values, and `String` for textual data. Swift also provides powerful versions of the two primary collection types, `Array` and `Dictionary`, as described in 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html#//apple_ref/doc/uid/TP40014097-CH8-ID105">Collection Types</a>
</span>

.

Like C, Swift uses variables to store and refer to values by an identifying name. Swift also makes extensive use of variables whose values cannot be changed. These are known as constants, and are much more powerful than constants in C. Constants are used throughout Swift to make code safer and clearer in intent when you work with values that do not need to change.

In addition to familiar types, Swift introduces advanced types not found in Objective-C, such as tuples. Tuples enable you to create and pass around groupings of values. You can use a tuple to return multiple values from a function as a single compound value.

Swift also introduces optional types, which handle the absence of a value. Optionals say either "there _is_ a value, and it equals _x_" or "there _isn't_ a value at all". Optionals are similar to using `nil` with pointers in Objective-C, but they work for any type, not just classes. Optionals are safer and more expressive than `nil` pointers in Objective-C and are at the heart of many of Swift's most powerful features.

Optionals are an example of the fact that Swift is a _type safe_ language. Swift helps you to be clear about the types of values your code can work with. If part of your code expects a `String`, type safety prevents you from passing it an `Int` by mistake. This restriction enables you to catch and fix errors as early as possible in the development process.

[]()

## Constants and Variables

Constants and variables associate a name (such as `maximumNumberOfLoginAttempts` or `welcomeMessage`) with a value of a particular type (such as the number `10` or the string `"Hello"`). The value of a _constant_ cannot be changed once it is set, whereas a _variable_ can be set to a different value in the future.

[]()

## Declaring Constants and Variables

Constants and variables must be declared before they are used. You declare constants with the `let` keyword and variables with the `var` keyword. Here's an example of how constants and variables can be used to track the number of login attempts a user has made:

- `let maximumNumberOfLoginAttempts = 10`
- `var currentLoginAttempt = 0`

This code can be read as:

"Declare a new constant called `maximumNumberOfLoginAttempts`, and give it a value of `10`. Then, declare a new variable called `currentLoginAttempt`, and give it an initial value of `0`."

In this example, the maximum number of allowed login attempts is declared as a constant, because the maximum value never changes. The current login attempt counter is declared as a variable, because this value must be incremented after each failed login attempt.

You can declare multiple constants or multiple variables on a single line, separated by commas:

- `var x = 0.0, y = 0.0, z = 0.0`

[]() Note

If a stored value in your code is not going to change, always declare it as a constant with the `let` keyword. Use variables only for storing values that need to be able to change.

[]()

## Type Annotations

You can provide a _type annotation_ when you declare a constant or variable, to be clear about the kind of values the constant or variable can store. Write a type annotation by placing a colon after the constant or variable name, followed by a space, followed by the name of the type to use.

This example provides a type annotation for a variable called `welcomeMessage`, to indicate that the variable can store `String` values:

- `var welcomeMessage: String`

The colon in the declaration means _"...of type...,"_ so the code above can be read as:

"Declare a variable called `welcomeMessage` that is of type `String`."

The phrase "of type `String`" means "can store any `String` value." Think of it as meaning "the type of thing" (or "the kind of thing") that can be stored.

The `welcomeMessage` variable can now be set to any string value without error:

You can define multiple related variables of the same type on a single line, separated by commas, with a single type annotation after the final variable name:

- `var red, green, blue: Double`

[]() Note

It is rare that you need to write type annotations in practice. If you provide an initial value for a constant or variable at the point that it is defined, Swift can almost always infer the type to be used for that constant or variable, as described in 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID322">Type Safety and Type Inference</a>
</span>

. In the `welcomeMessage` example above, no initial value is provided, and so the type of the `welcomeMessage` variable is specified with a type annotation rather than being inferred from an initial value.

[]()

## Naming Constants and Variables

Constant and variable names can contain almost any character, including Unicode characters:

- `let π = 3.14159`
- `let 你好 = "你好世界"`
- `let 🐶🐮 = "dogcow"`

Constant and variable names cannot contain whitespace characters, mathematical symbols, arrows, private-use (or invalid) Unicode code points, or line- and box-drawing characters. Nor can they begin with a number, although numbers may be included elsewhere within the name.

Once you've declared a constant or variable of a certain type, you can't redeclare it again with the same name, or change it to store values of a different type. Nor can you change a constant into a variable or a variable into a constant.

[]() Note

If you need to give a constant or variable the same name as a reserved Swift keyword, surround the keyword with back ticks (`` ` `` ) when using it as a name. However, avoid using keywords as names unless you have absolutely no choice.

You can change the value of an existing variable to another value of a compatible type. In this example, the value of `friendlyWelcome` is changed from `"Hello!"` to `"Bonjour!"`:

- `var friendlyWelcome = "Hello!"`
- `friendlyWelcome = "Bonjour!"`
- `// friendlyWelcome is now "Bonjour!"`

Unlike a variable, the value of a constant cannot be changed once it is set. Attempting to do so is reported as an error when your code is compiled:

- `let languageName = "Swift"`
- `languageName = "Swift++"`
- `// this is a compile-time error - languageName cannot be changed`

[]()

## Printing Constants and Variables

You can print the current value of a constant or variable with the `println` function:

- `println(friendlyWelcome)`
- `// prints "Bonjour!"`

`println` is a global function that prints a value, followed by a line break, to an appropriate output. In Xcode, for example, `println` prints its output in Xcode's "console" pane. (A second function, `print`, performs the same task without appending a line break to the end of the value to be printed.)

The `println` function prints any `String` value you pass to it:

- `println("This is a string")`
- `// prints "This is a string"`

The `println` function can print more complex logging messages, in a similar manner to Cocoa's `NSLog` function. These messages can include the current values of constants and variables.

Swift uses _string interpolation_ to include the name of a constant or variable as a placeholder in a longer string, and to prompt Swift to replace it with the current value of that constant or variable. Wrap the name in parentheses and escape it with a backslash before the opening parenthesis:

- `println("The current value of friendlyWelcome is \(friendlyWelcome)")`
- `// prints "The current value of friendlyWelcome is Bonjour!"`

[]()

## Comments

Use comments to include non-executable text in your code, as a note or reminder to yourself. Comments are ignored by the Swift compiler when your code is compiled.

Comments in Swift are very similar to comments in C. Single-line comments begin with two forward-slashes (`//`):

Multiline comments start with a forward-slash followed by an asterisk (`/*`) and end with an asterisk followed by a forward-slash (`*/`):

- `/* this is also a comment,`
- `but written over multiple lines */`

Unlike multiline comments in C, multiline comments in Swift can be nested inside other multiline comments. You write nested comments by starting a multiline comment block and then starting a second multiline comment within the first block. The second block is then closed, followed by the first block:

- `/* this is the start of the first multiline comment`
- `/* this is the second, nested multiline comment */`
- `this is the end of the first multiline comment */`

Nested multiline comments enable you to comment out large blocks of code quickly and easily, even if the code already contains multiline comments.

[]()

## Semicolons

Unlike many other languages, Swift does not require you to write a semicolon (`;`) after each statement in your code, although you can do so if you wish. Semicolons _are_ required, however, if you want to write multiple separate statements on a single line:

- `let cat = "🐱"; println(cat)`
- `// prints "🐱"`

[]()

## Integers

_Integers_ are whole numbers with no fractional component, such as `42` and `-23`. Integers are either _signed_ (positive, zero, or negative) or _unsigned_ (positive or zero).

Swift provides signed and unsigned integers in 8, 16, 32, and 64 bit forms. These integers follow a naming convention similar to C, in that an 8-bit unsigned integer is of type `UInt8`, and a 32-bit signed integer is of type `Int32`. Like all types in Swift, these integer types have capitalized names.

[]()

## Integer Bounds

You can access the minimum and maximum values of each integer type with its `min` and `max` properties:

- `let minValue = UInt8.min // minValue is equal to 0, and is of type UInt8`
- `let maxValue = UInt8.max // maxValue is equal to 255, and is of type UInt8`

The values of these properties are of the appropriate-sized number type (such as `UInt8` in the example above) and can therefore be used in expressions alongside other values of the same type.

[]()

## Int

In most cases, you don't need to pick a specific size of integer to use in your code. Swift provides an additional integer type, `Int`, which has the same size as the current platform's native word size:

- On a 32-bit platform, `Int` is the same size as `Int32`.

- On a 64-bit platform, `Int` is the same size as `Int64`.

Unless you need to work with a specific size of integer, always use `Int` for integer values in your code. This aids code consistency and interoperability. Even on 32-bit platforms, `Int` can store any value between `-2,147,483,648` and `2,147,483,647`, and is large enough for many integer ranges.

[]()

## UInt

Swift also provides an unsigned integer type, `UInt`, which has the same size as the current platform's native word size:

- On a 32-bit platform, `UInt` is the same size as `UInt32`.

- On a 64-bit platform, `UInt` is the same size as `UInt64`.

[]() Note

Use `UInt` only when you specifically need an unsigned integer type with the same size as the platform's native word size. If this is not the case, `Int` is preferred, even when the values to be stored are known to be non-negative. A consistent use of `Int` for integer values aids code interoperability, avoids the need to convert between different number types, and matches integer type inference, as described in 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID322">Type Safety and Type Inference</a>
</span>

.

[]()

## Floating-Point Numbers

_Floating-point numbers_ are numbers with a fractional component, such as `3.14159`, `0.1`, and `-273.15`.

Floating-point types can represent a much wider range of values than integer types, and can store numbers that are much larger or smaller than can be stored in an `Int`. Swift provides two signed floating-point number types:

- `Double` represents a 64-bit floating-point number. Use it when floating-point values must be very large or particularly precise.

- `Float` represents a 32-bit floating-point number. Use it when floating-point values do not require 64-bit precision.

[]() Note

`Double` has a precision of at least 15 decimal digits, whereas the precision of `Float` can be as little as 6 decimal digits. The appropriate floating-point type to use depends on the nature and range of values you need to work with in your code.

[]()

## Type Safety and Type Inference

Swift is a _type safe_ language. A type safe language encourages you to be clear about the types of values your code can work with. If part of your code expects a `String`, you can't pass it an `Int` by mistake.

Because Swift is type safe, it performs _type checks_ when compiling your code and flags any mismatched types as errors. This enables you to catch and fix errors as early as possible in the development process.

Type-checking helps you avoid errors when you're working with different types of values. However, this doesn't mean that you have to specify the type of every constant and variable that you declare. If you don't specify the type of value you need, Swift uses _type inference_ to work out the appropriate type. Type inference enables a compiler to deduce the type of a particular expression automatically when it compiles your code, simply by examining the values you provide.

Because of type inference, Swift requires far fewer type declarations than languages such as C or Objective-C. Constants and variables are still explicitly typed, but much of the work of specifying their type is done for you.

Type inference is particularly useful when you declare a constant or variable with an initial value. This is often done by assigning a _literal value_ (or _literal_) to the constant or variable at the point that you declare it. (A literal value is a value that appears directly in your source code, such as `42` and `3.14159` in the examples below.)

For example, if you assign a literal value of `42` to a new constant without saying what type it is, Swift infers that you want the constant to be an `Int`, because you have initialized it with a number that looks like an integer:

- `let meaningOfLife = 42`
- `// meaningOfLife is inferred to be of type Int`

Likewise, if you don't specify a type for a floating-point literal, Swift infers that you want to create a `Double`:

- `let pi = 3.14159`
- `// pi is inferred to be of type Double`

Swift always chooses `Double` (rather than `Float`) when inferring the type of floating-point numbers.

If you combine integer and floating-point literals in an expression, a type of `Double` will be inferred from the context:

- `let anotherPi = 3 + 0.14159`
- `// anotherPi is also inferred to be of type Double`

The literal value of `3` has no explicit type in and of itself, and so an appropriate output type of `Double` is inferred from the presence of a floating-point literal as part of the addition.

[]()

## Numeric Literals

Integer literals can be written as:

- A _decimal_ number, with no prefix

- A _binary_ number, with a `0b` prefix

- An _octal_ number, with a `0o` prefix

- A _hexadecimal_ number, with a `0x` prefix

All of these integer literals have a decimal value of `17`:

- `let decimalInteger = 17`
- `let binaryInteger = 0b10001 // 17 in binary notation`
- `let octalInteger = 0o21 // 17 in octal notation`
- `let hexadecimalInteger = 0x11 // 17 in hexadecimal notation`

Floating-point literals can be decimal (with no prefix), or hexadecimal (with a `0x` prefix). They must always have a number (or hexadecimal number) on both sides of the decimal point. They can also have an optional _exponent_, indicated by an uppercase or lowercase `e` for decimal floats, or an uppercase or lowercase `p` for hexadecimal floats.

For decimal numbers with an exponent of `exp`, the base number is multiplied by 10

<sup>exp</sup>

:

- `1.25e2` means 1.25 × 10

  <sup>2</sup>

  , or `125.0`.

- `1.25e-2` means 1.25 × 10

  <sup>-2</sup>

  , or `0.0125`.

For hexadecimal numbers with an exponent of `exp`, the base number is multiplied by 2

<sup>exp</sup>

:

- `0xFp2` means 15 × 2

  <sup>2</sup>

  , or `60.0`.

- `0xFp-2` means 15 × 2

  <sup>-2</sup>

  , or `3.75`.

All of these floating-point literals have a decimal value of `12.1875`:

- `let decimalDouble = 12.1875`
- `let exponentDouble = 1.21875e1`
- `let hexadecimalDouble = 0xC.3p0`

Numeric literals can contain extra formatting to make them easier to read. Both integers and floats can be padded with extra zeroes and can contain underscores to help with readability. Neither type of formatting affects the underlying value of the literal:

- `let paddedDouble = 000123.456`
- `let oneMillion = 1_000_000`
- `let justOverOneMillion = 1_000_000.000_000_1`

[]()

## Numeric Type Conversion

Use the `Int` type for all general-purpose integer constants and variables in your code, even if they are known to be non-negative. Using the default integer type in everyday situations means that integer constants and variables are immediately interoperable in your code and will match the inferred type for integer literal values.

Use other integer types only when they are specifically needed for the task at hand, because of explicitly-sized data from an external source, or for performance, memory usage, or other necessary optimization. Using explicitly-sized types in these situations helps to catch any accidental value overflows and implicitly documents the nature of the data being used.

[]()

## Integer Conversion

The range of numbers that can be stored in an integer constant or variable is different for each numeric type. An `Int8` constant or variable can store numbers between `-128` and `127`, whereas a `UInt8` constant or variable can store numbers between `0` and `255`. A number that will not fit into a constant or variable of a sized integer type is reported as an error when your code is compiled:

- `let cannotBeNegative: UInt8 = -1`
- `// UInt8 cannot store negative numbers, and so this will report an error`
- `let tooBig: Int8 = Int8.max + 1`
- `// Int8 cannot store a number larger than its maximum value,`
- `// and so this will also report an error`

Because each numeric type can store a different range of values, you must opt in to numeric type conversion on a case-by-case basis. This opt-in approach prevents hidden conversion errors and helps make type conversion intentions explicit in your code.

To convert one specific number type to another, you initialize a new number of the desired type with the existing value. In the example below, the constant `twoThousand` is of type `UInt16`, whereas the constant `one` is of type `UInt8`. They cannot be added together directly, because they are not of the same type. Instead, this example calls `UInt16(one)` to create a new `UInt16` initialized with the value of `one`, and uses this value in place of the original:

- `let twoThousand: UInt16 = 2_000`
- `let one: UInt8 = 1`
- `let twoThousandAndOne = twoThousand + UInt16(one)`

Because both sides of the addition are now of type `UInt16`, the addition is allowed. The output constant (`twoThousandAndOne`) is inferred to be of type `UInt16`, because it is the sum of two `UInt16` values.

`SomeType(ofInitialValue)` is the default way to call the initializer of a Swift type and pass in an initial value. Behind the scenes, `UInt16` has an initializer that accepts a `UInt8` value, and so this initializer is used to make a new `UInt16` from an existing `UInt8`. You can't pass in _any_ type here, however--it has to be a type for which `UInt16` provides an initializer. Extending existing types to provide initializers that accept new types (including your own type definitions) is covered in 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html#//apple_ref/doc/uid/TP40014097-CH24-ID151">Extensions</a>
</span>

.

[]()

## Integer and Floating-Point Conversion

Conversions between integer and floating-point numeric types must be made explicit:

- `let three = 3`
- `let pointOneFourOneFiveNine = 0.14159`
- `let pi = Double(three) + pointOneFourOneFiveNine`
- `// pi equals 3.14159, and is inferred to be of type Double`

Here, the value of the constant `three` is used to create a new value of type `Double`, so that both sides of the addition are of the same type. Without this conversion in place, the addition would not be allowed.

Floating-point to integer conversion must also be made explicit. An integer type can be initialized with a `Double` or `Float` value:

- `let integerPi = Int(pi)`
- `// integerPi equals 3, and is inferred to be of type Int`

Floating-point values are always truncated when used to initialize a new integer value in this way. This means that `4.75` becomes `4`, and `-3.9` becomes `-3`.

[]() Note

The rules for combining numeric constants and variables are different from the rules for numeric literals. The literal value `3` can be added directly to the literal value `0.14159`, because number literals do not have an explicit type in and of themselves. Their type is inferred only at the point that they are evaluated by the compiler.

[]()

## Type Aliases

_Type aliases_ define an alternative name for an existing type. You define type aliases with the `typealias` keyword.

Type aliases are useful when you want to refer to an existing type by a name that is contextually more appropriate, such as when working with data of a specific size from an external source:

- `typealias AudioSample = UInt16`

Once you define a type alias, you can use the alias anywhere you might use the original name:

- `var maxAmplitudeFound = AudioSample.min`
- `// maxAmplitudeFound is now 0`

Here, `AudioSample` is defined as an alias for `UInt16`. Because it is an alias, the call to `AudioSample.min` actually calls `UInt16.min`, which provides an initial value of `0` for the `maxAmplitudeFound` variable.

[]()

## Booleans

Swift has a basic _Boolean_ type, called `Bool`. Boolean values are referred to as _logical_, because they can only ever be true or false. Swift provides two Boolean constant values, `true` and `false`:

- `let orangesAreOrange = true`
- `let turnipsAreDelicious = false`

The types of `orangesAreOrange` and `turnipsAreDelicious` have been inferred as `Bool` from the fact that they were initialized with Boolean literal values. As with `Int` and `Double` above, you don't need to declare constants or variables as `Bool` if you set them to `true` or `false` as soon as you create them. Type inference helps make Swift code more concise and readable when it initializes constants or variables with other values whose type is already known.

Boolean values are particularly useful when you work with conditional statements such as the `if` statement:

- `if turnipsAreDelicious {`
- `println("Mmm, tasty turnips!")`
- `} else {`
- `println("Eww, turnips are horrible.")`
- `}`
- `// prints "Eww, turnips are horrible."`

Conditional statements such as the `if` statement are covered in more detail in 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID120">Control Flow</a>
</span>

.

Swift's type safety prevents non-Boolean values from being substituted for `Bool`. The following example reports a compile-time error:

- `let i = 1`
- `if i {`
- `// this example will not compile, and will report an error`
- `}`

However, the alternative example below is valid:

- `let i = 1`
- `if i == 1 {`
- `// this example will compile successfully`
- `}`

The result of the `i == 1` comparison is of type `Bool`, and so this second example passes the type-check. Comparisons like `i == 1` are discussed in 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/BasicOperators.html#//apple_ref/doc/uid/TP40014097-CH6-ID60">Basic Operators</a>
</span>

.

As with other examples of type safety in Swift, this approach avoids accidental errors and ensures that the intention of a particular section of code is always clear.

[]()

## Tuples

_Tuples_ group multiple values into a single compound value. The values within a tuple can be of any type and do not have to be of the same type as each other.

In this example, `(404, "Not Found")` is a tuple that describes an _HTTP status code_. An HTTP status code is a special value returned by a web server whenever you request a web page. A status code of `404 Not Found` is returned if you request a webpage that doesn't exist.

- `let http404Error = (404, "Not Found")`
- `// http404Error is of type (Int, String), and equals (404, "Not Found")`

The `(404, "Not Found")` tuple groups together an `Int` and a `String` to give the HTTP status code two separate values: a number and a human-readable description. It can be described as "a tuple of type `(Int, String)`".

You can create tuples from any permutation of types, and they can contain as many different types as you like. There's nothing stopping you from having a tuple of type `(Int, Int, Int)`, or `(String, Bool)`, or indeed any other permutation you require.

You can _decompose_ a tuple's contents into separate constants or variables, which you then access as usual:

- `let (statusCode, statusMessage) = http404Error`
- `println("The status code is \(statusCode)")`
- `// prints "The status code is 404"`
- `println("The status message is \(statusMessage)")`
- `// prints "The status message is Not Found"`

If you only need some of the tuple's values, ignore parts of the tuple with an underscore (`_`) when you decompose the tuple:

- `let (justTheStatusCode, _) = http404Error`
- `println("The status code is \(justTheStatusCode)")`
- `// prints "The status code is 404"`

Alternatively, access the individual element values in a tuple using index numbers starting at zero:

- `println("The status code is \(http404Error.0)")`
- `// prints "The status code is 404"`
- `println("The status message is \(http404Error.1)")`
- `// prints "The status message is Not Found"`

You can name the individual elements in a tuple when the tuple is defined:

- `let http200Status = (statusCode: 200, description: "OK")`

If you name the elements in a tuple, you can use the element names to access the values of those elements:

- `println("The status code is \(http200Status.statusCode)")`
- `// prints "The status code is 200"`
- `println("The status message is \(http200Status.description)")`
- `// prints "The status message is OK"`

Tuples are particularly useful as the return values of functions. A function that tries to retrieve a web page might return the `(Int, String)` tuple type to describe the success or failure of the page retrieval. By returning a tuple with two distinct values, each of a different type, the function provides more useful information about its outcome than if it could only return a single value of a single type. For more information, see 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Functions.html#//apple_ref/doc/uid/TP40014097-CH10-ID164">Functions with Multiple Return Values</a>
</span>

.

[]() Note

Tuples are useful for temporary groups of related values. They are not suited to the creation of complex data structures. If your data structure is likely to persist beyond a temporary scope, model it as a class or structure, rather than as a tuple. For more information, see 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-ID82">Classes and Structures</a>
</span>

.

[]()

## Optionals

You use _optionals_ in situations where a value may be absent. An optional says:

- There _is_ a value, and it equals _x_

<!--  -->

- There _isn't_ a value at all

[]() Note

The concept of optionals doesn't exist in C or Objective-C. The nearest thing in Objective-C is the ability to return `nil` from a method that would otherwise return an object, with `nil` meaning "the absence of a valid object." However, this only works for objects--it doesn't work for structures, basic C types, or enumeration values. For these types, Objective-C methods typically return a special value (such as `NSNotFound`) to indicate the absence of a value. This approach assumes that the method's caller knows there is a special value to test against and remembers to check for it. Swift's optionals let you indicate the absence of a value for _any type at all_, without the need for special constants.

Here's an example of how optionals can be used to cope with the absence of a value. Swift's `String` type has a method called `toInt`, which tries to convert a `String` value into an `Int` value. However, not every string can be converted into an integer. The string `"123"` can be converted into the numeric value `123`, but the string `"hello, world"` does not have an obvious numeric value to convert to.

The example below uses the `toInt` method to try to convert a `String` into an `Int`:

- `let possibleNumber = "123"`
- `let convertedNumber = possibleNumber.toInt()`
- `// convertedNumber is inferred to be of type "Int?", or "optional Int"`

Because the `toInt` method might fail, it returns an _optional_ `Int`, rather than an `Int`. An optional `Int` is written as `Int?`, not `Int`. The question mark indicates that the value it contains is optional, meaning that it might contain _some_ `Int` value, or it might contain _no value at all_. (It can't contain anything else, such as a `Bool` value or a `String` value. It's either an `Int`, or it's nothing at all.)

[]()

## nil

You set an optional variable to a valueless state by assigning it the special value `nil`:

- `var serverResponseCode: Int? = 404`
- `// serverResponseCode contains an actual Int value of 404`
- `serverResponseCode = nil`
- `// serverResponseCode now contains no value`

[]() Note

`nil` cannot be used with nonoptional constants and variables. If a constant or variable in your code needs to work with the absence of a value under certain conditions, always declare it as an optional value of the appropriate type.

If you define an optional constant or variable without providing a default value, the constant or variable is automatically set to `nil` for you:

- `var surveyAnswer: String?`
- `// surveyAnswer is automatically set to nil`

[]() Note

Swift's `nil` is not the same as `nil` in Objective-C. In Objective-C, `nil` is a pointer to a nonexistent object. In Swift, `nil` is not a pointer--it is the absence of a value of a certain type. Optionals of _any_ type can be set to `nil`, not just object types.

[]()

## If Statements and Forced Unwrapping

You can use an `if` statement to find out whether an optional contains a value by comparing the optional against `nil`. You perform this comparison with the "equal to" operator (`==`) or the "not equal to" operator (`!=`).

If an optional has a value, it is considered to be "not equal to" `nil`:

- `if convertedNumber != nil {`
- `println("convertedNumber contains some integer value.")`
- `}`
- `// prints "convertedNumber contains some integer value."`

Once you're sure that the optional _does_ contain a value, you can access its underlying value by adding an exclamation mark (`!`) to the end of the optional's name. The exclamation mark effectively says, "I know that this optional definitely has a value; please use it." This is known as _forced unwrapping_ of the optional's value:

- `if convertedNumber != nil {`
- `println("convertedNumber has an integer value of \(convertedNumber!).")`
- `}`
- `// prints "convertedNumber has an integer value of 123."`

For more on the `if` statement, see 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID120">Control Flow</a>
</span>

.

[]()

## Optional Binding

You use _optional binding_ to find out whether an optional contains a value, and if so, to make that value available as a temporary constant or variable. Optional binding can be used with `if` and `while` statements to check for a value inside an optional, and to extract that value into a constant or variable, as part of a single action. `if` and `while` statements are described in more detail in 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ControlFlow.html#//apple_ref/doc/uid/TP40014097-CH9-ID120">Control Flow</a>
</span>

.

Write optional bindings for the `if` statement as follows:

- if let constantName = someOptional {

- statements

- }

You can rewrite the `possibleNumber` example from the 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID330">Optionals</a>
</span>

 section to use optional binding rather than forced unwrapping:

- `if let actualNumber = possibleNumber.toInt() {`
- `println("\'\(possibleNumber)\' has an integer value of \(actualNumber)")`
- `} else {`
- `println("\'\(possibleNumber)\' could not be converted to an integer")`
- `}`
- `// prints "'123' has an integer value of 123"`

This code can be read as:

"If the optional `Int` returned by `possibleNumber.toInt` contains a value, set a new constant called `actualNumber` to the value contained in the optional."

If the conversion is successful, the `actualNumber` constant becomes available for use within the first branch of the `if` statement. It has already been initialized with the value contained _within_ the optional, and so there is no need to use the `!` suffix to access its value. In this example, `actualNumber` is simply used to print the result of the conversion.

You can use both constants and variables with optional binding. If you wanted to manipulate the value of `actualNumber` within the first branch of the `if` statement, you could write `if var actualNumber` instead, and the value contained within the optional would be made available as a variable rather than a constant.

[]()

## Implicitly Unwrapped Optionals

As described above, optionals indicate that a constant or variable is allowed to have "no value". Optionals can be checked with an `if` statement to see if a value exists, and can be conditionally unwrapped with optional binding to access the optional's value if it does exist.

Sometimes it is clear from a program's structure that an optional will _always_ have a value, after that value is first set. In these cases, it is useful to remove the need to check and unwrap the optional's value every time it is accessed, because it can be safely assumed to have a value all of the time.

These kinds of optionals are defined as _implicitly unwrapped optionals_. You write an implicitly unwrapped optional by placing an exclamation mark (`String!`) rather than a question mark (`String?`) after the type that you want to make optional.

Implicitly unwrapped optionals are useful when an optional's value is confirmed to exist immediately after the optional is first defined and can definitely be assumed to exist at every point thereafter. The primary use of implicitly unwrapped optionals in Swift is during class initialization, as described in 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html#//apple_ref/doc/uid/TP40014097-CH20-ID55">Unowned References and Implicitly Unwrapped Optional Properties</a>
</span>

.

An implicitly unwrapped optional is a normal optional behind the scenes, but can also be used like a nonoptional value, without the need to unwrap the optional value each time it is accessed. The following example shows the difference in behavior between an optional string and an implicitly unwrapped optional string when accessing their wrapped value as an explicit `String`:

- `let possibleString: String? = "An optional string."`
- `let forcedString: String = possibleString! // requires an exclamation mark`
- ``
- `let assumedString: String! = "An implicitly unwrapped optional string."`
- `let implicitString: String = assumedString // no need for an exclamation mark`

You can think of an implicitly unwrapped optional as giving permission for the optional to be unwrapped automatically whenever it is used. Rather than placing an exclamation mark after the optional's name each time you use it, you place an exclamation mark after the optional's type when you declare it.

[]() Note

If you try to access an implicitly unwrapped optional when it does not contain a value, you will trigger a runtime error. The result is exactly the same as if you place an exclamation mark after a normal optional that does not contain a value.

You can still treat an implicitly unwrapped optional like a normal optional, to check if it contains a value:

- `if assumedString != nil {`
- `println(assumedString)`
- `}`
- `// prints "An implicitly unwrapped optional string."`

You can also use an implicitly unwrapped optional with optional binding, to check and unwrap its value in a single statement:

- `if let definiteString = assumedString {`
- `println(definiteString)`
- `}`
- `// prints "An implicitly unwrapped optional string."`

[]() Note

Do not use an implicitly unwrapped optional when there is a possibility of a variable becoming `nil` at a later point. Always use a normal optional type if you need to check for a `nil` value during the lifetime of a variable.

[]()

## Assertions

Optionals enable you to check for values that may or may not exist, and to write code that copes gracefully with the absence of a value. In some cases, however, it is simply not possible for your code to continue execution if a value does not exist, or if a provided value does not satisfy certain conditions. In these situations, you can trigger an _assertion_ in your code to end code execution and to provide an opportunity to debug the cause of the absent or invalid value.

[]()

## Debugging with Assertions

An assertion is a runtime check that a logical condition definitely evaluates to `true`. Literally put, an assertion "asserts" that a condition is true. You use an assertion to make sure that an essential condition is satisfied before executing any further code. If the condition evaluates to `true`, code execution continues as usual; if the condition evaluates to `false`, code execution ends, and your app is terminated.

If your code triggers an assertion while running in a debug environment, such as when you build and run an app in Xcode, you can see exactly where the invalid state occurred and query the state of your app at the time that the assertion was triggered. An assertion also lets you provide a suitable debug message as to the nature of the assert.

You write an assertion by calling the global `assert` function. You pass the `assert` function an expression that evaluates to `true` or `false` and a message that should be displayed if the result of the condition is `false`:

- `let age = -3`
- `assert(age >= 0, "A person's age cannot be less than zero")`
- `// this causes the assertion to trigger, because age is not >= 0`

In this example, code execution will continue only if `age >= 0` evaluates to `true`, that is, if the value of `age` is non-negative. If the value of `age` _is_ negative, as in the code above, then `age >= 0` evaluates to `false`, and the assertion is triggered, terminating the application.

The assertion message can be omitted if desired, as in the following example:

[]()

## When to Use Assertions

Use an assertion whenever a condition has the potential to be false, but must _definitely_ be true in order for your code to continue execution. Suitable scenarios for an assertion check include:

- An integer subscript index is passed to a custom subscript implementation, but the subscript index value could be too low or too high.

- A value is passed to a function, but an invalid value means that the function cannot fulfill its task.

- An optional value is currently `nil`, but a non-`nil` value is essential for subsequent code to execute successfully.

See also 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Subscripts.html#//apple_ref/doc/uid/TP40014097-CH16-ID305">Subscripts</a>
</span>

 and 

<span class="x-name">
  <a href="https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Functions.html#//apple_ref/doc/uid/TP40014097-CH10-ID158">Functions</a>
</span>

.

[]() Note

Assertions cause your app to terminate and are not a substitute for designing your code in such a way that invalid conditions are unlikely to arise. Nonetheless, in situations where invalid conditions are possible, an assertion is an effective way to ensure that such conditions are highlighted and noticed during development, before your app is published.

[A Swift Tour](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/GuidedTour.html#//apple_ref/doc/uid/TP40014097-CH2-ID1)

[Basic Operators](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/BasicOperators.html#//apple_ref/doc/uid/TP40014097-CH6-ID60)

Copyright © 2015 Apple Inc. All rights reserved. [Terms of Use](http://www.apple.com/legal/terms/site.html) | [Privacy Policy](http://www.apple.com/privacy/) | Updated: 2014-10-16
