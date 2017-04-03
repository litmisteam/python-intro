# Simple Template and Routing Cont.

The goal of Step 4 in this tutorial is to introduce the audience to much more complex Bottle topics while also digging deeper into concepts introduced in previous steps. We will focus on both more in depth template usage as well as interactions with the Bottle template interface while working with the yahoo-finance module to produce a ticker lookup system that returns information about a company associated with a valid user entered ticker string. While this is not meant to make you an expert on template usage, it may help incoming or perspective Bottle/Litmis Space users better understand the core functionality of the API and how to implement it.

#### Templates

In a way, this section is a necessary continuation of Step 2's introduction to the Bottle template engine \(SimpleTemplate\). Here though, we are going to go further in depth and make use of form data as well as rendering parameters for the templates. First, we need to create a file that represents the entry point for our web application. This file will contain the contents of the first page that any incoming user to our site is directed to. For this sections example application, we are going to build off the yahoo-finance library that we encountered briefly in Step 3. I will also be using the litmis space that we set up in the IBM i Access section throughout this section of the tutorial. Copy and paste the following '.html' file to your IFS with a filename of your choosing. If you are still struggling to understand the Integrated File System \(IFS\) you can simply create a new file titled 'lookupStock.html' in the root directory of your workspace filesystem and copy the following code to that file.

```
<html>
<style>
    html{background-color: powderblue;}
    button{top-margin:5px; margin:auto; display:block}
    .spacer{height: 10px;}
    .smallSpacer{height:2px;}
    table{border: 1px solid black;margin-top:10%}
    input{width:100%; border: 1 solid #838383}
</style>  
<head>
    <title>Search Stocks</title>
</head>
    <form action="/search" method="POST">
    <table align="center"; width="30%"; >
        <caption align="center">Display Stock Information:</caption>
        <tr>
            <td width="30%">Company Ticker:</td>
            <td width="70%"> <input type="text" name="ticker"></td>
        </tr>
    </table>
    <div class="smallSpacer"></div> 
    <button align="center"; type="submit">Search</button>
    </form>  
    <form action="/close" method="POST">
        <div class="smallSpacer"></div> 
        <button style="background-color: red"; align="center"; type="submit">Close</button>
    </form>
</html>
```

Reiterating a previous point, the above code is not supposed to be showing flawless HTML or CSS usage. What I am trying to show is the use of the values submitted from the above form by our Bottle api. When this form is rendered, the user has the option to enter a ticker value and select the 'Search' button, or hit the 'Close' button. Each of these options has a different form 'action' parameter. The form line is used to signify both the type of HTTP request we will be performing as well as to what address that request should be submitted to! The 'method' parameter clearly defines the type of HTTP request we will make, leaving the 'action' parameter to define where our request should be directed. We come full circle with this traffic control using the Bottle Python module \(or a similar template engine\) to define our routing and serve the application in the following Python file that I've labeled 'stockSearch.py' in my IFS.

```
from bottle import *
from yahoo_finance import *

port_number=62668
host_location='spaces.litmis.com'
stockSearch='lookupStock.html'
rootLoc = '/home/USRQFETE/'

@route('/')
def start():
    return static_file(stockSearch, root=rootLoc) 

@route('/search', method='POST')
def searchPost():
    try:
        ticker = request.forms.get('ticker')
        stock = Share(ticker)
        companyName = stock.get_name()
        stockPrice = float(stock.get_price())
        lowYearly = float(stock.get_year_low())
        highYearly = float(stock.get_year_high())
        exchange = stock.get_stock_exchange()
        if exchange == 'NYQ':
            exchange = 'New York Stock Exchange'
        if exchange == 'NMS':
            exchange = 'Nasdaq'
        try: 
            dividend = float(stock.get_dividend_yield())
        except TypeError:
            dividend = '0.00'
    except:
        companyName = 'Failure'
        stockPrice = 'Whoops!'
        lowYearly = 'You tried a stock outside our system!'
        highYearly = ''
        exchange = ''
        dividend = ''
    return template('/home/USRQFETE/infoOutput.tpl', tick=ticker.upper(), stockName=companyName, price=stockPrice, yearHigh=highYearly, yearLow=lowYearly, stockExchange=exchange, div=dividend)

@route('/close', method='POST')
def close():
    return template('/home/USRQFETE/close.html')

run(host=host_location, port=port_number, debug=True)
```

