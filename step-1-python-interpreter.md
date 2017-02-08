# Using the Python Interpreter

##### Python Interpreter \(Shell Commands\) {#python-interpreter-shell-commands}

It should first be noted that Python utilizes an interpreter to render it's code into machine readable instructions, not a compiler. The interpreter reads in a program line by line, executing each line in order, unlike a compiler that translates the contents of the program before execution. I thought it useful to take a moment and explain the difference between the two translators as the rest of this section utilizes the Python interpreter to run simple Python commands, which can be confusing to individuals accustomed to a compiler for their code of choice.

The Python interpreter allows you to run small pieces of code in the shell and to immediately see the results. To enter the Python interpreter simply type 'python' at which point you should be brought to a new line in the shell with a &gt;&gt;&gt; at the start of the line to indicate that you are in the Python interpreter \(the '&gt;&gt;&gt;' indicator can even be personalized!\). To exit simply type 'exit\(\)' or 'ctrl+c / cmd+d'.

Below, you can jump right into a brief introduction on the Python interpreter if you are so inclined to do so! If your comfortable with Python at this point, you might find it more useful to jump ahead to Step 2 and use this Step as well as the Python Overview as references for anything you might find unfamiliar with moving forward. 

### Getting Started

To enter the Python interpreter you simply need to enter "python3" \(to use the Python 3.\* distribution\). From there, as stated above, you can enter individual lines of valid Python code that the interpreter will then make sense of in real time, allowing you to try out single-line commands in a stress free environment and get the results from those commands immediately.

![](/assets/startingPythonInterpreter.PNG)

Above you see the shell command 'python3 --version' that behaves predictably, listing our current working version of Python. You can use this with 'python --version' or any other module to determine what version your system currently has in place.

```
 ![](/assets/PythonInterpreter1.PNG)
```

The 'python3' entry into the shell provided some details regarding our current distribution of Python \(similar but more in depth than the --version commands return\) and also started the Python interpreter. Below that you can see some simple arithmetic being handled by the interpreter as well as an in-line comment signaled by the '\#' sign.

![](/assets/Strings and commentsPythonInterpreter.PNG)

Again going off the picture above, you can see one of the limitations to the comment sign indicator that should be noted. Place comments outside quotations to ensure their reliability.

![](/assets/errorsMultiLineCommentsPythonInterpreter.PNG)

The first thing you see me do in this example is access an undefined variable. Attempting to do so should return the NameError response. Next, I set a boolean value and I then use that in a multi-lined if statement. Not only does this snippet provide useful information about the if statment syntax, but this also illsutrates the white space requirements of Python. The command within the if statement is given four leading spaces to identify it as a part of the if statement.

![](/assets/FibonacciSeriesPythonInterpreterIntro.PNG)

Moving on to more complicated topics, we have the Fibonacci Series defined above. This particular algorithm implemented in this way illustrates the use of dual assignment on a single liine. The while statement in the code above is similar to the if statement used previously in that they both must be delineated with white space instead of brackets or other mechanisms popular language alternatives embrace.

![](/assets/definedFunctionFibonacciSeriesPythonIntro.PNG)

Bringing the Fibonacci Series algorithm into the future by defining it as a function with a single input instead of a single while loop. This differs from the previous implementation as the new version now enables us to call the code an indefinate number of times throughout our program. You might be able to envision the power of such a construct and if you have any experience coding in another language, you probably have an immense appreciation for the power this provides developers.

![](/assets/advancedPythonInterpreterUse.PNG)

One of my final examples of the Python interpreter goes a little bit more in depth than my previous examples and makes use of pip and it's import capabilities. In the first line of this example I am using pip \(PyPa recommended tool for installing Python packages\) to get the yahoo\_finance library and install it into my environment. As you can see, my environment already satisfies the requirements for this installation so pip didn't do much, though it's worth noting that there are requirements and in some cases you may need to install these independently to get packages to work so pay attention to any error warning returned by the 'pip3 install' command as they might contain useful tips to help you solve any discrepancies existing between your environment and the requirements of the desired installation.

Moving into the interpreter, you can see that I used both 'import yahoofinance' and 'from yahoofinance import \*' to inform the interpreter that I would be using commands from that module. However, you will not need to run both of these commands. In fact, it is not recommended to do so! By simply importing all of a module you could accidentaly overwrite something in a different imported module that you did not wish to in your runtime environment. You should specify the methods that you plan to make use of from the import. For example, to improve my code above one would go with 'from yahoo\_finance import Share' instead of the lines mentioned previously.

Within the interpreter you then see me utilize the yahoo\_finance library to fetch a Company Name and Price from the web and utilize it directly in the application. Pretty cool huh? There are many robust libraries written for Python that are freely distributed making Python an extremely powerful and developer friendly language. This also means that the more people that use Python, the more tools you will have access too. Hopefully at this point some of the questions you had regarding Python have been answered. On the other hand, if your still unsure about what is happening in any of the above examples or just want more information about Python and how it works, meandering to the online Python documentation [here](https://docs.python.org/3.6/contents.html "Python Documentation") would be my suggestion. You can also check out the Python References section for more information!

