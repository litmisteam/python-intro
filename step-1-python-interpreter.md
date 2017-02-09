# Using the Python Interpreter

##### Python Interpreter \(Shell Commands\) {#python-interpreter-shell-commands}

It should first be noted that Python utilizes an interpreter to render it's code into machine readable instructions, not a compiler. The interpreter reads in a program line by line, executing each line in order, unlike a compiler that translates the contents of the program before execution. I thought it useful to take a moment and explain the difference between the two translators as the rest of this section utilizes the Python interpreter to run simple Python commands, which can be confusing to individuals accustomed to a compiler for their code of choice.

The Python interpreter allows you to run small pieces of code in the shell and to immediately see the results. To enter the Python interpreter simply type 'python' at which point you should be brought to a new line in the shell with a &gt;&gt;&gt; at the start of the line to indicate that you are in the Python interpreter \(the '&gt;&gt;&gt;' indicator can even be personalized!\). To exit simply type 'exit\(\)' or 'ctrl+c / cmd+d'.

Below, you can jump right into a brief introduction on the Python interpreter if you are so inclined to do so! If your comfortable with Python at this point, you might find it more useful to jump ahead to Step 2 and use this Step as well as the Python Overview as references for anything you might find unfamiliar with moving forward.

### Getting Started

To enter the Python interpreter you simply need to enter "python3" \(to use the Python 3.\* distribution\). From there, as stated above, you can enter individual lines of valid Python code that the interpreter will then make sense of in real time, allowing you to try out single-line commands in a stress free environment and get the results from those commands immediately.

```
[usr3ouav@SPACES]~% python3 --version
Python 3.4.4
[usr3ouav@SPACES]~% python3
Python 3.4.4 (default, Mar 23 2016, 11:07:11) 
[GCC 4.8.4] on aix6
Type "help", "copyright", "credits" or "license" for more information.
>>> 2 + 2
4
```

Above you see the shell command 'python3 --version' that behaves predictably, listing our current working version of Python. You can use this with 'python --version' or any other module to determine what version your system currently has in place.

The 'python3' entry into the shell provided some details regarding our current distribution of Python \(similar but more in depth than the --version commands return\) and also started the Python interpreter. Below that you can see some simple arithmetic being handled by the interpreter as well as an in-line comment signaled by the '\#' sign.

```
>>> # Anything following the '#' sign on the same line will be a comment
... # the '...' indicator at the beggining of the line indicates a multi-line entry
... not_a_comment = '# this is not a comment because it is inside quotations'                  
>>> print(not_a_comment)
# this is not a comment because it is inside quotations
```

Again going off the picture above, you can see one of the limitations to the comment sign indicator that should be noted. Place comments outside quotations to ensure their reliability.

```
>>> somevariable                                 
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'somevariable' is not defined
>>> # Python does not allow access to undefined variables
... the_packers_rock = True
>>> if the_packers_rock:
...     print('Aaron Rodgers is their quarterback.')
... 
Aaron Rodgers is their quarterback.
>>> # Multi-line constructs require continuation lines (...).
... # Whitespace matters in Python so the tab space in front of the 'print' command above is required! 
```

The first thing you see me do in this example is access an undefined variable. Attempting to do so should return the NameError response. Next, I set a boolean value and I then use that in a multi-lined if statement. Not only does this snippet provide useful information about the if statment syntax, but this also illsutrates the white space requirements of Python. The command within the if statement is given four leading spaces to identify it as a part of the if statement.

```
>>> # Fibonacci Series:
... # The sum of two elements defines the next.
... a, b = 0, 1 # Python supports dual assignment!                
>>> while b < 10:
...     print(b)
...     a, b = b, a + b
... 
1
1
2
3
5
8
```

Moving on to more complicated topics, we have the Fibonacci Series defined above. This particular algorithm implemented in this way illustrates the use of dual assignment on a single liine. The while statement in the code above is similar to the if statement used previously in that they both must be delineated with white space instead of brackets or other mechanisms popular language alternatives embrace.

```
>>> def fib(n):
...     # fib(n) will print a Fibonacci series up to n.
...     a, b = 0, 1
...     while a < n:
...             print(a, end=' ')
...             a, b = b, a+b
...     print()
... 
>>> # Now call the function defined above:
... fib(2000)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597 
>>> # The 'end' modifier inside the print statement in the while loop modified the output format
```

Bringing the Fibonacci Series algorithm into the future by defining it as a function with a single input instead of a single while loop. This differs from the previous implementation as the new version now enables us to call the code an indefinate number of times throughout our program. You might be able to envision the power of such a construct and if you have any experience coding in another language, you probably have an immense appreciation for the power this provides developers.

