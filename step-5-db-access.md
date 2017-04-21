# Step 5: DB2 Access

In this section we will be using the `ibm_db_dbi` library to connect to the local database associated with the IBM i system and enact changes on the contents of this database. This section is meant to prepare us for a more in depth discussion of the Watson Cognitive API and its use on the IBM i system.

#### Database Access

To begin our look at how we can access the native database included in our IBM i system, we need to import the proper library using the 'pip install' command.

\*\*

I have decided to put up some code illustrating the use of the native DB2 database through the ibm-db connector \(see the import statement for specific name\). With the 'pip3 list' function in the shell interface I determined that I am using ibm-db version \(2.0.5.4\) for this example. I've been hesitant to put this up as I'm somewhat strapped for time and therefore can't run through the file as I have in previous sections for clarity. Regardless though, I hope this can help shed some light on one of the more intimidating concepts to new developers that is database access. 

NOTE: You will need to change the port number to that of your own space, the file paths throughout the code to match your own space, as well as the name of the database used in the query strings below to your own \(mine is RBJ73\_T and this can be found under your space information from the 'workspaces' pane on Litmis Spaces\).

```
from bottle import *
import ibm_db_dbi as dbi

port_number=62389
host_location='spaces.litmis.com'

@route('/newWatsonDeveloperProfile')
def newUser():
    return template('/home/USR3OUAV/dbConnect/Templates/newUser.html')

@route('/newWatsonDeveloperProfile', method='POST')
def fillDatabase():
    try:
        firstName = request.forms.get('firstname')
        lastName = request.forms.get('lastname')
        title = request.forms.get('title')
        theData = request.forms.get('data')
        result = newEntry(firstName, lastName, title, theData)
        return template('/home/USR3OUAV/dbConnect/Templates/make_table.tpl', rows=result)
    except Exception as e:
        print('EXCEPTION: ' + str(e))
        return template('/home/USR3OUAV/dbConnect/Templates/newUser.html')

@route('/removeUser', method='POST')
def removeUser():
    try:
        firstName = request.forms.get('firstname')
        lastName = request.forms.get('lastname')
        result = removeEntry(firstName, lastName)
        return template('/home/USR3OUAV/dbConnect/Templates/make_table.tpl', rows=result)
    except Exception as e:
        print('EXCEPTION: ' + str(e))
        return template('/home/USR3OUAV/dbConnect/Templates/newUser.html')

@route('/')
def showTheDatabase():
    try:
        conn = dbi.connect(dsn=None, database='*LOCAL', user=None, password=None)
        db2 = conn.cursor()
        sql = "CREATE OR REPLACE TABLE RBJ73_T.BLOGDATA(FIRSTNAME VARCHAR(200) ALLOCATE(20) CCSID 1208, \
                                             LASTNAME VARCHAR(200) ALLOCATE(20) CCSID 1208, \
                                             TITLE VARCHAR(1000) ALLOCATE(20) CCSID 1208, \
                                             THEDATA VARCHAR(30739) CCSID 1208)"
        db2.execute(sql)
        db2.execute("SELECT * FROM RBJ73_T.BLOGDATA FOR READ ONLY")
        result = db2.fetchall()
        db2.close()
        conn.close()
        return template('/home/USR3OUAV/dbConnect/Templates/make_table.tpl', rows=result)
    except Exception as e:
        print('EXCEPTION: ' + str(e))
    finally:
        print('Using this to illustrate that finally runs every time!')

def newEntry(firstname, lastname, title, data):
    try:
        conn = dbi.connect(dsn=None, database='*LOCAL', user=None, password=None)
        db2 = conn.cursor()
        db2.execute("INSERT INTO RBJ73_T.BLOGDATA (FIRSTNAME, LASTNAME, TITLE, THEDATA) VALUES (?,?,?,?)",(firstname, lastname, title, data))
        conn.commit()
        db2.execute('Select * from RBJ73_T.BLOGDATA')
        result = db2.fetchall()
        db2.close()
        conn.close()
        if not result:
            return "No data on user"
        else:
            return result
    except Exception as e:
        return 'Exception' + str(e)

def removeEntry(firstname, lastname):
    try:
        conn = dbi.connect(dsn=None, database='*LOCAL', user=None, password=None)
        db2 = conn.cursor()
        print('Showing firstname in remove: ' + str(firstname))
        db2.execute("DELETE FROM RBJ73_T.BLOGDATA WHERE FIRSTNAME = ? AND LASTNAME = ?",(firstname, lastname))
        conn.commit()
        db2.execute('Select * from RBJ73_T.BLOGDATA')
        result = db2.fetchall()
        db2.close()
        conn.close()
        if not result:
            return "No data on user"
        else:
            return result
    except Exception as e:
        return 'Exception' + str(e)

run(host=host_location, port=port_number, debug=True)
```

