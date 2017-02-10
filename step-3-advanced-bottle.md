# Advanced Bottle

Welcome to the advanced section of the Python development with Bottle tutorial. In this section we will be taking a closer look at a variety of Bottle concepts including advanced routing techniques, server modification, and the Bottle Simple Template engine to name a few.

#### Routing

Bottle provides requests to function-call mapping with support for clean and dynamic URLs to prevent congestion on your web application. Routing is a complex process and Bottle seeks to simplify this complexity by adding clearly defined routing capabilities, wildcards, wildcard filters, and response objects to name a few. This section is dedicated to the routing capabilities Bottle has to offer.

To start our inspection of Bottles routing capabilities we first need to create a new file to store our routing endeavors. This time, unlike in our previous example, we are going to create the files we need within the code editor.

![](/assets/workspaceFilesFileCreation.png)

From your file tree as shown above, right click on your user name \(the highest level directory\) and select 'new folder'. Name this folder 'Templates' as it will be storing our HTML and CSS files that we will use to produce clean HTTP responses. You should also right click on your 'bottleLearning' file from Step 2: Learn Python with Bottle and create a file named bottleWildcards.py.

Once you've got the 'bottleWildcards.py' file made, go ahead and paste the following code into the file.

```
from bottle import route, run, template, static_file


@route('/<filename>')
def chooseFile(filename):
    return static_file(filename, root='/home/USR3OUAV/Templates/')

run(host='spaces.litmis.com', port=62368)
```

To break down the code above, we should begin our inspection with the '@route\(\)' decorator. The route decorator specifies the URL extension we will need to enter beyond our host and port address in order to make the defined \(def ...\) callback function. This code block is also noteable due to its use of a wildcard route. Wildcard routes, properly coined 'dynamic routes', match more than one URL at the same time and consist of a name enclosed in angle brackets. Dynamic or wildcard routes pass the covered part of the URL as a keyword argument to the rest of the callback function \(hence 'chooseFile\(filename\)'\). They support RESTful, visually appealing and syntaxically meaninful URLs right out of the box that is the Bottle module.

Beyond the routing concepts there is another construct readers may not yet be familiar with in the above code snippet called static\_file\(\). This method automatically guesses a media type, adds a last-modified header, restricts accessible paths to the root directory for security reasons and generates appropriate error responses.

The code above still needs to have the \(proper\) root variable assigned to tell Bottle where to look for the static file with the given filename. To do so, enter the command line interface and run the following commands to get a listing of your root directory. If you are using litmis spaces to follow along for this lab, it should be '/home/yourUserName'. Enter the value returned by the pwd \(print working directory\) command issued at the root of your file system and add the '/Templates/' extension to finish the root variable specification. It should look something like the example code snippet above once fully specified.

```
[usr3ouav@SPACES]~/bottleLearning% cd
[usr3ouav@SPACES]~% pwd
/home/USR3OUAV
```

To make proper use of the static\_file\(\) method, we need to create a file that it can render. Create a file in the 'Templates' folder within your root directory and name it 'wildcardSample.html'. This is what bottle will render for us when called to do so with the proper URL entry. Enter the following lines of HTML, or whatever HTML you would prefer to recieve as a response from our callback function, and save the file under the 'wildcardSample.html' file that was just created.

```
<!DOCTYPE HTML>

<html lang="en-US">
       <head>
              <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
              <title>Select wildcardSample.html for valid response</title>
       </head>
<style>
    body {background-color: powderblue;}

    p {
    font-family: verdana;
    font-size: 300%; 
    color: red;
    }
</style>
<body>
    <p>Woah, you wrote the code that displayed this!</p>
</body>
</html>
```

To run this code, jump over to your CLI \(command line interface\) and run the bottleWildcards.py file after you have saved it as we did earlier and as shown below:

```
[usr3ouav@SPACES]~% ls
New Folder           bottle_Files         cloud9_server.pid    htmlstyle_Files      mako_Files
Templates            bottle_jinja_cherry  core                 ibm_db_Files
bottleLearning       cherrypy_Files       django_Files         jinja2_Files
[usr3ouav@SPACES]~% cd bottleLearning
[usr3ouav@SPACES]~/bottleLearning% ls
bottleWildcards.py     firstBottleExample.py
[usr3ouav@SPACES]~/bottleLearning% python3 bottleWildcards.py
Bottle v0.12.13 server starting up (using WSGIRefServer())...
Listening on http://spaces.litmis.com:62368/
Hit Ctrl-C to quit.
```

After we have our server running, we need to use the correct routing configuration to get a proper response from our application.

![](/assets/wildcardRoutingStaticFileBottle.png)

As you can see, the added 'wildcardSample.html' wildcard entry is providing us a response as expected. Now, lets make things a little bit more interesting...

```
from bottle import route, run, template, static_file

# ... wildcard filter being used below
@route('/<filename:path>')
@route('/selectFile/<filename:path>')
def chooseFile(filename):
    return static_file(filename, root='/home/USR3OUAV/Templates/')

run(host='spaces.litmis.com', port=62368)
```

