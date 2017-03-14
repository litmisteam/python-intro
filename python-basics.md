# Python Basics

## \(If you're familiar with Python, feel free to skip this section!\)

CodeEval, an exclusive community of over 69,000 competitive developers, has assessed Python as the "most popular coding language" for the past FIVE years \(last ranking - 2016\).

With such a thriving community, it's hard to come up with a reason not to get on the Python bandwagon. Its recent integration to the IBM i platform furthers the existing incentive, meaning it's never been a better time to read ahead and begin your Python journey!

#### Overview

Python is a high-level scripting language with object oriented features. Python offers more structure and support for large programs than shell scripts or batch files can offer while remaining a simple to use programming language. In fact, with high-level data types built in \(flexible arrays and dictionaries\) Python is actually considered a very-high-level-language. Allowing you to split your program into modules that can be reused in other Python programs greatly expands the language's inherent capabilities. Things like file I/O, system calls, sockets and even interfaces to graphical user interface toolkits can be modularized to reduce development time. Furthermore, Python is both an interpreted language and extensible. Being an interpreted language means that no compilation and linking is necessary making it easier to experiment with features of the language. Extensibility makes it easy to add a new built-in function or module to the interpreter on an existing program and allows us to link the Python interpreter to an application written in C to use as an extension or command language for that application. Python places a strong emphasis on readability and efficiency and can be picked up easily by Java, C, Perl and other language converts.

#### Python Oddities

Before we move to the actual tutorial I thought it might be useful to list some of the language protocols that might seem foreign to new users \(as they did to me\).

##### Indentation

Python uses indentation for blocks, instead of curly braces. Both tabs and spaces are supported, but the [standard style guide](https://www.python.org/dev/peps/pep-0008/) recommends the use of four spaces per indentation level.

```
x = 1
if x == 1:
    # indented four spaces
    print("x is 1.")
```

The above code block illustrates the use of spaces in flow-control statements such as the 'if' statement. The bottom line here is that Python recognizes spacing similar to how HTML does and misplaced or misused spaces can be fatal to program execution.

You might also note that the above code segment illustrates the lack of termination characters \(such as ';' in Java or C\).

##### Variable Assignment

Variable definition is fairly straightforward in Python. Since it is a scripting language you can assign and reassign a variable as many times as you would like in your program. Simple operators can be executed on numbers and strings as you might expect, and Python also supports simultaneous variable assignment.

```
a, b = 3, 4
print(a, b)
```

The above code would print 3, 4 as both of the variables were assigned at once.

Furthermore, variables are dynamically typed, meaning that Python has no type declarations and instead infers them itself. Python is strongly typed however, making variable assignment and modification an important aspect of code to still be aware of even without the direct type declarations.

##### Flow Control

The last odd Python syntax we will cover is the use of an 'else' clause following a loop. If the loop executes to completion, the 'else' block will be executed. However, if the loop is exited prematurely, the 'else' block will be skipped.

```
count = 0
target = 8
while count < 10:
    print(count)
    if count == target:
        print('Target found!')
        break
    count += 1
else:
    print('Target not found.')
```

## Please proceed to the next step. {#_please_proceed_to_the_next_step}



