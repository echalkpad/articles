# Learn How to Use Awk Built-in Variables – Part 10

[Original URL](http://www.tecmint.com/awk-built-in-variables-examples/)

> As we uncover the section of Awk features, in this part of the series, we shall walk through the concept of built-in variables in Awk. There are two types of variables you can use in Awk, these are;...

As we uncover the section of **Awk** features, in this part of the series, we shall walk through the concept of built-in variables in Awk. There are two types of variables you can use in Awk, these are; **user-defined** variables, which we covered in [Part 8](http://www.tecmint.com/learn-awk-variables-numeric-expressions-and-assignment-operators/) and **built-in** variables.

[![Awk Built in Variables Examples](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-Built-in-Variables-Examples.png)](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-Built-in-Variables-Examples.png) Awk Built in Variables Examples

**Built-in** variables have values already defined in **Awk**, but we can also carefully alter those values, the built-in variables include:

1. `FILENAME` : current input file name( do not change variable name)
2. `FR` : number of the current input line (that is input line 1, 2, 3... so on, do not change variable name)
3. `NF` : number of fields in current input line (do not change variable name)
4. `OFS` : output field separator
5. `FS` : input field separator
6. `ORS` : output record separator
7. `RS` : input record separator

Let us proceed to illustrate the use of some of the Awk built-in variables above:

To read the filename of the current input file, you can use the `FILENAME` built-in variable as follows:

```
$ awk ' { print FILENAME } ' ~/domains.txt 
```

[![Awk FILENAME Variable](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-FILENAME-Variable.png)](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-FILENAME-Variable.png) Awk FILENAME Variable

You will realize that, the filename is printed out for each input line, that is the default behavior of **Awk** when you use `FILENAME` built-in variable.

Using `NR` to count the number of lines (records) in an input file, remember that, it also counts the empty lines, as we shall see in the example below.

When we view the file **domains.txt** using [cat command](http://www.tecmint.com/13-basic-cat-command-examples-in-linux/), it contains **14** lines with text and empty **2** lines:

```
$ cat ~/domains.txt
```

[![Print Contents of File](http://www.tecmint.com/wp-content/uploads/2016/07/Print-Contents-of-File.png)](http://www.tecmint.com/wp-content/uploads/2016/07/Print-Contents-of-File.png) Print Contents of File

```
$ awk ' END { print "Number of records in file is: ", NR } ' ~/domains.txt 
```

[![Awk Count Number of Lines](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-Count-Number-of-Lines.png)](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-Count-Number-of-Lines.png) Awk Count Number of Lines

To count the number of fields in a record or line, we use the NR built-in variable as follows:

```
$ cat ~/names.txt
```

[![List File Contents](http://www.tecmint.com/wp-content/uploads/2016/07/List-File-Contents.png)](http://www.tecmint.com/wp-content/uploads/2016/07/List-File-Contents.png) List File Contents

```
$ awk '{ print "Record:",NR,"has",NF,"fields" ; }' ~/names.txt
```

[![Awk Count Number of Fields in File](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-Count-Number-of-Fields-in-File.png)](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-Count-Number-of-Fields-in-File.png) Awk Count Number of Fields in File

Next, you can also specify an input field separator using the `FS` built-in variable, it defines how **Awk** divides input lines into fields.

The default value for `FS` is **space** and **tab**, but we can change the value of `FS` to any character that will instruct Awk to divide input lines accordingly.

There are two methods to do this:

1. one method is to use the **FS** built-in variable
2. and the second is to invoke the **-F** Awk option

Consider the file **/etc/passwd** on a Linux system, the fields in this file are divided using the `:` character, so we can specify it as the new input field separator when we want to filter out certain fields as in the following examples:

We can use the `-F` option as follows:

```
$ awk -F':' '{ print $1, $4 ;}' /etc/passwd
```

[![Awk Filter Fields in Password File](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-Filter-Fields-in-Password-File.png)](http://www.tecmint.com/wp-content/uploads/2016/07/Awk-Filter-Fields-in-Password-File.png) Awk Filter Fields in Password File

Optionally, we can also take advantage of the `FS` built-in variable as below:

```
$ awk ' BEGIN { FS=“:” ; } { print $1, $4 ; } ' /etc/passwd
```

[![Filter Fields in File Using Awk](http://www.tecmint.com/wp-content/uploads/2016/07/Filter-Fields-in-File-Using-Awk.png)](http://www.tecmint.com/wp-content/uploads/2016/07/Filter-Fields-in-File-Using-Awk.png) Filter Fields in File Using Awk

To specify an output field separator, use the `OFS` built-in variable, it defines how the output fields will be separated using the character we use as in the example below:

```
$ awk -F':' ' BEGIN { OFS="==>" ;} { print $1, $4 ;}' /etc/passwd
```

[![Add Separator to Field in File](http://www.tecmint.com/wp-content/uploads/2016/07/Add-Separator-to-Field-in-File.png)](http://www.tecmint.com/wp-content/uploads/2016/07/Add-Separator-to-Field-in-File.png) Add Separator to Field in File

In this **Part 10**, we have explored the idea of using Awk built-in variables which come with predefined values. But we can also change these values, though, it is not recommended to do so unless you know what you are doing, with adequate understanding.

After this, we shall progress to cover how we can use shell variables in Awk command operations, therefore, stay connected to **Tecmint**.