The following files will also be needed to allow the application to run. If you are attempting to keep with my setup from the lines above, I have them placed in a sub-folder named 'Templates' in the default location for the project.

make\_table.tpl :

```
%#template to generate a HTML table from a list of tuples (or list of lists, or tuple of tuples or ...)
<html>
<head>
<style>
html {
    text-align: center;
    background-color: DeepSkyBlue;
}

body {
    display: inline-block;
}

table {
    border-collapse: collapse;
    text-align: center;
    margin-bottom:10px;
}

.tableContainer {
	width: 60%;
    padding-left: 20%;
}

input[type=text] {
    width: 100%;
    margin-bottom:1%;
}

td {
    padding:10px;
}

.removeContainer {
    width:40%;    
    padding-left:30%;
}

.removeUser {
	background-color: red;
}

</style>   
</head>
<body>
<div class="tableContainer">
<title>Database Table</title>
<h1>Developer Database Records</h1>
<table border="1">
<tr> 
    <th><b>First Name</b></th>
    <th><b>Last Name</b></th>
    <th><b>Title</b></th>
    <th><b>Excerpt of Writing</b></th>
<tr>
% for row in rows:
  <tr>
  %for col in row:
    <td>{{col}}</td>
  %end
  </tr>
%end
</table>
<form action="/newWatsonDeveloperProfile" method="GET">
    <button type="submit" class="newUserBtn">New User</button>
</form>
</div>

<div class="removeContainer">
<form action="/removeUser" method='POST'>
    
    <label><b>First Name:</b></label>
    <input type="text" placeholder="First Name" name="firstname" required>
    
    <label><b>Last Name:</b></label>
    <input type="text" placeholder="Last Name" name="lastname" required>
    
    <button type="submit" class="removeUser">Remove User Record</button>

</form>
</div>
</body>
</html>
```

newUser.html: 

```
<html>
<head>
    <title>Developer Data Entry</title>
<style>
html {
    text-align: center;
    background-color: DeepSkyBlue;
}

body {
	margin-top: 10%;
    display: inline-block;
}

.inputContainer {
	width: 50%;
    padding-left: 25%;
}

input[type=text] {
    width: 100%;
    margin-bottom:2%;
}

textarea {
	margin-bottom:1%;
    width: 100%;
}

.cancelBtn {
	background-color: red;
}

</style>   
</head>
<body>
<div class="inputContainer">
    <form action="/newWatsonDeveloperProfile" method=post>
   
        <label><b>First Name:</b></label>
        <input type="text" placeholder="First Name" name="firstname" required>
    
        <label><b>Last Name:</b></label>
        <input type="text" placeholder="Last Name" name="lastname" required>
        
        <label><b>Title:</b></label>
        <input type="text" placeholder="Job Title" name="title" required>
 
        <label><b>Data to Analyze:</b></label>
        <textarea rows="8" required
    					placeholder="Please enter the text to analyze (minimum of 100 words)..."
    					name='data'>
    	</textarea>
    	<button type="submit" class="btn">Submit</button>
    </form>
</div>
<br></br>
<div class="cancelContainer">
    <form action="/" method="GET">
        <button type="submit" method="GET" class="cancelBtn">Show Database</button>
    </form>
</div>
</body>
</html>
```



