# The Fundamentals of Bash Scripting

[Original URL](http://code.tutsplus.com/tutorials/the-fundamentals-of-bash-scripting--net-32093)

> Shell scripts are widely used in the UNIX world. They're excellent for speeding up repetitive tasks and simplifying complex execution logic. They can be as simple as a set of commands, or they can...

Shell scripts are widely used in the UNIX world. They're excellent for speeding up repetitive tasks and simplifying complex execution logic. They can be as simple as a set of commands, or they can orchestrate complex tasks. In this tutorial, we'll learn more about the Bash scripting language by writing an example script step-by-step.

## The Fizz-Buzz Problem

One of the best way to learn about a new language is by example. Let's start with one.

The Fizz-Buzz problem is a very simple one. It became famous after a programmer, named Imran, used it as an interview test. It turns out that 90-99.5% of the candidates for a programming job are simply unable to write the simplest program. Imran took this simple Fizz-Buzz game and asked the candidates to solve it. Many followed Imran's example, and, today, it is one of the most asked frequently asked questions for a programming job. If you're hiring, and need a way to filter through 90% of the candidates, this is a great problem to present.

Here are the rules:

- Take and print the numbers between 1 and 100.
- When a number is divisible by 3, print "Fizz" instead of the number.
- When it is divisible by 5, print "Buzz" instead.
- When it is divisible both by 3 and 5, print "FizzBuzz".

That's all there is to it. I'm sure most of you can already visualize the two or three `if` statements to solve this. Let's work through this using the Bash scripting language.

## Shebang

A shebang refers to the combination of the hash and exclamation mark characters: `#!`. The program loader will look for a shebang on the first line of the script, and use the interpreter specified in it. A shebang consists of the following syntax: `#!interpreter [parameters]`. The interpreter is the program that is used to interpret our language. For bash scripting, that would be `/bin/bash`. For example, if you want to create a script in PHP and run it in console, you'd probably want to use `/usr/bin/php` (or the path to the PHP executable on your machine) as the interpreter.

```
#!/usr/bin/php
<?php
phpinfo();
```

Yes, that will actually work! Isn't it simple? Just be sure to make your file executable first. Once you do, this script will output your PHP information as you would expect.

**Tip:** To ensure that your script will work on as many systems as possible, you can use `/bin/env` in the shebang. As such, instead of `/bin/bash`, you could use `/bin/env bash`, which will work on systems where the bash executable is not within `/bin`.

## Outputting Text

The output of a script will be equal to, as you might expect, whatever is outputted from your command. However, if we explicitly want to write something to the screen, we can use `echo`.

```
#!/bin/bash

echo "Hello World"
```

Running this script will print "Hello World" in the console.

```
csaba@csaba ~ $ ./helloWorld.sh
Hello World
csaba@csaba ~ $
```

## Introducing Variables

As with any programming language, when writing shell scripts, you can use variables.

```
#!/bin/bash

message="Hello World"
echo $message
```

This code produces exactly the same "Hello World" message. As you can see, to assign a value to a variable, simply write its name - exclude the dollar sign in front of it. Also, be careful with spaces; there can't be any spaces between the variable name and the equal sign. So `message="Hello"` instead of `message = 'Hello'`

When you wish to use a variable, you can take the value from it just as we did in the `echo` command. Prepending a `$` to the variable's name will return its value.

**Tip:** Semicolons aren't required in bash scripting. You can use them in most cases, but be careful: they may have a different meaning than what you expect.

## Printing the Numbers Between 1 and 100

Continuing on with our demo project, we need to cycle through all numbers between 1 and 100\. For this, we'll need to use a `for` loop.

```
#!/bin/bash

for number in {1..100}; do
 echo $number
done
```

There are several new things worth noting in this example - which by the way, prints all the numbers from 1 to 100, one number at a time.

- The `for` syntax in Bash is: `for VARIABLE in RANGE; do COMMAND done`.
- The curly braces will transform `1..100` into a range in our example. They're used in other contexts as well, which we'll review shortly.
- `do` and `for` are actually two separate commands. If you want to place two commands on a single line, you'll need to separate them somehow. One way is to use semicolon. Alternatively you could write the code without a semicolon by moving `do` to the following line.

<!--  -->

```
#!/bin/bash

for number in {1..100}
do
 echo $number
done
```

## The First Decision

Now that we know how to print all the numbers between 1 and 100, it's time to make our first decision.

```
#!/bin/bash

for number in {1..100}; do
 if [ $((number%3)) -eq 0 ]; then
 echo "Fizz"
 else
 echo $number
 fi
done
```

This example will output "Fizz" for numbers divisible by 3\. Again, we have to deal with a bit of new syntax. Let's take them one by one.

- `if..then..else..fi` - this is the classic syntax for an `if` statement in Bash. Of course, the `else` part is optional - but required for our logic in this case.
- `if COMMAND-RETURN-VALUE; then...` - `if` will execute if the return value of the command is zero. Yes, logic in Bash is zero based, meaning that commands that execute successfully exit with a code of 0\. If something goes wrong, on the other hand, a positive integer will be returned. To simplify things: anything other than 0 is considered `false`.
- Mathematical expressions in Bash are specified by double parentheses. `$((number%3))` will return the remaining value of dividing the variable, `number`, by 3\. Please note that we did not use `$` inside the parenthesis - only in front of them.

You might be wondering where the command is in our example. Isn't there just a bracket with an odd expression in it? Well, it turns out that `[` is actually an executable command. To play around with this, try out the following commands in your console.

```
csaba@csaba ~ $ which [
/usr/bin/[
csaba@csaba ~ $ [ 0 -eq 1 ]
csaba@csaba ~ $ echo $?
1
csaba@csaba ~ $ [ 0 -eq 0 ]
csaba@csaba ~ $ echo $?
0
```

> **Tip:** A command's exit value is always returned into the variable, `?` (question mark). It is overwritten after each new command's execution.

## Checking for Buzz

We're doing well so far. We have "Fizz"; now let's do the "Buzz" part.

```
#!/bin/bash

for number in {1..100}; do
 if [ $((number%3)) -eq 0 ]; then
 echo "Fizz"
 elif [ $((number%5)) -eq 0 ]; then
 echo "Buzz"
 else
 echo $number
 fi
done
```

Above, we've introduced another condition for divisibility by 5: the `elif` statement. This, of course, translates to _else if_, and will be executed if the command following it returns `true` (or `0`). As you can observe, the conditional statements within `[]` are usually evaluated with the help of parameters, such as `-eq`, which stands for "equals."

> For the syntax, `arg1 OP arg2`, `OP` is one of `-eq`, `-ne`, `-lt`, `-le`, `-gt`, or `-ge`. These arithmetic binary operators return `true` if `arg1` is equal to, not equal to, less than, less than or equal to, greater than, or greater than or equal to `arg2`, respectively.`arg1` and `arg2` may be positive or negative integers. - _Bash Manual_

When you're attempting to compare strings, you may use the well-known `==` sign, or even a single equal sign will do. `!=` returns `true` when the strings are different.

## But the Code isn't Quite Correct

So far, the code runs, but the logic is not correct. When the number is divisible by both 3 and 5, our logic will echo only "Fizz." Let's modify our Code to satisfy the last requirement of FizzBuzz.

```
#!/bin/bash

for number in {1..100}; do
 output=""
 if [ $((number%3)) -eq 0 ]; then
 output="Fizz"
 fi
 if [ $((number%5)) -eq 0 ]; then
 output="${output}Buzz"
 fi
 if [ -z $output ]; then
 echo $number
 else
 echo $output;
 fi
done
```

Again, we've had to make a handful of changes. The most notable one is the introduction of a variable, and then the concatenation of "Buzz" to it, if necessary. Strings in bash are typically defined between double quotes ("). Single quotes are usable as well, but for easier concatenation, doubles are the better choice. Within these double quotes, you can reference variables: `some text $variable some other text`" will replace `$variable` with its contents. When you want to concatenate variables with strings without spaces between them, you may prefer to put the variable's name within curly braces. In most cases, like PHP, you're not required to do so, but it helps a lot when it comes to the code's readability.

**Tip:** You can't compare empty strings. That would return a missing parameter.

Because arguments inside `[ ]` are treated as parameters, for `"["`, they must be different from an empty string. So this expression, even though logical, will output an error: `[ $output != "" ]`. That's why we've used `[ -z $output ]`, which returns `true` if the string has a length of zero.

## Extract Method for Logical Expression

One way to improve our example is to extract into functions the mathematical expression from the `if` statements, like so:

```
#!/bin/bash

function isDivisibleBy {
 return $(($1%$2))
}

for number in {1..100}; do
 output=""
 if isDivisibleBy $number 3; then
 output="Fizz"
 fi
 if isDivisibleBy $number 5; then
 output="${output}Buzz"
 fi
 if [ -z $output ]; then
 echo $number
 else
 echo $output;
 fi
done
```

We took the expressions comparing the rest with zero, and moved them into a function. Even more, we eliminated the comparison with zero, because zero means true for us. We only have to return the value from the mathematical expression - very simple!

**Tip:** A function's definition must precede its call.

In Bash, you can define a method as `function func_name { commands; }`. Optionally, there is a second syntax for declaring functions: `func_name () { commands; }`. So, we can drop the string, `function` and add `"()"` after its name. I personally prefer this option, as exemplified in the example above. It's more explicit and resembles traditional programming languages.

You do not need to specify the parameters for a function in Bash. Sending parameters to a function is accomplished by simply enumerating over them after the function call separated by white spaces. Do not place commas or parenthesis in the function call - it won't work.

Received parameters are automatically assigned to variables by number. The first parameter goes to `$1`, the second to `$2`, and so on. The special variable, `$0` refers the current script's file name.

### Let's Play with Parameters

```
#!/bin/bash

function exampleFunc {
 echo $1
 echo $0
 IFS="X"
 echo "$@"
 echo "$*"
}

exampleFunc "one" "two" "three"
```

This code will produce the following output:

```
csaba@csaba ~ $ ./parametersExamples.sh
one
./parametersExamples.sh
one two thre
oneXtwoXthre
```

Let's analyze the source, line by line.

- The last line is the function call. We call it with three string parameters.
- The first line after the shebang is the function definition.
- The first line in the function outputs the first parameter: "one". So far so simple.
- The second line outputs current script's file name. Again, very simple.
- The third line changes the default character separator to the letter, "X". By default, this is " " (a space). That's how Bash knows how the parameters are separated.
- The fourth line outputs a special variable, `$@`. It represents all the parameters as a single word, exactly as specified in the function call.
- The final line outputs another special variable, `$*`. It represents all the parameters, taken one-by-one and concatenated with the first letter of the IFS variable. That's why the result is `oneXtwoXthre`.

## Returning Strings From Functions

As I noted earlier, functions in Bash can return only integers. As such, writing `return "a string"` would be invalid code. Still, in many situations, you need more than just a zero or one. We can refactor our FizzBuzz example so that, in the `for` statement, we will just make a function call.

```
#!/bin/bash

function isDivisibleBy {
 return $(($1%$2))
}

function fizzOrBuzz {
 output=""
 if isDivisibleBy $1 3; then
 output="Fizz"
 fi
 if isDivisibleBy $1 5; then
 output="${output}Buzz"
 fi
 if [ -z $output ]; then
 echo $1
 else
 echo $output;
 fi
}

for number in {1..100}; do
 fizzOrBuzz $number
done
```

Well, this is the first step. We just extracted all the code into a function, called `fizzOrBuzz`, and then replaced `$number` with `$1`. However, all outputting occurs in the `fizzOrBuzz` function. We want to output from the `for` loop with an `echo` statement, so that we can prepend each line with another string. We have to capture the `fizzOrBuzz` function's output.

```
#[...]
for number in {1..100}; do
 echo "-`fizzOrBuzz $number`"
 fizzBuzzer=$(fizzOrBuzz $number)
 echo "-${fizzBuzzer}"
done
```

We've updated our `for` loop just a bit (no other changes). We've now echoed everything twice in two different ways to exemplify the differences between the two solutions to the same problem.

The first solution to capture the output of a function or another command is to use backticks. In 99% of the cases, this will work just fine. You can simply reference a variable within backticks by their names, as we did with `$number`. The first few lines of the output should now look like:

```
csaba@csaba ~/Personal/Programming/NetTuts/The Basics of BASH Scripting/Sources $ ./fizzBuzz.sh
-1
-1
-2
-2
-Fizz
-Fizz
-4
-4
-Buzz
-Buzz
-Fizz
-Fizz
-7
-7
```

As you can see, everything is duplicated. Same output.

For the second solution, we've chosen to first assign the return value to a variable. In that assignment, we used `$()`, which, in this case, forks the script, executes the code, and returns its output.

## ;, && and ||

Do you remember that we used semicolon here and there? They can be used to execute several commands written on the same line. If you separate them by semicolons, they will just simply be executed.

A more sophisticated case is to use `&&` between two commands. Yes, that's a logical AND; it means that the second command will be executed only if the first one returns `true` (it exits with 0). This is helpful; we can simplify the `if` statements into these shorthands:

```
#!/bin/bash

function isDivisibleBy {
 return $(($1%$2))
}

function fizzOrBuzz {
 output=""
 isDivisibleBy $1 3 && output="Fizz"
 isDivisibleBy $1 5 && output="${output}Buzz"
 if [ -z $output ]; then
 echo $1
 else
 echo $output;
 fi
}

for number in {1..100}; do
 echo "-`fizzOrBuzz $number`"
done
```

As our function, `isDivisibleBy` returns a proper return value, we can then use `&&` to set the variable we want. What's after `&&` will be executed only if the condition is `true`. In the same manner, we can use `||` (double pipe character) as a logical OR. Here's a quick example below.

```
csaba@csaba ~ $ echo "bubu" || echo "bibi"
bubu
csaba@csaba ~ $ echo false || echo "bibi"
false
csaba@csaba ~ $
```

## Final Thoughts

So that does it for this tutorial! I hope that you've picked up a handful of new tips and techniques for writing your own Bash scripts. Thanks for reading, and stay tuned for more advanced articles on this subject.