The above code illustrates a few new topics for us. The first noteable piece to take from this example is that one callback can have more than one route bound to it \(with the @route\(\) decorator\).

\*While creating the code for this section, I noticed that when adding multiple routes, you need to have the shorter route specified first, followed by any longer routes as shown above.

You might also have noticed that Bottle even allows us to add filters to our wildcards. It natively supports int, float, path and regex filters and even allows you to define and use your own filters. Above, a filter is used on the &lt;filename:path&gt; wildcard where we specify that the value entered into the wildcard extension must be a path, which matches all characters including the slash character and can be used to match more than one path segment.

Copy and paste the code snippet from above that includes the second routing decorator and the wildcard filter and restart your server. You should see no difference in the response you get, as we are still rendering the same HTML file, but you should be able to access a response from the server through both of the defined routes.

![](/assets/wildcardRoutingStaticFileBottle.png)

![](/assets/secondRouteBottleRouting.png)

Now that we have touched on some of the basic principles behind the routing mechanism of Bottle, its time to take a deeper look at HTTP request methods and their interaction with the Bottle routing notation.

The HTTP protocol defines several methods for different response tasks. In botttle, GET is the default for all routes with no other specified method. To handle other methods we need to add a method keyword to the route\(\) decorator or use one of the alternative decorators: get\(\), post\(\), put\(\), delete\(\), patch\(\).

Going along with our theme of topic seperation, create a new file in the 'bottleLearning' folder on your IFS and name it 'protocolMethods.py'. Within this file enter the following code block:

```
from bottle import run, get, post, request # or route

@get('/login') # or @route('/login')
def login():
    return '''
        <form action="/login" method="post">
            Username: <input name="username" type="text" />
            Password: <input name="password" type="password" />
            <input value="Login" type="submit" />
        </form>
    '''

@post('/login') # or @route('/login', method='POST')
def do_login():
    username = request.forms.get('username')
    password = request.forms.get('password')
    if check_login(username, password):
        return "<p>Your login information was correct.</p>"
    else:
        return "<p>Login failed.</p>"

run(host='spaces.litmis.com', port=62368)
```

This code block illustrates the different way you can use the routing mechanisms in Bottle. Notice the '\#' comment lines that define alternative route decorators for the non-commented versions provided. If you are unfamiliar with some of the syntax here, that is okay. What you should really be taking from this example is the use of the @get and @post route decorators and what they imply. In the @get block, we see an odd return value starting with tripple quotes, which is simply an HTML form embedded in our python code with the ''' and '''' tags surrounding it. This form takes a Username and Password as input, and when the input value is submitted, it generates the @post response.

Once the @get values are input, the @post request responds due to the 'method="post"' declaration in the HTML form. To retrieve the values from the form the 'request.forms.get\(\)' method is used with the appropriate input names provided as parameters. This finds the form values entered by the user and extracts them from the form.

Run the @get and @post code snippet and see if you can follow the flow of the application in action.  It should produce the following output:

![](/assets/bottleFormUseExample.png)

What response do you think you will recieve when fill in the form and submit it with the login button?

![](/assets/undefinedMethodResponseError.png)

We recieve this response because we never defined the check\_login\(username, password\) function! Lets change a few things and see if we can get a response.

```
from bottle import get, post, request, run # or route

@get('/login') # or @route('/login')
def login():
    return '''
        <form action="/login" method="post">
            Username: <input name="username" type="text" />
            Password: <input name="password" type="password" />
            <input value="Login" type="submit" />
        </form>
    '''

@post('/login') # or @route('/login', method='POST')
def do_login():
    username = request.forms.get('username')
    password = request.forms.get('password')
    if check_login(username, password):
        return "<p>Your login information was correct.</p>"
    else:
        return "<p>Login failed.</p>"

def check_login(user, pwrd):
    if user == 'Eric':
        return True
    else:
        return False

run(host='spaces.litmis.com', port=62368, reloader = True, debug = True)
```

The above code demonstrates a few things that I think are worth noting. For instance, this is our first example of a user defined method interacting with the methods within our given route decorators callback. The 'check\_login\(user, pwrd\)' function defnition will respond affirmatively whenever a user with the name of Eric enters the site, which in practice is likely only appreciated by Eric's \(me\), but does a good job illustrating the use of user defined methods within a callback.

However, this was not the only other change, I also added the 'reloader = True' and 'debug = True' lines in the run statement. The reloader enables our server to self deploy itself if serving a piece of code that we make changes to and then save over. This enhances the responsiveness of the application during application development and can save us a great deal of time during the error checking phase of our application launch. The setting the 'debug' variable to true makes Bottle much more verbose in response to error occurances. It also disables some optimizations that might get in your way during the development process and warns you about possible misconfigurations.

Running the above code will produce the following output if you enter 'Eric' as the username. Because of the form values provided, Google Chrome will even respond and ask you to save your login information for this site if your using that particular browser. Though I haven't tested them, I would assume other browsers respond in kind.

![](/assets/loginSuccessBottleExamples.png)

