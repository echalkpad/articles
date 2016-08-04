# The What, How and Why Of Single Var Pattern in JavaScript?

[Original URL](http://www.smashingbuzz.com/2015/09/single-var-pattern-javascript/)

> Are you tired of dealing with language rules and conventions? Well, then you can switch to using JavaScript, as it gives flexibility over applying rules and code conventions. For instance, JavaScript...

Are you tired of dealing with language rules and conventions? Well, then you can switch to using JavaScript, as it gives flexibility over applying rules and code conventions. For instance, JavaScript allows users to create any type of variable within the script in your web page. While this may seem an advantage to you in the first place, however, it can also result in unfavorable consequences if you won't work carefully with the JavaScript variables.

![The What, How and Why Of Single Var Pattern in JavaScript?](http://www.smashingbuzz.com/wp-content/uploads/2015/09/The-What-How-and-Why-Of-Single-Var-Pattern-in-JavaScript.jpg)

Fortunately, there is an excellent solution that can help you work with JavaScript variables in the most effective way possible, called as single var pattern. In fact, many JavaScript legends have been encouraging to utilize the single var pattern, as it helps in preventing plenty of mistakes often made by both novice and experienced JavaScript developers.

Douglas Crockford, who built JSLint the popular code quality tool likes the single var pattern and strongly advocates using it. Below is his opinion as to why JavaScript users must support this pattern:

> **"When using languages with block scope, it is usually recommended that variables must be declared as close as possible to the first place where they're used. But, since JavaScript does not have block scope, it is wiser to declare all of a function's variables at the top of the function. It is recommended that a single var statement be used per function. This can be declined with the vars option."**

Through this post, I would like to make you understand about the JavaScript "single var pattern" and why you should consider using it in your development projects.

## An Overview of The Single Var Pattern

The single var pattern, basically, declares all of the (local) variables at the top of a scope block (i.e. a function block) using only a single "var" statement. Furthermore, all the variables are separated by a comma (,), while the last variable contains a semicolon (;) at the end.<br>
Let's look at an example of the single var pattern:

```
function myfuncname() {
var  a = 10,
b = 20,
c = { name: "XYZ", age: 25 },
d;

}
```

As you can see the above example amounts to having many different var statements for each variable, as follows:

```
function myfuncname() {

var a = 10;

var b = 20;

var c = { name: "XYZ", age: 25};

var d;

}
```

The multiple var statement pattern as shown in the code snippet above is a viable alternative to the single var pattern. That's because, it consists of all the variables that are declared at the beginning of the function block, which helps in preventing logical errors. Plus, it makes the code more readable and so on.

Since JavaScript ignores commas and semicolons included in tabs, newlines, and other spacing characters, the above example could have been written in just a single line:

```
function myfuncname() {

var a = 10, b = "20, c = { name: "XYZ", age: 25}, d;

}
```

However, it's a good practice to declare each variable individually on one line for improved readability. This also makes it easy to comment your variables separately (if needed).

## Alternative Way To Using Single Var Pattern

Apart from the above discussed single var pattern, there is another way (or formatting style) that can be used for the single var pattern. This alternative patter requires to put the comma-separators at the very beginning of each line, rather than adding it at the end:

```
function myfuncname() {

var   a = 10

, b = "Hello!"

, c = { name: "ABC", age: 44 }

, d = new Date()

, e;

}
```

Whether you should place the commas at the start or the end of each line depends upon your preference. However, it would be better if you'll prefer to put the commas at the end of the line, as it enables you to read the var statements easily.

So, Why You Should Use the Single Var Pattern?

Below are a few benefits that you get by using the single var pattern:

## 1\. Avoids Confusion

The biggest benefit of using the single var pattern is that it helps avoid confusion, by making it easier to find where a certain variable is; since this JavaScript pattern provides one single place to search for all the local variables essential to be used by the function. You just need to look at the top of the function to see or modify your it's variables.

## 2\. Prevents Logical Errors From Occurring

Since all of the variables are declared at the beginning of the function block, it avoids logical errors that are related to hoisting, declaring global variables and naming collisions. Let us discuss about these errors one by one:

## Variable Hoisting

JavaScript allows to place "multiple var statements" anywhere within a function, and will make you believe that the variables were declared at the start of the function block. JavaScript's behavior of processing the statements prior to executing else is known as hoisting. Now, in case you're using a variable and declare it within the function later causes logical errors. Because the single var pattern intentionally puts all the variables at the top of the function, it helps prevent related hoisting from taking place.

## Naming Collisions

: While working on large-size and complicated scripts, it's easy to accidentally reuse a variable name, which could lead to logical errors. However, since the single var pattern help in keeping all the variables in one spot, it becomes easy to keep track of the variable names that have already been used.

## Minimize Accidental Global Variables

: Having too many global variables can cause naming collisions with other JavaScript libraries – you probably would be using – that relies upon global variables. And thus, it is recommended that you should get rid of unnecessary global variables to avoid naming collisions. Besides, it is a good practice to use local variables over global ones, since the former ones are faster to access compared to the latter. The single var pattern will help you remember to create local variables and thereby reduces accidental global variables.

## 3\. Make You Write Less Code

Lastly, using single var statement reduces the amount of code that you need to write. Let us consider an example to understand how it helps in writing less code:

```
// A function that returns a random integer number
// between two specified integer arguments.

var myrandomizer = function (a, b) {

var minvalue = a,

maxvalue = b,

random = Math.random(),

result;

// Calculate random number

result = Math.floor(random binaryjs.md cooking_with_docker_and_coreos_on_os_x.md create_a_rest_api_with_nodejs.md data data.json even_easier_restful_api_with_nodejs_and_express_framework.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md hacker_codex.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md introduction.md just_a_theory.md links_list.txt md null.md parse_urls_json.sh png reclaiming_a_timemachine_volume’s_disk_space.md sqljs.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md url_to_filename.csv (maxvalue - minvalue) + minvalue);

return result;

}

// Display random number in an alert box

alert(myrandomizer(1, 100));
```

You can reduce the above amount of code, by including the calculation of the variable 'result' within the single var statement, as follows:

```
// A function that returns a random integer number

// between two specified integer arguments.

var myrandomizer = function (a, b) {

var minvalue = a,

maxvalue = b,

random = Math.random(),

// Calculate random number

result = Math.floor(random binaryjs.md cooking_with_docker_and_coreos_on_os_x.md create_a_rest_api_with_nodejs.md data data.json even_easier_restful_api_with_nodejs_and_express_framework.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md hacker_codex.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md introduction.md just_a_theory.md links_list.txt md null.md parse_urls_json.sh png reclaiming_a_timemachine_volume’s_disk_space.md sqljs.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md url_to_filename.csv (maxvalue - minvalue) + minvalue);

return result;

}

// Display random number in an alert box

alert(myrandomizer(1, 100));
```

Conclusion

Hopefully, this post will help you in becoming familiar with the JavaScript "Single Var Pattern" and its benefits. But, many professionals have also [criticized using the single var pattern in JavaScript](http://danhough.com/blog/single-var-pattern-rant/). And so, make sure to carefully analyze its set of pros and cons to make an informed decision.

Author Biography: Jack Calder is currently a Senior web developer in Markupcloud Ltd. He always use to share extinct & concrete information on web design and development. Jack is curently involved in [psd to html service](http://www.markupcloud.com/services/psd-to-html) with extraneous efforts on web design templates.