```
[usr3ouav@SPACES]~% pip3 install yahoo_finance
Requirement already satisfied: yahoo_finance in /QOpenSys/QIBM/ProdData/OPS/Python3.4/lib/python3.4/site-packages
Requirement already satisfied: simplejson in /QOpenSys/QIBM/ProdData/OPS/Python3.4/lib/python3.4/site-packages (from yahoo_finance)
Requirement already satisfied: pytz in /QOpenSys/QIBM/ProdData/OPS/Python3.4/lib/python3.4/site-packages (from yahoo_finance)
[usr3ouav@SPACES]~% python3
Python 3.4.4 (default, Mar 23 2016, 11:07:11) 
[GCC 4.8.4] on aix6
Type "help", "copyright", "credits" or "license" for more information.
>>> import yahoo_finance
>>> from yahoo_finance import *
>>> from yahoo_finance import Share
>>> # The above import statements should all accomplish what we need. To avoid 
... # overwriting something unintentionally by importing * (all), we should 
... # specify the import as in the third example.        
... ticker = 'IBM'
>>> shareObject = Share(ticker)
>>> companyName = shareObject.get_name()
>>> stockPrice = shareObject.get_price()
>>> print('{} has a stock price of ${}.'.format(companyName, stockPrice))
International Business Machines has a stock price of $176.17.
>>> exit()
```

One of my final examples of the Python interpreter goes a little bit more in depth than my previous examples and makes use of pip and it's import capabilities. In the first line of this example I am using pip \(PyPa recommended tool for installing Python packages\) to get the yahoo\_finance library and install it into my environment. As you can see, my environment already satisfies the requirements for this installation so pip didn't do much, though it's worth noting that many packages have requirements and in some cases you may need to install these independently to get packages to work so pay attention to any error warning returned by the 'pip3 install' command as they might contain useful tips to help you solve any discrepancies existing between your environment and the requirements of the desired installation. 

Moving into the interpreter, you can see that I used both 'import yahoofinance' and 'from yahoofinance import \*' to inform the interpreter that I would be using commands from that module. However, you will not need to run both of these commands. In fact, it is not recommended to do so! By simply importing all of a module you could accidentaly overwrite something in a different imported module that you did not wish to in your runtime environment. You should specify the methods that you plan to make use of from the import. For example, to improve my code above one would go with 'from yahoo\_finance import Share' instead of the lines mentioned previously.

Within the interpreter you then see me utilize the yahoo\_finance library to fetch a Company Name and Price from the web and utilize it directly in the application. Pretty cool huh? There are many robust libraries written for Python that are freely distributed making Python an extremely powerful and developer friendly language. This also means that the more people that use Python, the more tools you will have access too. Hopefully at this point some of the questions you had regarding Python have been answered. On the other hand, if your still unsure about what is happening in any of the above examples or just want more information about Python and how it works, meandering to the online Python documentation [here](https://docs.python.org/3.6/contents.html "Python Documentation") would be my suggestion. You can also check out the Python References section for more information!

To view a more in depth list of 'pip' commands simply enter 'pip3' into the command prompt to display an output as shown:

```
[usr3ouav@SPACES]~% pip3 

Usage:   
  pip <command> [options]

Commands:
  install                     Install packages.
  download                    Download packages.
  uninstall                   Uninstall packages.
  freeze                      Output installed packages in requirements format.
  list                        List installed packages.
  show                        Show information about installed packages.
  check                       Verify installed packages have compatible dependencies.
  search                      Search PyPI for packages.
  wheel                       Build wheels from your requirements.
  hash                        Compute hashes of package archives.
  completion                  A helper command used for command completion.
  help                        Show help for commands.

General Options:
  -h, --help                  Show help.
  --isolated                  Run pip in an isolated mode, ignoring environment variables and user configuration.
  -v, --verbose               Give more output. Option is additive, and can be used up to 3 times.
  -V, --version               Show version and exit.
  -q, --quiet                 Give less output. Option is additive, and can be used up to 3 times (corresponding to WARNING, ERROR, and CRITICAL
                              logging levels).
  --log <path>                Path to a verbose appending log.
  --proxy <proxy>             Specify a proxy in the form [user:passwd@]proxy.server:port.
  --retries <retries>         Maximum number of retries each connection should attempt (default 5 times).
  --timeout <sec>             Set the socket timeout (default 15 seconds).
  --exists-action <action>    Default action when a path already exists: (s)witch, (i)gnore, (w)ipe, (b)ackup, (a)bort.
  --trusted-host <hostname>   Mark this host as trusted, even though it does not have valid or any HTTPS.
  --cert <path>               Path to alternate CA bundle.
  --client-cert <path>        Path to SSL client certificate, a single file containing the private key and the certificate in PEM format.
  --cache-dir <dir>           Store the cache data in <dir>.
  --no-cache-dir              Disable the cache.
  --disable-pip-version-check
                              Don't periodically check PyPI to determine whether a new version of pip is available for download. Implied with
                              --no-index.

```



