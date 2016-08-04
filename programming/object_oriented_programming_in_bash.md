# Object Oriented Programming in Bash

[Original URL](http://hipersayanx.blogspot.sk/2012/12/object-oriented-programming-in-bash.html)

> Bash is a very common *nix shell, and it's programming language is purely procedural and focused on command execution. Object Oriented Programming (OOP) is a programming paradigm that represents the...

Bash is a very common *nix shell, and it's programming language is purely procedural and focused on command execution. Object Oriented Programming (OOP) is a programming paradigm that represents the elements of a problem as entities with a set of properties and actions that it can execute. If you use Bash to write very simple and short scripts, procedural programming is just fine, you don't need more. But if your program becomes more and more bigger, a monster program (> 1000 lines), then you need a better way to structure your program to make it easy to maintain and read. Of course, Bash lacks OOP features, but with some tricks you can simulate it with a few lines added, and I'll show you how.<br>
[![](https://lh3.ggpht.com/-K7muL7uw4h8/UNXz7hCaU4I/AAAAAAAAAlU/pqYTXXL_7MM/s320/oop.png)](http://commons.wikimedia.org/wiki/File:Analysis_Model_Objects.jpg)

[]() **ATTENTION**: You need to have solid concepts of OOP and Bash before reading it, I will not explain nothing of that here.

First at all create an script in which you will define your class, and give it execution permissions.<br>
touch vector.sh chmod +x vector.sh

Then copy the following code inside the script:

```
#!/bin/bash

# Base class. (1)
function Vector()
{
 # A pointer to this Class. (2)
 base=$FUNCNAME
 this=$1

 # Inherited classes (optional). (3)
 export ${this}_inherits="Class1 Class2 Class3" # (3.1)

 for class in $(eval "echo \$${this}_inherits")
 do
 for property in $(compgen -A variable ${class}_)
 do
 export ${property/#$class\_/$this\_}="${property}" # (3.2)
 done

 for method in $(compgen -A function ${class}_)
 do
 export ${method/#$class\_/$this\_}="${method} ${this}"
 done
 done

 # Declare Properties. (4)
 export ${this}_x=$2
 export ${this}_y=$3
 export ${this}_z=$4

 # Declare methods. (5)
 for method in $(compgen -A function)
 do
 export ${method/#$base\_/$this\_}="${method} ${this}"
 done
}

# Human readable representation of the vector. (6)
function Vector_show()
{
 # (7)
 base=$(expr "$FUNCNAME" : '\([a-zA-Z][a-zA-Z0-9]*\)')
 this=$1

 x=$(eval "echo \$${this}_x")
 y=$(eval "echo \$${this}_y")
 z=$(eval "echo \$${this}_z")

 echo "$this ($x, $y, $z)"
}

# Adds two vectors.
function Vector_add()
{
 base=$(expr "$FUNCNAME" : '\([a-zA-Z][a-zA-Z0-9]*\)')
 this=$1
 other=$2

 # Get it's components
 x1=$(eval "echo \$${this}_x")
 y1=$(eval "echo \$${this}_y")
 z1=$(eval "echo \$${this}_z")

 x2=$(eval "echo \$${other}_x")
 y2=$(eval "echo \$${other}_y")
 z2=$(eval "echo \$${other}_z")

 # Add it's components
 x=$(($x1 + $x2))
 y=$(($y1 + $y2))
 z=$(($z1 + $z2))

 # Create a new vector. (8)
 Vector 'vector3' $x $y $z

 $vector3_show
}
```

1. This is function is an equivalent of the constructor of a class. It must have the same name of the class. The name of a class can't contains underscores (**_**). We will use this function to create instances of the _"Vector"_ class.
2. Here you can get a reference of the base class of the object and a reference of the object created. The first argument of the constructor must be always the name of the object to create.
3. This block of code simulates multiple inheritance, is optional and you can remove it if you don't need.

  1. Every property and method of the class can be accessed as _${this}_methodProperty_name_, or you can also call to it's base class as _${base}_methodProperty_name_. Multiple inheritance works overwriting the methods and properties (m&p) of the preceding classes as follows:

    ```
    Class1 < Class2 < Class3 < Base
    ```

    - _Class2_ overwrites m&p of _Class1_.
    - _Class3_ overwrites m&p of _Class1_ and _Class2_.
    - _Base_ overwrites m&p of _Class1_ and _Class2_ and _Class3_.

  2. This will export the m&p of the base class as a m&p of the object. See bellow.

4. Every method and property of the class is exported as a global variable in the form of _objectName_methodProperty_name_. You can initialize it's properties through the constructor's parameters, or with a default value, or leave it uninitialized.

5. This will find every function with the pathern _BaseClass_** and export it as a global variable replacing_ BaseClass* with the object's name, the value of the variable will be a string with the base class method and the object name as first parameter that serves as a reference to it.
6. The methods of the class are defined as _BaseClass_methodName_. Method names can contains underscores, but camelCase is recommended.
7. Obtains a reference to the base class (not obligatory) and the object, remember, the first argument is the name of the object.
8. Of course, you can create new objects inside the methods of the same class.

We are ready to use our class. Create an script from which you will use your class, give it execution permissions, and copy the following code inside it:

```
#!/bin/bash

# Import the class definition file.
. vector.sh

function main()
{
 # Create the vectors objects. (1)
 Vector 'vector1' 1 2 3
 Vector 'vector2' 7 5 3

 # Show it's properties. (2)
 echo "vector1 ($vector1_x, $vector1_y, $vector1_z)"
 echo "vector2 ($vector2_x, $vector2_y, $vector2_z)"

 # Call to it's methods.
 $vector1_show
 $vector2_show

 $vector1_add vector2
}

main
```

1. Objects are declared as _Class objectName [arg1 arg2 arg3 ...]_.
2. Now you can access to it's methods and properties as _$objectName_methodsProperty_name [arg1 arg2 arg3 ...]_.

The result of the script execution is:<br>
$ ./main.sh vector1 (1, 2, 3) vector2 (7, 5, 3) vector1 (1, 2, 3) vector2 (7, 5, 3) vector3 (8, 7, 6)

OOP in Bash unlocked. Achievement reached :D