To dissect the above code, I am going to start near the top and work my way down starting with the first Bottle defined '@route\('/'\)' declaration. This line declares the base address for the application. In other words, the following method is returned when no additions are made to the general '\(http://\) host:root' URL request. We can also see that no 'method' parameter is declared meaning that this route is by default a 'GET' http request serving route, and will not react to a '\(http://\) host:root' URL POST/PUT/etc. request. When this route is called, a static file is rendered from the root file system with the name 'lookupStock.html' as declared in the variable listing above. We now know what will happen when a user searches the default route for our application and have a file being rendered.

The next route encountered in our inspection is the '/search' http POST request route. We know from the 'lookupStock.html' file that this is called in response to a submission of the first declared form in this document. When a user hits the 'Search' button in the 'lookupStock' file, the '/search' route in our Python file is sent the input data from the associated form. In our example, this is a ticker string value that we then make use of in the defined method for the '/search' route with our imported yahoo-finance module. More precisely, when the previously mentioned form is submitted, it generates a request object that we can then make use of to retrieve values from the form. An example of this is the 'request.forms.get\('ticker'\)' call in the above Python code.

One last thing to direct your attention to if trying to understand Bottle and its SimpleTemplate engine is the 'return' line in the '/search' POST route. Notice here that we are using the 'template\(\)' method to both compile and render the given '.tpl' file \(which will be listed below\). Furthermore, we also make use of a group of declarations in this 'template\(\)' method that get used when rendering the given '.tpl' file in place of their assigned variables in that file. To shed some more light on this phenomenon see the following code block that represents 'infoOutput.tpl' as used in the above Python file for our stock ticker inspection application.

```
<html>
<style>
    html{background-color: powderblue;}
    button{top-margin:5px; margin:auto; display:block}
    .spacer{height: 10px;}
    .smallSpacer{height:2px;}
    table{border: 1px solid black;margin-top:10%}
    input{width:100%; border: 1 solid #838383}
</style>  
<head>
    <title>Stock Results</title>
</head>
    <form action="/" method="GET">
    <table align="center"; width="30%"; >
        <caption align="center">{{tick}} Information:</caption>
        <tr>
            <td width="30%">Company Name:</td>
            <td width="70%">{{stockName}}</td>
        </tr>
        <tr>
            <td width="30%">Stock Price:</td>
            <td width="70%">${{price}}</td>
        </tr>
        <tr>
            <td width="30%">One Year High Price:</td>
            <td width="70%">${{yearHigh}}</td>
        </tr>
        <tr>
            <td width="30%">One Year Low Price:</td>
            <td width="70%">${{yearLow}}</td>
        </tr>
        <tr>
            <td width="30%">Stock Exchange:</td>
            <td width="70%">{{stockExchange}}</td>
        </tr>
        <tr>
            <td width="30%">Dividend Yield:</td>
            <td width="70%">${{div}}</td>
        </tr>
    </table>
    <div class="smallSpacer"></div> 
    <button align="center"; type="submit">Search Another</button>
    </form>  
    <form action="/close" method="POST">
        <div class="smallSpacer"></div> 
        <button style="background-color: red"; align="center"; type="submit">Close</button>
    </form>
</html>
```

In the above '.tpl' file, the values surrounded by curly braces are used as variables when rendered by SimpleTemplate. You will find that when moving between template engines \(Mako, Jinja2, Cheeta, SimpleTemplate\) that many of the syntax requirements and rendering requirements are similar. In other words, you can set default values for all of these values, check to ensure the variable being rendered fits a certain regular expression, and use the value as a variable to be replaced when the given template is rendered. In our example, our '/search' route return function provides values to fill the given variable declarations in our '.tpl' file based on the ticker string sent to the function by the user. The final file we use in this example is called whenever a user selects the 'Close' button and acts as a dummy closed application screen. I saved this file as 'close.html' in the root directory of my IFS.

