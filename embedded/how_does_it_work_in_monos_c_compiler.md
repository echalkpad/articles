# How does it work in Mono's C# compiler?

[Original URL](http://www.codeproject.com/Articles/330184/How-does-it-work-in-Mono-s-C-compiler)

> Download source code - 1.78 MB Table of Contents Introduction Mono is an Open Source free programming language project. It is an implementation of Microsoft's .NET Framework based on the...

- [Download source code - 1.78 MB](http://www.codeproject.com/KB/cs/330184/Mono_C__Compiler_source_code_-gmcs.zip)

## Table of Contents

## Introduction[]()

Mono is an Open Source free programming language project. It is an implementation of Microsoft's .NET Framework based on the European association for standardizing information and communication systems ([ECMA](http://msdn.microsoft.com/en-us/netframework/aa569283)) standards for C# language and Common Language Runtime ([CLR](http://en.wikipedia.org/wiki/Common_Language_Runtime)). The Mono [C#](http://msdn.microsoft.com/en-us/library/z1zx9t92.aspx) compiler was started by Miguel de Icaza. In Table 1, I have tried to show the different components of Mono and a brief description of those components to show what they do.

Table 1: Mono source code components

**Component** **Description** C# Compiler Mono's C# compiler is an implementation of the C# language based on the ECMA specificiation. It is now with C# 1.0, 2.0, 3.0, 4.0. Mono Runtime The runtime implements the ECMA Common Language Infrastructure (CLI). The runtime provides a Just-in-Time (JIT) compiler, an Ahead-of-Time compiler (AOT), a library loader, the garbage collector, a threading system, and interoperability functionality. Base Class Library The Mono platform provides a comprehensive set of classes that provide a solid foundation to build applications on. These classes are compatible with Microsoft's .NET Framework classes. Mono Class Library Mono also provides many classes that go above and beyond the Base Class Library provided by Microsoft. These provide additional functionality that are useful, especially in building Linux applications. Some examples are classes for Gtk+, Zip files, LDAP, OpenGL, Cairo, POSIX, etc. **Note**: The information shown in the above table has been retrieved from <http://www.mono-project.com/What_is_Mono>.

There are many version of the Mono compiler. Table 2 shows the different versions of the Mono compiler and the framework each supports.

Table 2: Mono compiler version and related frameworks

Compiler Version Target Framework mcs 1.1 gmcs 2.0 smcs 2.1 dmcs 4.0 **Note**: The information shown in the table has been retrieved from <http://www.mono-project.com/CSharp_Compiler>.

The compiler mcs now defaults to the 3.x language specification, starting with Mono 2.8.

## []()Getting the Mono source code

Mono is a freely available Open Source C# programming language project. If you want to download the Mono C# compiler project's source code, there are many places to do so. We can use gitHub for instance. The URL for the Mono source code in gitHub is <https://github.com/mono/mono/branches>. Or we can download from other places such as <http://www.go-mono.com/mono-downloads/download.html>. I downloaded the Mono source code from gitHub site (Figure 4.1). There are a few branches of Mono for example, as seen in Figure 4.1\. Mono has mono-2-10, mono-2-10-8, mono-2-6, mono-2-8, etc. In the following Table 3, I show the different directories of Mono and a short description of each.

Table 3: Mono source code directory

docs Technical documents about the Mono runtime. data Configuration files installed as part of the Mono runtime. mono The core of the Mono Runtime. metadata The object system and metadata reader. mini The Just in Time Compiler. dis CIL executable Disassembler cli Common code for the JIT and the interpreter. io-layer The I/O layer and system abstraction for emulating the .NET IO model. cil Common Intermediate Representation, XML definition of the CIL byte codes. interp Interpreter for CLI executable (obsolete). arch Architecture specific portions. mcs The core of the Mono Compiler code mcs mcs Compiler source code jay Parser generator man Manual pages for the various Mono commands and programs. samples Some simple sample programs on uses of the Mono runtime as an embedded library. scripts Scripts used to invoke Mono and the corresponding program. runtime A directory that contains the Makefiles that link the mono/ and mcs/ build systems. _../olive_ If the directory _../olive_ is present (as an independent checkout) from the Mono module, that directory is automatically configured to share the same prefix than this module gets. **Note**: The above Mono source code directory has been retrieved from <https://github.com/mono/mono>.

The Mono compiler source code resides inside the _mcs_ folder of _/mono/mcs_.

## []()Jay

Jay is an Open Source Compiler-Compiler tool derived from Berkeley Yacc. It is used in the Mono project as a Compiler-Compiler tool to generate the parser of the Mono C# compiler. Jay reads the grammar specification from a grammar file and generates an LR parser for it. This _cs-parser.jay_ file is used by Jay to turn into _cs-parser.cs_ file which will be consumed by the Mono C# compiler as the parser.

## cs-parser.jay to cs-parser.cs conversion[]()

Cygwin is a set of Open Source tools which provide a Linux like environment for Windows where Linux applications, for example, Shell can be used in Windows. So now we assume we have a working Cygwin environment in our desktop. We will open the Cygwin terminal by clicking on _Start > Program Files > Cygwin > Cygwin Terminal_. When we open the Cygwin terminal, it will be like Figure 1.

![330184/1_CygwinOpenmode.jpg](http://www.codeproject.com/KB/cs/330184/1_CygwinOpenmode.jpg)

Figure 1: Cygwin Open mode

Please copy the Mono source code inside the _/usr/src_ directory of the Cygwin installation directory. And now open the Cygwin terminal and write the following command listed in Code-Listing 1.

Code-Listing 1: Bash Command to convert cs-parser.jay to cs-parser.cs

```
$cd /usr/src/Mono/mcs
$cd jay
$make
$cd ..
$cd mcs
$../jay/jay.exe -ctv < ../jay/skeleton.cs cs-parser.jay > cs-parser.cs
```

Please see the following figure:

![330184/2_BashCommandOutput.jpg](http://www.codeproject.com/KB/cs/330184/2_BashCommandOutput.jpg)

Figure 2: Bash Command output

So after executing _Jay.exe_ with the appropriate argument, it will convert the _cs-parser.jay_ file into _cs-parser.cs_ which is the parser for Mono.

## Mono source code relationship[]()

According to the documentation supplied with the Mono source code (_mcs\mcs\compiler.txt_ or <https://github.com/mono/mono/blob/master/mcs/docs/compiler.txt> ), the entire source code file for the Mono C# compiler has been divided into five categories: Infrastructure, Parsing, Expressions, Statements and Declarations, classes, structs, Enumerations. If we look into the following Mono C# compiler source code classification table 4, then it will be easier to understand all the types used in the compiler construction in Mono.

Table 4: Mono Source code classification

Mono Compiler Source code classification Infrastructure Parsing Expressions Statements Declarations, Classes, Structs, Enumerations _driver.cs_ _cs-tokenizer.cs_ _ecore.cs_ _statement.cs_ _decl.cs_ _codegen.cs_ _cs-parser.jay, cs-parser.cs_ _expression.cs_ _iterators.cs_ _class.cs_ _attribute.cs_ _location.cs_ _assign.cs_ _delegate.cs_ _rootcontext.cs_ _constant.cs_ _enum.cs_ _typemanager.cs_ _literal.cs_ _interface.cs_ _report.cs_ _cfold.cs_ _parameter.cs_ _support.cs_ _pending.cs_ **Note**: The above Mono source code classification has been retrieved from <https://github.com/mono/mono/blob/master/mcs/docs/compiler.txt>.

## Mono compilation in depth[]()

The Mono C# compiler starts compilation from the _driver.cs_ file. By calling the `public bool Compile ()` method, Mono starts its compilation process. It then initializes the `TopLevelTypes` variable of the `RootContext` class. After doing that, it calls the `Parse()` method of the driver class. The `Parse()` method then calls `void Parse (CompilationUnit file)` to start reading from the source code file. After reading from the source code file, the _driver.cs_ file starts the parsing process by calling the:

```
void Parse (SeekableStreamReader reader, CompilationUnit file)
```

method. It will create an instance of the Mono parser by creating an instance of an object of `CSharpParser` by calling the

```
public CSharpParser(SeekableStreamReader reader, CompilationUnit file, CompilerContext ctx) 
```

constructor. If we look into the partial code of the `Compile` method from the _driver.cs_ file listed in code-listing 1, we can see the main flow of the compilation process.

Code-Listing 1: Partial source code of the Compile method

```
public bool Compile()
{
 RootContext.ToplevelTypes = new ModuleContainer(ctx, RootContext.Unsafe);
 Parse();
 ProcessDefaultConfig();
 GlobalRootNamespace.Instance.AddModuleReference(RootContext.ToplevelTypes.Builder);
 LoadReferences();
 TypeManager.InitOptionalCoreTypes(ctx);
 RootContext.ResolveTree();
 if (!RootContext.StdLib)
 RootContext.BootCorlib_PopulateCoreTypes();
 RootContext.PopulateTypes();
 NamespaceEntry.VerifyAllUsing();
 if (Report.Errors > 0)
 {
 return false;
 }
 CodeGen.Assembly.Resolve();
 if (RootContext.VerifyClsCompliance)
 {
 }
 RootContext.EmitCode();
 RootContext.CloseTypes();
 CodeGen.Save(output_file, want_debugging_support, Report);
}
```

From Code-Listing 1, we can see the Mono parser starts parsing by calling `public void parse()` of the driver class which will call the following `Parse` method listed in Code-Listing 2 to continue parsing.

Code-Listing 2: Source code of Parse method

```
void Parse (SeekableStreamReader reader, CompilationUnit file)
{
 CSharpParser parser = new CSharpParser (reader, file, ctx);
 try {
 parser.parse ();
 } catch (Exception ex) {
 Report.Error(589, parser.Lexer.Location,
 "Compilation aborted in file `{0}', {1}", file.Name, ex);
 }
}
```

This `Parse` method will create an instance of the `CSharpParser` class by calling the constructor listed in Code-Listing 3\. This will make an instance of the Mono parser generated by Compiler-Compiler tool Jay as we discussed earlier.

Code-Listing 3: Constructor of the CSharpParser class

```
public CSharpParser (SeekableStreamReader reader, CompilationUnit file, CompilerContext ctx)
```

The constructor listed in Code-Listing 3 will take the file reader stream and a readonly value of the compiler context defined in the driver class.

Code-Listing 4: CSharpParser declaration in the cs-parser.jay file

```
%{
using System.Text;
using System.IO;
using System;
namespace Mono.CSharp
{
using System.Collections;
 public class CSharpParser
 {
```

The parser object created from `CSharpParser` will call the internal `parse()` method of the `CSharpParser` class which will call the Compiler-Compiler generated `yyparse` method. To call `yyparse`, it needs to pass the lexer or the tokenizer in it as a parameter. The code listed in Code-Listing 5 shows the method signature of the `yyparse` method which takes the lexer as a parameter.

Code-Listing 5: Signature of the yyparse method

```
internal Object yyparse (yyParser.yyInput yyLex)
```

The parsing will take place in this `yyparse` method. This `yyparse` method will parse each of the tokens generated by the lexer.

Code-Listing 6: Lexer initialization in the CSharpParser constructor

```
public CSharpParser (SeekableStreamReader reader, CompilationUnit file, CompilerContext ctx)
{
 lexer = new Tokenizer (reader, file, ctx);
}
```

The `yyparse` method will call the `xToken()` method of the lexer to generate tokens for it. The lexer will return a token by doing lexical analysis of the source code of a program. Before we move ahead, we need to understand the token generation process. In Mono, token generation is an interesting process, the `Tokenizer` class reads each character from the source code (for example, in this instance, _ClassToParse.cs_ listed in Code-Listing 13) one by one and will match with keywords stored in the tokenizer class to find out whether it has an associate keyword or find if it is a literal. The lexer will perform this operation by calling the `Is_identifier_start_character(int c)` method, to find out whether the character is an identifier or not by calling the `get_char()` method. If it is an identifier, then the lexer will call the `consume_identifier(int s)` method to consume the identifier. The logic is shown in code-listing 7.

Code-Listing 7: Identifier check in tokenizer.cs

```
if (is_identifier_start_character (c)) {
 tokens_seen = true;
 return consume_identifier (c);
}
```

While the lexer tries to consume the identifier, it will try to find out if there is any keyword match as shown in Code-Listing 8 by calling the `GetKeyword` method.

Code-Listing 8: consume_identifier of tokenizer.cs

```
private int consume_identifier (int c, bool quoted)
{
 while ((c = get_char ()) != -1) {
 if (id_builder [0] >= '_' && !quoted) {
 int keyword = GetKeyword (id_builder, pos);
 if (keyword != -1) {
 val = loc;
 return keyword;
 }
 }
 CharArrayHashtable identifiers_group = identifiers [pos];
 if (identifiers_group != null) {
 val = identifiers_group [id_builder];
 if (val != null) {
 val = new LocatedToken (loc, (string) val);
 if (quoted)
 AddEscapedIdentifier ((LocatedToken) val);
 return Token.IDENTIFIER;
 }
 }
 val = new String (id_builder, 0, pos);
 identifiers_group.Add (chars, val);
 val = new LocatedToken (loc, (string) val);
 if (quoted)
 AddEscapedIdentifier ((LocatedToken) val);
 return Token.IDENTIFIER;
}
```

If the token is not a keyword, then the lexer will mark it as an identifier and return `IDENTIFIER` as the token type and the word it consumes from the stream will be stored in the `val` object of the lexer class, i.e., the `Tokenizer` class (_cs-toeknizer.cs_). The `val` is an object type private variable defined in the lexer (which is _cs-tokenizer.cs_). The `val` object of the _Tokenizer.cs_ file is accessible via a property called `value` in the `Tokenizer` class listed in Code-Listing 9.

Code-Listing 9: value property of tokenizer.cs

```
public Object value ()
{
 return val;
}
```

After finish the checking process, the lexer will return the token to the parser to continue the parsing process. So when the parser finds a token value returned by the lexer (for example, 418 for `IDENTIFIER` for the example listed in Code-Listing 5.18; see appendix for details of the Mono tokens), then the parser will treat it as an identifier and try to access the `val` associated with the identifier. To access the `val` of the `Tokenizer` class, the parser will call the `value` property of the lexer and assign the value into `yyVal` (`yyVal` object type local variable defined in the `yyparse` method) of the parser. Each of this `yyVal` will be stored in the `yyVals` array inside the `yyparse` method in the `CSharpParser` class. The value stored in the `yyVals` array will be used later as a substitute variable for the grammar. In the following Code-Listing 5.13, we can see how `yyVal` is being stored in the `yyVals` array. Note: Substitution is the grammar-parser communication, i.e., to pass a value into a grammar file. For example, if we want to substitute variable $1 or $2 or $3 defined in the grammar from the parsing, we have to pass the substitute value from the parser. In here, `yyVals` will store that entire substitute variable for the grammar as per the tokens.

Code-Listing 10: Source code of the yyparse method of cs-parser.cs

```
internal Object yyparse(yyParser.yyInput yyLex)
{

 for (int yyTop = 0; ; ++yyTop)
 {

 yyVals[yyTop] = yyVal;
 if (debug != null) debug.push(yyState, yyVal);

}
```

When the parser accesses the value from the `Tokenizer` using the `yyVal=yyLex.value()` statement, it then assigns back `yyVal` to `yyVals` as shown in Code-Listing 5.13\. This communication between the lexer and the parser is like Just in Time, i.e., whenever the parser requires a token, it will ask for it by calling the `xToken()` method of the lexer and the lexer will execute the `xtoken()` method to perform the operation for the parser. So when the parser gets a token from the lexer, it will calculate the `yyN` value. The usage of the `yyN` value in Mono is to match with the appropriate grammar action block. `yyN` is one of the important variables in the parser because it is actually used to do the mapping between the token value returned from the source code file by the lexer and the grammar (language specification, for example, _cs-parser.jay_). Using the token value, the parser will match with the grammar action block defined in the `yyparse` method (the `switch case` statements generated by the Compiler-Compiler Jay). If it matches any `case` statement, then the parser will execute the related code block defined in the matched `case` condition. This code block will initialize the related abstract syntax tree node type, for example, a type of Statement object or Expression object and it will add the type object into the `TypeContainer`.

In short, parser will execute the action block of the grammar when any token value matched with the value yyNN, for example from the grammar file _cs-parser.jay_ file of the Mono has following grammar showing in the code-Listing 11, in the line 1265 for the method declaration.

Code-Listing 11: Grammar declaration of the Method in cs-parser.jay

```
method_declaration
: method_header {
if (RootContext.Documentation != null)
Lexer.doc_state = XmlCommentState.NotAllowed;
}
method_body
{
Method method = (Method) $1;
method.Block = (ToplevelBlock) $3;
current_container.AddMethod (method);
if (current_container.Kind == Kind.Interface && method.Block != null) {
Report.Error (531, method.Location, "`{0}': interface members cannot have a definition", method.GetSignatureForError ());
}
current_generic_method = null;
current_local_parameters = null;
if (RootContext.Documentation != null)
Lexer.doc_state = XmlCommentState.Allowed;
};
```

The method grammar specified in the grammar specification file in this case _cs-parser.jay_ file also defined in the _cs-parser.cs_ file within a case statement. In this case, the case condition value is 159 (159 is given by Compiler-Compiler tool in this case Jay while converting _cs-parser.jay_ into _cs-parser.cs_) as shown in the code-listing 12\. The code block defined for the method in the grammar will execute whenever lexer return a token value which become 159 as yyN value (yyN value is generate based on the token). This code block actually add instance of Method class into the Type container.

Code-Listing 12: Partial code block from the yyparser method

```
switch (yyN)
{
case 159:
#line 1265 "cs-parser.jay"
{
Method method = (Method)yyVals[-2 + yyTop];
method.Block = (ToplevelBlock)yyVals[0 + yyTop];
current_container.AddMethod(method);
if (current_container.Kind == Kind.Interface && method.Block != null)
{
Report.Error(531, method.Location, 
 "`{0}': interface members cannot have a definition", 
 method.GetSignatureForError());
}
current_generic_method = null;
current_local_parameters = null;
if (RootContext.Documentation != null)
Lexer.doc_state = XmlCommentState.Allowed;
}
break;
}
```

From the above code listed in Code-Listing 12, we can see there is communication between this grammar and _cs-parser.jay_ file using substitute variable. In the Code-Listing 11 there are two substitute value $1 which will be replaced by the value returned from (Method)yyVals[-2 + yyTop] and $3 by the return value of (ToplevelBlock)yyVals[0 + yyTop] from the code listed in the Code-Listing 12\. This is how whole grammar will match with the token return by the lexer and the action block will be executed based on the grammar. The same process will continue until the end of the source code file, i.e., finalizes the token searching from the source code.

## []()Debug Mono compilation

We will experiment using Source Code Listing 13 and try to understand the following two basic things by debugging the Mono compiler using Visual Studio 2010 as IDE:.

- How does Mono retrieve tokens and parse source code.
- How does it build the AST.

The following `ClassToParse` class listed in Code-Listing 13 is written using C# and will be used as the source code for this experiment. `ClassToParse` is a simple program which has a `using` statement and a namespace declaration. It also defines a class and inside of the class, it has a `Main` method which is the entry point.

Code-Listing 13: Source code to display Hello! world on the Console.

```
using System;
namespace gmcs
{
public class ClassToParse
{
 public static int Main (string[] args)
 {
 Console.WriteLine("Hello! World.");
 return 1;
 }
}
}
```

The above `ClassToParse` program will be used to do this experiment. To start this debug we require to do bit of ground work such as we need to do, modify the `Main (string[] args)` method of the _driver.cs_ file of the Mono source code as shown in the Code-Listing 14.

Code-Listing 14: Source code Main method of the driver.cs

```
public static int Main(string[] args)
{
 Location.InEmacs = Environment.GetEnvironmentVariable("EMACS") == "t";
 args = new string[] { @"C:\Temp\ClassToParse.cs", @"-out:C:\Temp\Otu.exe" };

 Driver d = Driver.Create(args, true, new ConsoleReportPrinter());
 if (d == null)
 return 1;
 if (d.Compile() && d.Report.Errors == 0)
 {
 if (d.Report.Warnings > 0)
 {
 Console.WriteLine("Compilation succeeded - {0} warning(s)", d.Report.Warnings);
 }
 Environment.Exit(0);
 return 0;
 }
 Console.WriteLine("Compilation failed: {0} error(s), {1} warnings",
 d.Report.Errors, d.Report.Warnings);
 Environment.Exit(1);
 return 1;
}
```

In the above code listed in the 14, I added the _ClassToParse.cs_ file path into the `args[]` array (which is _C:\Temp\ClassToParse.cs_) and set the parsing option along with output filename for instance in here _Out.exe_. If we put a break point on the `if (d.Compile() && d.Report.Errors == 0)` line:

When it starts debugging, the Mono compiler will call the `Compile()` method of the `Driver` object for instance, `d.Compile()` starts calling another method to start compiling as below. If we look into the `Compile()` method of the _driver.cs_ class, we can see the major functionality is as below:

Code-Listing 15: Compile method of the driver.cs

```
public bool Compile ()
{
 RootContext.ToplevelTypes = new ModuleContainer (ctx, RootContext.Unsafe);
 Parse ();
 ProcessDefaultConfig ();
 LoadReferences ();
 RootContext.ResolveTree ();
 RootContext.PopulateTypes ();
 NamespaceEntry.VerifyAllUsing ();
 CodeGen.Assembly.Resolve ();
 RootContext.CloseTypes ();
 CodeGen.Save (output_file, want_debugging_support, Report);
 }
```

Depending on the tokenize status inside the parser method, it will go further, i.e., it will start `tokenize_file`.

Code-Listing 16: Parse method of the cs-parser.cs

```
public void Parse()
{
 Location.Initialize();

 ArrayList cu = Location.SourceFiles;
 for (int i = 0; i < cu.Count; ++i)
 {
 if (tokenize)
 {
 tokenize_file((CompilationUnit)cu[i], ctx);
 }
 else
 {
 Parse((CompilationUnit)cu[i]);
 }
 }
}
```

and finally the parser will call `parse()` of the `CSharpParser` class which has been generated by Jay.

Code-Listing 17: Parse method of the Mono

```
void Parse(SeekableStreamReader reader, CompilationUnit file)
{
 CSharpParser parser = new CSharpParser(reader, file, ctx);
 try
 {
 parser.parse();
 }
 catch (Exception ex)
 {
 Report.Error(589, parser.Lexer.Location,
 "Compilation aborted in file `{0}', {1}", file.Name, ex);
 }
}
```

All the grammar specified in _cs-parser.jay_ has an action block against the rule and also in the parser method there is a mapping between this grammar and their associate action (please see the Appendix for full listing of grammar for the Mono C# compiler) as a `case` of the `switch` statement. Depending on (the token value converted into) `yyN` value, the related action will be executed to build the abstract syntax tree. If we look into Figure 3, we can see how Mono consumes a token as it calls `Parse()` of _driver.cs_ and then `yyparse` of the `CSharpParser` class. `yyparse` will consume the token from the input stream by calling the `xtoken()` method of the `Tokenizer` class.

![330184/3_StackTraceOfCompile.jpg](http://www.codeproject.com/KB/cs/330184/3_StackTraceOfCompile.jpg)

Figure 3: Stack trace of the Compile() method

Before we go ahead, we will a have a look at the process that takes place inside the `xtoken()` method. In the first phase of file reading, `Tokenizer` will read the first character from the stream which will be 117\. In here, 117 is the representation of u in ASCII (please see the Appendix for the complete list of ASCII and decimal value tables). If we look at Figure 4, it shows the current value of c (character refers to the token) is 117 which is u and it is the first character of the `using` statement used in the `ClassToParse` class.

![330184/4_TokeinizingU.jpg](http://www.codeproject.com/KB/cs/330184/4_TokeinizingU.jpg)

Figure 4: Tokenizing the ClassToParse class

As 117 is not a standard token, so it will be validated as the identifier and tokenizer will start consuming the identifier as showing in the Figure 5,

![330184/5_TokeinizingUsing.jpg](http://www.codeproject.com/KB/cs/330184/5_TokeinizingUsing.jpg)

Figure 5: Tokenizeing using statement

After consume the identifier it will match with the stored keyword inside the tokenizer class and try to find out whether it is a Keyword or not as showed in the Figure 6.

![330184/6_TokeinizingUsingKeyword.jpg](http://www.codeproject.com/KB/cs/330184/6_TokeinizingUsingKeyword.jpg)

Figure 6: Keyword matching

It will be identified as a keyword as Mono has a keyword with value 335 (please see the Appendix for a full list of tokens). And finally, the lexer will return the token value 335 which is the `using` statement. Figure 7 shows the `return` statement of the token method of the lexer which returns 335 as the current token value.

![330184/7_CurrentTokenValue.jpg](http://www.codeproject.com/KB/cs/330184/7_CurrentTokenValue.jpg)

Figure 7: Current Token value from the token() of the cs-tokenizer.cs

The parser will now try to find out whether there is any condition which is equal to this token value, if so, it will execute the related action block defined as part of the grammar.

Before parser can execute the action block it has to calculate the yyN value as we see earlier yyN is the mapping between token value and grammar. The bit of code which parser uses to calculate yyN is listed in the Code-Listing 18.

Code-Listing 18: yyN calculation based on yyTable

```
if ((yyN = yyRindex[yyState]) != 0 && (yyN += yyToken) >= 0
 && yyN < yyTable.Length && yyCheck[yyN] == yyToken)
 yyN = yyTable[yyN];
```

If we see the Figure 8, we can see the watch window while debugging the compiler with the token value 374\. In this calculation process, the parser will retrieve the yyN value from the yyTable (yyTable was created while generating the parser using Jay) array.

![330184/8_WatchValueofyyN_yyToken.jpg](http://www.codeproject.com/KB/cs/330184/8_WatchValueofyyN_yyToken.jpg)

Figure 8: yyN value in the watch list

The `yyN` value calculation is another interesting bit of work in the Mono compiler. So based on the given value yyState = 33, yyToken = 374, we get the value of 33th position of yyRinedx[33] which will be 450\. The current value of the yyN will be 450 and second part of the if((yyN += yyToken) >= 0) condition will add yyToken value with 450(current value of yyN) as yyN += yyToken.

Finally, the latest yyN value will be 824 (current token is 374 + previous yyN value which is 450). This 824 will be used as index to retrieve the value stored into the `yyTable` (`yyTable` created by the Compiler-Compiler Jay while converting the _cs-parser.jay_ to _cs-parser.cs_) in that position. And this value will be used as the new value of yyN which will be used as the switch case selector to execute the action block. I would like introduce here following arrays listed in the Code-Listing 5.23\. All these arrays have been created by the compiler-compiler Jay while converted grammar file into the parser.

Note: Array generated by the Jay for the Mono Parser.

Code-Listing 19: array generation by yacc

```
static short[] yyLhs
static short[] yyLen
static short[] yyDefRed
protected static short[] yyDgoto
protected static short[] yySindex
protected static short[] yyRindex
protected static short[] yyGindex
protected static short[] yyTable
protected static short[] yyCheck
```

In Figure 9, I have tried to show how `yyN` maps the `case` statement defined in the `yyparse` method to execute the related code block defined in the grammar, i.e., the _cs-parser.jay_ file.

![330184/9_StackTrace_Grammar_Token_Match.jpg](http://www.codeproject.com/KB/cs/330184/9_StackTrace_Grammar_Token_Match.jpg)

Figure 9: Token Mapping

We can see from Figure 10 how Mono constructs the abstract syntax tree while parsing the source code of a program. Each time the parser finds a valid token and a `yyN` value, it will match with the condition to run the related action block which adds the related type (based on the grammar specification, please see the Appendix for a full listing of grammar for the Mono C# compiler) into the `TypeContainer` which is later on used to resolve the Types.

![330184/10_GrammarMatchingWithToken.jpg](http://www.codeproject.com/KB/cs/330184/10_GrammarMatchingWithToken.jpg)

Figure 10: Watching

The `Compile()` method will call the `ResolveTree` method of the `RootContext` type from the _rootContext.cs_ file.

Code-Listing 20: ResolveTree method of RootContext

```
RootContext.ResolveTree ();
```

The `ResolveTree` method will generate the hierarchy tree or parse tree. And later on, the `Compile()` method calls `PopulateTypes` of the `RootContext` class. So far we have seen how Mono tokenizes the source code, parses the source code, and based on it how it constructs the Abstract Syntax Tree. In the next section, we will see how Mono generates Intermediate Language (IL) code to generate the assembly.

## []()References
