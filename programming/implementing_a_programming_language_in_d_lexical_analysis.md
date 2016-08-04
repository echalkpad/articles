# Implementing a programming language in D: Lexical Analysis

[Original URL](http://blog.felixangell.com/implementing-a-programming-language-in-d-part-1/)

> I've been writing a lot of D lately, and it's slowly becoming one of my favourite languages to write in. I'm not a master at D, but it's a lot easier to write than C++, and it's a lot less verbose...

I've been writing a lot of D lately, and it's slowly becoming one of my favourite languages to write in. I'm not a master at D, but it's a lot easier to write than C++, and it's a lot less verbose than writing C code. So for this series, I'll be implementing everything in D. You can follow along in D if you wish, but it should translate pretty well to any other language that you wish to use.

## Disclaimer

This is by no means an introduction to the D language, so if you have never written D, I cannot guarantee you can copy and paste the snippets and it will just work, so you should think twice about what language you wish to use, I suggest something you are familiar with. I'm also not a D God, so you any D gurus may be constantly critiquing my code, but this is written in a way that can be easily translated to other languages.

### Overview of Elba

_As the heading may suggest, I've decided to name the language Elba after the actor this may or may not be to do with my recent binge of Luther on Netflix..._

#### GitHub

I've also put the code up on GitHub [here](https://github.com/felixangell/elba), so if you're stuck you can check the repository for help. Or if you're just interested in seeing what you're going to build, it's all on there.

### Syntax

The language we will be implementing is very simple, here's a small code-sample that should compile at the end of the series correctly.

```
var a = 10; 
var b = 21;

// store the result of a + b into c
var c = a + b;

// simple function that does some
// random math to a and b and returns
// the result
def foo(a, b) { 
 return (a articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii b) + (b - a);
}

// store function call result into g
var g = foo(20, 20);

// call c printf function
printf("%d\n", g); 
```

What we can see from the code example is that the language will have:

- variables `var a = 10;`
- functions `def foo(a, b) {}`
- complex expressions `(a articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii b) + (b - a)`

  - _function calls also fall under this category_

- a simple data type (an integer)
- comments
- "c interop" - though this is kind of hacky and not true interop

Because we only have a single data type (an integer [whole number]), there isn't any type inference per se, though it may look otherwise. I'll leave it as an exercise to the reader to try and implement different types, such as floating point or strings. From this you can also decide if you want to try and infer the type with a type checking phase, or if you want to change the grammar and the parser to allow for the type to be explicitly annotated (e.g. `a: int = 5;` or `int a = 5;`).

### Lexical Analysis

#### What is it?

A compiler is divided into different phases to make things easier to implement, and actually read the source code. The first stage is typically "Lexical Analysis" (sometimes written as tokenizer, lexer), which is basically where we take some input in the form of text from a source file, and cut it up into tokens. This produces a token stream, which is passed to the parser to be parsed (haha). We do this stage for a few reasons. The first is it's a barrier for error checking, we can look at the source code and weed out some errors that may be present in the code. Additionally, it makes it easier for the parser to process, because we have simplified our source down into token categories. Take the following example:

```
var a = 5; 
```

If we fed this to the parser as some text for it to parse, it's trickier to process since we have all of this white-space in the way. We're also doing it as we go, so if we find an error, we have to stop halfway through the process, whereas if we put this into a different phase (a lexer), it's less expensive to do and we can weed out any errors quicker. We can also cut out the white-space, and find a category for each word in the source file.

The lexer would take this and produce a token stream like this:

```
identifier being a category, 
and the value in quotes being a lexeme:

[identifier => "var", identifier => "a", symbol => "=", number => 5]
```

This is a lot easier to be processed by the parser, because if it wants to parse a variable for instance, it can look if the stream of tokens has two identifiers, followed by a symbol, followed by a number (simplified example here), and then it can check the contents of these tokens, so it would check if the contents of the first identifier is "var". If it's not, it will throw an error, or try something else.

We can even be more specific, so if we got the number 5.5, we could that it's a floating point number, or if we got just 5, we can say it's a whole number or an integer. We can also group symbols under more finer categories, for instance we could say `+`, `-`, `/`, or `*` are symbols like `=` and `{`, etc. Or we could say they are binary operators, i.e. an operator that takes two expressions on the left and right to operate on. If we're writing something like Python where the tabs actually effect the source code, e.g. functions or something, then we can lex those into tokens.

#### How do I implement a lexer?

The first thing to do is to identify each category for your lexer given a source code example:

```
var a = 10; 
var b = 21;

// store the result of a + b into c
var c = a + b;

// simple function that does some
// random math to a and b and returns
// the result
def foo(a, b) { 
 return (a articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii b) + (b - a) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 20 / 20;
}

// store function call result into g
var g = foo(20, 20);

// call c printf function
printf("%d\n", g); 
```

From the source code above, we can see that we would need a few categories:

- identifier (for `var def foo a b`)
- symbols (for `( ) { } , + - ; = / *`)
- number (for `5 5.55555`)
- string (for `"hi"`, note we'll just eat _everything_ inside double quotes)
- character (for `mess`, note we'll just eat _everything_ inside single quotes)

Something to note, a lexeme is the contents or string value of a token, and the token category is the type of token. For instance, we have a token of the type/category identifier, where the lexeme is "foo".

Once we've identified what token categories we need, we need to actually lex it into a token stream! This is very straightforward, we identify what makes up the lexeme. Now a lexeme is a string, which is a sequence of characters. We need to read the source file character by character, and check if the character is suitable for any of the token categories.

We usually use a grammar to define a language, here's a simple grammar that defines a part of our language, specifically the part we need to know for writing our lexer:

```
letter = "a" ... "z" | "A" ... "Z"; 
digit = "0" ... "9"; 
identifier = letter, { letter | digit | "_" };

symbols = "(" | ")" | "{" | "}" | "," | "+" | "-" | "/" | "*" | ";" | "="; 
number = {digit} [ "." {digit} ]; 
```

If you've never read a grammar before, this will mostly look like a somewhat arranged mess of characters. The above snippet is a grammar written in EBNF or Extended Backus-Naur Form. Which you can read more about on [wikipedia](https://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_Form). For a quick guide, the symbols mean:

- `"something"` => terminal
- `"a" ... "z"` => characters from lower-case a to lower-case z
- `{ ... }` => repetition, more than zero of something
- `|` => choice, letter | digit is letter OR digit
- `,` => concatenation (joining things together)
- `[ ... ]` => option, i.e. it matches that rule with our without the thing inside the brackets
- `xyz = foo | bar | baz;` => a rule, our grammar is made up of rules

##### Grammar Breakdown

Here's a breakdown of our grammar step-by-step.

Here we specify that a letter can either be a lower-case or upper-case character from the alphabet (a to z).

```
letter = "a" ... "z" | "A" ... "Z"; 
```

A digit can either be a 0, 1, 2, 3, 4, 5, 6, 7, 8, or 9.

```
digit = "0" ... "9"; 
```

An identifier is something that identifies something, for instance a function like `def foo(a, b): ...` is identified by the identifier `foo`. An identifier in this grammar _must_ start with a letter, followed by a repetition of either letters, digits, or underscores.

```
identifier = letter, { letter | digit | "_" }; 
```

These are all of the symbols in our program, we could put these under<br>
sub-categories like pairs for the brackets, binary operators for the<br>
`+`, `-`, etc. but I'm lazy.

```
symbols = "(" | ")" | "{" | "}" | "," | "+" | "-" | "/" | "*" | ";" | "="; 
```

A number must start with a repetition of digits, like `1234`, and can<br>
_optionally_ be followed by a decimal place and a group of digits.

```
number = {digit} [ "." {digit} ]; 
```

#### Programming our Lexer!

Now for the actual code, we've identified most of the backbone for the lexer. Now we just need a few things.

It would be nice luxury to have something that represents source code, this way we can link our token to sourcefiles by holding an instance to the sourcefile in the token, which makes for nicer error handling.

```
class SourceFile { 
private: 
 string contents;
 string location;
 int length; // we need a way to check the source length

 // we have not defined our Token yet, but this 
 // is the files token stream that is produced
 Token[] tokens; 

 // helper function for reading files
 // you can make this static and move
 // into a global scope, or into some
 // util class if you wish
 string read_file(string location) {
 string result = "";
 File file = File(location, "r");
 while (!file.eof()) {
 string line = chomp(file.readln());
 result ~= line; // ~ is concatentation/append in D!
 }
 return result;
 }
public: 
 this(string location) {
 this.location = location;
 this.contents = read_file(location);
 this.length = cast(int) this.contents.length;
 }

 void push_token(Token t) {
 tokens ~= t; // here's that weird operator again
 }

 string get_location() {
 return location;
 }

 string get_contents() {
 return contents;
 }

 Token[] get_tokens() {
 return tokens;
 } 

 int get_length() {
 return length;
 }
};
```

We also want a structure that represents our Token, and an enum for the types of tokens.

```
class Token { 
private: 
 SourceFile owner;
 string lexeme;
 int type; 

public: 
 static enum {
 IDENTIFIER,
 NUMBER,
 STRING,
 CHARACTER,
 SYMBOL
 }

 this(SourceFile owner, string lexeme, int type) {
 this.owner = owner;
 this.lexeme = lexeme;
 this.type = type;
 }

 string get_contents() {
 return lexeme;
 }

 int get_type() {
 return id;
 }

 int get_length() {
 return length;
 }

};
```

Great, we've got some basic code for our lexer, now we need to write the actual lexer itself. So we'll just make a Lexer class first that will take a SourceFile to tokenize. We'll also need a few variables to help us out.

```
class Lexer { 
private: 
 SourceFile file;
 int position;
 int initial_position;
 bool running;
 char current_char;

public: 
 this(SourceFile file) {
 this.file = file;

 // these will be explained later on
 this.position = 0;
 this.initial_position = 0;
 this.running = true;

 // set character to first character in file
 this.current_char = file.get_contents()[0];
 }

 bool is_running() {
 return running;
 }
};
```

Now we'll add some utility functions in there, these will check if the given character matches the criteria for each category. So we have one like `is_identifier`, which will take a char `c`, and check if it matches the criteria for an identifier as defined in our grammar.

```
bool is_digit(char c) { 
 return (c >= '0' && c <= '9');
}

bool is_letter(char c) { 
 return (c >= 'a' && c <= 'z') || (c >= 'A' && c <= 'Z');
}

bool is_letter_or_digit(char c) { 
 return is_letter(c) || is_digit(c);
}

bool is_identifier(char c) { 
 return is_letter_or_digit(c) || c == '_';
}
```

We need a few utility functions to help us lex our source file, the first is a consume function, which will increment the position variable, and update the current_char, it also checks if we're about to hit EOF (the end of file).

```
void consume() { 
 // this looks confusing, but we check if 
 // on the next consume it is the end of the file
 // if it is, we increment the position, but we
 // set the character to the null terminator '\0'
 // so when we get the tokens, we can check if it's '\0'
 // and stop the lexer
 if (position + 1 >= file.get_length()) {
 position++;
 current_char = '\0';
 return;
 }

 // otherwise we just set the current char to
 // the character at {position} in the source file
 // note we increment position BEFORE, not AFTER!!
 current_char = file.get_contents()[++position];
}
```

Remember I mentioned there is the whitespace in sourcefiles that makes it hard to parse a sourcefile without any lexing? Well, we need to ignore it in the lexer, so that's what<br>
the following function is for:

```
void eat_layout() { 
 // in the ASCII table, any char below 32 (' ') is
 // pretty much junk, like tabs, backspaces, etc
 // though we want to make sure we don't eat the EOF
 while (current_char <= 32 && current_char != 0) {
 consume();
 }
}
```

Now we need a function that will get the next token in the source code (note that we take the source code directly from the file contents), this function will check what the current character is, and act accordingly.

First it eats all the junk characters, it will then set the `initial_position` to what the `position` currently is. This is to show the start of the token, so we can later extract it from the source file. Note that it's important that we set the initial_position **after** we eat the junk characters. To visualize everything, we assume a string is a sequence of characters, like an array.

For instance: `['a', 'b', 'c']` is the sequence of characters that make up the string `abc`. Say we wanted to lex the following sequence: `[' ', ' ', '\t', 'a', 'b', 'c']`, (`' '` is whitespace, and `'\t'` is a tab), we start at position 0, which is `' '` in our sequence. We then eat the layout, so that means our position is 3, which is 'a', since the spaces and tabs are layout that we don't want. We then set the `initial_position` to 3, lex our character and hopefully at the end of lexing our sequence our position will be 5\. We then extract from the sequence the string from 3 to 5, which is `"abc"`. This gets stored as a Token, and a type is set appropriately. If we were to set the initial position before eating the layout, our `initial_position` is 0, and the end is 5, so our token ends up being `" abc"`, which is not what we want.

```
void get_next_token() { 
 eat_layout();
 initial_position = position;
```

It then checks if the current_char is the null terminator, if it is, it will set the lexer up to stop running. Otherwise it will try match some tokens:

```
 if (current_char == '\0') {
 running = false;
 return; // exit out of this function
 }
```

Now we can start lexing, so we check if our current character is either a letter to match an identifier, or a digit to match a digit. Remember in our grammar, we said that our identifiers must start with a letter, and digts must start with a digit. The recognize functions will be shown later, but they basically eat all the characters and spit out a token.

```
 else if (is_letter(current_char)) {
 recognize_identifier();
 }
 else if (is_digit(current_char)) {
 recognize_digit();
 }
```

If the current_char is not a letter or a digit, we check if it matches any of the symbols we specified in our grammar.

```
 else {
 switch (current_char) {
 case '-': case '+': case '/': case '*':
 case '=': case '(': case ')': case '[':
 case ']': case ',': case '{': case '}':
 case ';': case '.':
 recognize_symbol();
 break;
```

If it's not a symbol, we check if it's a single or double quote to match a character or string.

```
 case '\'':
 recognize_character();
 break;
 case '"':
 recognize_string();
 break;
```

And finally, if it doesn't match any of these, we just throw an error (or print out some text...) to say we have an unrecognised character.

```
 default:
 writeln("unrecognised character ", current_char);
 break;
 }
 }
}
```

Basically what we have here is a simple state machine. We check if the current state is something that we expect, and act accordingly. Now for the actual action we need to act upon _hehe_, or in other words, the `recognize_xyz`, functions:

Recognizing a token is very simple, we keep consuming and consuming until the current character no longer matches the criteria of the token being tokenized. If it doesn't match the criteria, we assume that the token has been lexed, and we push it into our `Token` stream in the `SourceFile`.

First we need a few utility functions, just to reduce duplicate code:

```
void push_token(int type) { 
 // push a new Token, where the contents is cut from initial_position to position
 // (basically a substring if your language doesn't have this fancy sugar)
 // and we set the type to the one passed
 // this is pushed to the file being lexed
 file.push_token(new Token(file, file.get_contents()[initial_position .. position], type));
}
```

Here's the `recognize_identifier` function:

```
void recognize_identifier() { 
 while (is_identifier(current_char)) {
 consume();
 }
 push_token(Token.IDENTIFIER);
}
```

I'll leave the rest as an exercise to the reader, but remember you can always check the GitHub repository if you're stuck [here](https://github.com/felixangell/elba). But to give you a better idea, here's a more complicated token to tokenize:

```
void recognize_digit() { 
 while (is_digit(current_char)) {
 consume();
 }

 if (current_char == '.') {
 consume(); // eat the dot

 while (is_digit(current_char)) {
 consume();
 }
 }

 push_token(Token.NUMBER);
}
```

Once you've done all this, in the main function (which we haven't yet created), create an instance of your lexer, and call get_next_token while lexer.running is true. Note, maybe you want to make a start method that will do this part. You can then iterate through the file.get_tokens array, and print out the content and type of each token.

```
// main.d
import lexer; 
import source_file;

int main() { 
 auto file = new SourceFile("tests/somefile.foo");

 auto lexer = new Lexer(file);
 while (lexer.is_running()) {
 lexer.get_next_token();
 }
 foreach (token; file.get_tokens()) {
 writeln("contents:", token.get_contents());
 }
}
```

And that concludes this article in the series! Hope you enjoyed and learnt new things!

Here's what we've made so far:

![](http://blog.felixangell.com/content/images/2015/12/yV2eLtO.gif)

[NEXT ARTICLE](http://blog.felixangell.com/implementing-a-programming-language-in-d-part-1/)

As an exercise for the reader, you can try and get multiple files from the command line arguments.

_Tip: iterate through the arguments, check if the file extensions are equal to ".foo", if they are add them to an array of SourceFiles, iterate through said array and create a new Lexer instance for each one._

Another bonus exercise would be to pretty print the token stream. But I won't give you the answer for this one...
