# Learning Python with Bottle

While using the interpreter can open up a range of resources for Python development and learning the general aspects of the language, some would argue that jumping right in and making use of one of the many public libraries avaliable to Python users doos a better job illustrating the power of the language. [Bottle](https://bottlepy.org/docs/dev/ "See the Bottle documentation") is a fast, simple and lightweight WSGI micro web-framework for Python distributed as a single file module with no dependiencies other than the Python Standard Library, making it a perfect candidate to illustrate the power behind Python and why it's inclusion into the PASE environment is so monumental.

To get started, enter the shell interface from the workspaces page of your litmis space. Submit the following 'pip' command and review the response, it should look something like this:

```
[usr3ouav@SPACES]~% pip3 install bottle
Collecting bottle
Installing collected packages: bottle
Successfully installed bottle-0.12.13
[usr3ouav@SPACES]~% 
```

If your unsure of the installation enter the pip3 list command into your shell to view your installed Python modules \(beyond the Python Standard Library\).

```
[usr3ouav@SPACES]~% pip3 list
DEPRECATION: The default format will switch to columns in the future. You can use --format=(legacy|columns) (or define a format=(legacy|columns) in your pip.conf under the [list] section) to disable this warning.
appdirs (1.4.0)
bottle (0.12.13)
CherryPy (8.9.1)
Django (1.10.5)
django-ipware (1.1.6)
ibm-db (2.0.5.4)
Jinja2 (2.9.4)
MarkupSafe (0.23)
nltk (3.2.2)
packaging (16.8)
pip (9.0.1)
pyparsing (2.1.10)
pytz (2016.10)
setuptools (34.0.2)
simplejson (3.10.0)
six (1.10.0)
textblob (0.11.1)
wheel (0.29.0)
yahoo-finance (1.4.0)
[usr3ouav@SPACES]~% 
```

To set up our bottle project we can take a number of routes. We could either enter a Python interpreter and run a short segment of code to see it in action, or we could put this short piece of code in a file and execute the file through the shell interface. Furthermore, we can either go to the code editor to create the files we will need, or we can do so through the shell. First, I will illustrate how we can create the file we need to run with shell commands, and then I'll demonstrate an alternative via the code editor.

```
[usr3ouav@SPACES]~% ls
Templates            bottle_jinja_cherry  cloud9_server.pid    django_Files         ibm_db_Files         mako_Files
bottle_Files         cherrypy_Files       core                 htmlstyle_Files      jinja2_Files
[usr3ouav@SPACES]~% mkdir bottleLearning
[usr3ouav@SPACES]~% ls
Templates            bottle_Files         cherrypy_Files       core                 htmlstyle_Files      jinja2_Files
bottleLearning       bottle_jinja_cherry  cloud9_server.pid    django_Files         ibm_db_Files         mako_Files
[usr3ouav@SPACES]~% cd bottleLearning
[usr3ouav@SPACES]~/bottleLearning% ls
[usr3ouav@SPACES]~/bottleLearning% touch firstBottleExample.py
[usr3ouav@SPACES]~/bottleLearning% ls
firstBottleExample.py
[usr3ouav@SPACES]~/bottleLearning% 
```

To break down the above we need to assess what all of the shell commands were. First, 'ls' was entered to list the contents of the current working directory. The next command, 'mkdir', created a new directory inside the current directory \(which should be the home directory on startup\). The second 'ls' command was sent to illustrate the completion of the 'mkdir' command \(notice the 'bottleLearning' listing under this command\). Once the new directory was created, the 'cd' command was issued to change the working directory to the newly created 'bottleLearning' directory. From that point the contents were listed using the 'ls' command, which displayed nothing, and at which point the file we came to create was finally produced with the 'touch firstBottleExample.py' command.

At this point you can enter the code editor through the litmis spaces homepage:

![](/assets/codeEditor.png)

Your code editor should look something like this:

![](/assets/codeEditorScreen.png)

You might not be able to see the file we supposedly created in the shell prompt right away. To view this file you may need to refresh your workstation by right clicking on your username and selecting refresh as shown below:

![](/assets/refreshCodeEditor.png)

Now that your workstation is refreshed you can go ahead and begin editing the file we created by selecting the down arrow on the 'bottleLearning' folder that contains our desired file and double clicking on the file to pull it up in the editor.

![](/assets/selectingFileInEditor.png)

From here we are ready to begin editing our file. Enter the following lines of code into your 'firstBottleExample.py' file:

```
from bottle import route, run, template

@route('/hello/<name>')
def index(name):
    return template('<b>Hello {{name}}</b>!', name=name)

run(host='localhost', port=8080)
```

These lines represent one of the most basic bottle structures you can engineer, and when run, should start the built in Bottle server on the given host at the provided port number that serves HTTP GET requests with the appropriate routing as defined in the '@route' attribute parameters. However, for our purposes we are going to need to update the host and port parameters in the run command with our Litmis Space information. To find this information navigate to the Litmis Spaces workspaces page and select the 'More Info' icon.

![](/assets/informationIcon.png)

You are going to need the following host and port information from this section:

![](/assets/hostAndPortInfo.png)

Based on the information from my own info page my finished 'firstBottleExample.py' code would look like this:

```
from bottle import route, run, template

@route('/hello/<name>')
def index(name):
    return template('<b>Hello {{name}}</b>!', name=name)

run(host='spaces.litmis.com', port=62368)
```

Make sure you save the 'firstBottleExample.py' file by selecting 'File' and 'Save' from your code editor or simply use the 'ctrl-s' hotkey to update the IFS \(Integrated File System\) for your space. If you haven't saved your file since your last edit the code editor interface will display an '\*' symbol next to the name of your file at the top of your display. After saving, we are now ready to execute this code and start hosting our application at the given host and port address. Head over to the shell interface and enter the following to execute your 'firstBottleExample.py' code:

```
[usr3ouav@SPACES]~% ls
Templates            bottle_Files         cherrypy_Files       core                 htmlstyle_Files      jinja2_Files
bottleLearning       bottle_jinja_cherry  cloud9_server.pid    django_Files         ibm_db_Files         mako_Files
[usr3ouav@SPACES]~% cd bottleLearning
[usr3ouav@SPACES]~/bottleLearning% ls
firstBottleExample.py
[usr3ouav@SPACES]~/bottleLearning% python3 firstBottleExample.py
Bottle v0.12.13 server starting up (using WSGIRefServer())...
Listening on http://spaces.litmis.com:62368/
Hit Ctrl-C to quit.
```

Restarting the console resets your directory setting so using an 'ls' command to determine your wherabouts in the IFS is a good way to start any shell entry. Using the 'cd' command to change directories to the 'bottleLearning' directory and using 'ls' to list it's contents is a way to display a text readout of the filename we are trying to use so that we can avoid any syntax errors in our execute statement. Once in the correct directory you can then issue the 'python3 firstBottleExample.py' command as shown to run the interpreter and execute the requested file. This runs the code we saved in the 'firstBottleExample.py' file starting a server at the requested host and port number as outlined in the file which produces the output shown. Copy and paste the URL listed at the 'Listening on http://...' line into a browser to view the servers response based on our code entry!

If your unfamiliar with the routing we defined, here are some examples of valid routes this code will accept:

![](/assets/routingInBrowserExample.png)

![](/assets/firefoxRoutingExampleWithArrows.png)

Simply enter the host name, port address and the proper routing configuration and you should see a response in a browser based on code that you just entered! Congratulations on your fist webpage using Python's Bottle module! Move on to Step 3 for more advanced Bottle topics and examples or skip ahead to a different topic if you so choose to!