```
<html>
    <style>
        html{background-color:black;}
        button{margin-left: 50%; margin-top: 25%; background-color:green;}
    </style>
    <form action="/" method='GET'>
    <button type="submit">Turn On</button>
    </form>
</html>
```

Once you have these saved, you can go ahead and run the stockSearch.py file and see what happens. You should be able to enter the default address with a '/' at the end and view the beginning of the application as you may have tried in earlier steps.

To add to your understanding and excitement for Python, check out the following files. The first file is a Bottle template file used to output information from the primary file. While I haven't set up a user interface to allow users to change the output based on their entry, the secondary file illustrates the use of the Twitter API to return information from the given users Twitter profile. All you need to do is go to Twitter and set up your access token and secret as well as your consumer key and secret. After you have these authentication pieces, you will be able to enter any user's 'screen name' to view a history of their tweets. Implement the following example to view current president Donald Trump's feed as an example \(realDonaldTrump is his screen name being used as a parameter for the Twitter call below\). You will need the `bottle` and `twitter` libraries in your environment to get this working as you can see in the Python import statements. Remember, use the 'pip install' command to retrieve these and the 'pip list' command if your aren't sure which ones you have. Good luck!

'pip list' results:

```
bottle (0.12.13)                                                                
pip (9.0.1)                                                                     
pytz (2016.10)                                                                  
setuptools (20.3.1)                                                             
simplejson (3.10.0)                                                             
six (1.10.0)                                                                    
twitter (1.17.1)                                                                
wheel (0.29.0)
```

tweetHistory.tpl:

```
<html>
<style>
</style>
<head>
    <title>Tweet Records</title>
</head>
<ul>
    % for tweet in tweetRecords:
        <li>{{tweet}}</li>
    % end
</ul>
</html>
```

twitter\_credentials.py:

```
# Import the necessary package to process data in JSON format
try:
    import json
except ImportError:
    import simplejson as json

import re
from bottle import *
from twitter import Twitter, OAuth, TwitterHTTPError, TwitterStream

port_number=62389
host_location='spaces.litmis.com'
newUserForm='newUser.html'
rootLoc = '/home/USRQPSPY/Templates/'

@route('/')
def start():
        # Variables that contains the user credentials to access Twitter API 
    ACCESS_TOKEN = ''
    ACCESS_SECRET = ''
    CONSUMER_KEY = ''
    CONSUMER_SECRET = ''

    oauth = OAuth(ACCESS_TOKEN, ACCESS_SECRET, CONSUMER_KEY, CONSUMER_SECRET)

    twitter = Twitter(auth=oauth)

    statusDict = twitter.statuses.user_timeline(screen_name="realDonaldTrump", count = 100, exclude_replies = True)

    theText = []

    for line in statusDict:
        text = line['text']
        result = re.sub(r"http\S+", "", text)
        theText.append(result)

    return template('/home/USRQFETE/twitter/tweetHistory.tpl', tweetRecords=theText)


run(host=host_location, port=port_number, debug=True)
```

At this point, you should have at least a general understanding of how Python can be used on the IBM i platform for web application development using almost exclusively Bottle \(and whatever application provides the substance of your site of course, as in yahoo-finance in our examples\). If you're anything like me, you likely have many questions left unanswered though. In the immediate future, we are going to look at how we can access and make use of the native database \(DB2 by IBM\) that comes with access to an IBM i system. Further down the road, I will attempt to better illustrate the possibilities of Python application development on the IBM i system using IBM-Watson, the native database offering DB2, and a few other interesting modules. Until then though, let us in the open source community at IBM know if you have any suggestions or requests for the Python guide, we would really appreciate the feedback!

You can reach me directly via e-mail!

E-Mail: Eric.Newman@ibm.com

## Further Steps Under Construction :\( Last Updated: 3/24/17



