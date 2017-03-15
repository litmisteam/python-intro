# Simple Template and Routing Cont.

The goal of Step 4 in this tutorial is to introduce the audience to much more complex bottle topics while also digging deeper into concepts introduced in previous steps. We will focus on both more in depth template usage as well as interactions with the Bottle template interface while working with the yahoo-finance module to produce a ticker lookup system that returns information about a company associated with a valid user entered ticker string. While this is not meant to make you an expert on DB2 operations, SQL queries, or template usage, it may help incoming or perspective IBM i/Bottle/Litmis Space users better understand the core functionality of each of these technologies and how they interact and complement eachother.

#### Templates

In a way, this section is a necessary continuation of Step 2's introduction to the Bottle template engine (SimpleTemplate). Here though, we are going to go further in depth and make use of form data as well as rendering parameters for the templates. First, we need to create a file that represents the entry point for our web application. This file will contain the contents of the first page that any incoming user to our site is directed to. For this sections example application, we are going to build off the yahoo-finance library that we encountered briefly in Step 3. I will also be using the litmis space that we set up in the IBM i Access section throughout this section of the tutorial. Copy and paste the following '.html' file to your IFS with a filename of your choosing. If you are still struggling to understand the Integrated File System (IFS) you can simply create a new file titled 'lookupStock.html' in the root directory of your workspace filesystem and copy the following code to that file.

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

Reiterating a previous point, the above code is not supposed to be showing flawless HTML or CSS usage. What I am trying to show is the use of the values submitted from the above form by our Bottle api. When this form is rendered, the user has the option to enter a ticker value and select the 'Search' button, or hit the 'Close' button. Each of these options has a different form 'action' parameter. The form line is used to signify both the type of HTTP request we will be performing as well as to what address that request should be submitted to! The 'method' parameter clearly defines the type of HTTP request we will make, leaving the 'action' parameter to define where our request should be directed. We come full circle with this traffic control using the Bottle Python module (or a similar template engine) to define our routing and serve the application in the following Python file that I've labeled 'stockSearch.py' in my IFS.

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

To disect the above code, I am going to start near the top and work my way down starting with the first bottle defined '@route('/')' declaration. This line declares the base address for the application. In other words, the following method is returned when no additions are made to the general '(http://) host:root' URL request. We can also see that no 'method' parameter is declared meaning that this route is by default a 'GET' http request serving route, and will not react to a '(http://) host:root' URL POST/PUT/ECT. request. When this route is called, a satic file is rendered from the root file system with the name 'lookupStock.html' as declared in the variable listing above. We now know what will happen when a user searches the default route for our application and have a file being rendered.

The next route encountered in our inspection is the '/search' http POST request route. We know from the 'lookupStock.html' file that this is called in response to a submission of the first declared form in this document. When a user hits the 'Search' button in the 'lookupStock' file, the '/search' route in our Python file is sent the input data from the associated form. In our example, this is a ticker string value that we then make use of in the defined method for the '/search' route with our imported yahoo-finance module. More precisely, when the previously mentioned form is submitted, it generates a request object that we can then make use of to retrieve values from the form. An example of this is the 'request.forms.get('ticker')' call in the above Python code. 

One last thing to direct your attention to if trying to understand Bottle and it's SimpleTemplate engine is the 'return' line in the '/search' POST route. Notice here that we are using the 'template()' method to both compile and render the given '.tpl' file (which will be listed below). Furthermore, we also make use of a group of declarations in this 'template()' method that get used when rendering the given '.tpl' file in place of their assigned variables in that file. To shed some more light on this phenomenon see the following code block that represents 'infoOutput.tpl' as used in the above Python file for our stock ticker inspection application.

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

In the above '.tpl' file, the values surrouned by squigly braces ({) (squigly braces may not be the proper term there), are used as variables when rendered by SimpleTemplate. You will find that when moving between template engines (Mako, Jinja2, Cheeta, SimpleTemplate) that many of the syntax requirements and rendering requirements are similar. In other words, you can set default values for all of these values, check to ensure the variable being rendered fits a certain regular expression, and use the value as a variable to be replaced when the given template is rendered. In our example, our '/search' route return function provides values to fill the given variable declarations in our '.tpl' file based on the ticker string sent to the function by the user. The final file we use in this example is called whenever a user selects the 'Close' button and acts as a dummy closed application screen. I saved this file as 'close.html' in the root directory of my IFS. 

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



