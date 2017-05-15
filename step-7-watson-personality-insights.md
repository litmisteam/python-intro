# Step 7: Watson - Personality Insights

In this section we will be using the Watson Developer Cloud Personality Insights API's to interface with Watson and asses an individuals personality based on a selection of text stored on the native DB2 offering, a excerpt of text pasted in via the user, or through the timeline of an individuals Twitter page given their associated handle. This has a vast array of use cases from low-level consumer trend information \(given a certain personality what are they most likely to buy and when\) as well as higher-order tasks such as AI interaction modifications given detected personality insights found within the context of a single interaction or over the course of many. To get started, you will first need access credentials associated with your chosen Watson API to allow Watson to identify you and service your requests. 

#### Watson Access

1. Create a Bluemix account to gain access to a mix of Watson API's as well as a variety of other services: [Bluemix](https://www.ibm.com/cloud-computing/bluemix/)
2. Go to your dashboard page on Bluemix.![](/assets/dashOnly.png)
3. Select 'Create Service'. ![](/assets/blueMixServices.PNG)
4. Create the Personality Insights Watson service and return to the 'dashboard' page. From there, scroll down to your newly created Personality Insights service offering and select it. 
5. In the Personality Insights listing, go to the Service Credentials tab and take note of your username and password, these will be needed to access the service later. ![](/assets/viewCredentials.PNG)

#### Watson Developer Cloud

At this point your almost ready to start assessing people's souls. Log in to your Python enabled Litmis Space and using the proper 'pip' version install the watson-developer-cloud library. At this point, you actually have a variety of Watson service API's ready for you to make use of including but not limited to Personality-Insights, Conversation, TextToSpeech, SpeechToText and many more. For many of these offerings, you will also need the 'json' Python library as Watson likes to send JSON response objects through many of the API calls.

For this particular Watson sample we are going to be using a slightly different library than the normal watson-developer-cloud python-sdk as we normally would. We are going to make use of the more dated yet still useful personality-insights-python sub directory of the watson-developer-cloud suite \(instead of the watson-developer-cloud/pthon-sdk/personality-insights library\). While this is a minor distinction it should be noted especially if you plan to return to he personality insights service as you may find it more beneficial to use the python-sdk tool kit to perform some of the requests made to the personality insights service as shown below. 

The following server.py example code uses a variety of APIs interchangeably to enable users access to a variety of text sources for interpretation by the Watson Personality Insights Service. 

```
#
# Copyright 2014 IBM Corp. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
## -*- coding: utf-8 -*-

## Edited and Updated - May 15, 2017 by Eric Newman @IBM

import os
import cherrypy
import requests
import re
import codecs
try:
    import json
except ImportError:
    import simplejson as json
import ibm_db_dbi as dbi
from mako.template import Template
from mako.lookup import TemplateLookup
from twitter import Twitter, OAuth, TwitterHTTPError, TwitterStream


class PersonalityInsightsService:
    """Wrapper on the Personality Insights service"""

    def __init__(self, vcapServices):
        """
        Construct an instance. Fetches service parameters from VCAP_SERVICES
        runtime variable for Bluemix, or it defaults to local URLs.
        """

        # Local variables for Watson Access - Examples Used (Replace with your own)
        self.url = "https://gateway.watsonplatform.net/personality-insights/api"
        self.username = "58f09419-e636-40dd-a6c5-89844b69e1f2"
        self.password = "qeJhT9k5Pd0L"

        if vcapServices is not None:
            print("Parsing VCAP_SERVICES")
            services = json.loads(vcapServices)
            svcName = "personality_insights"
            if svcName in services:
                print("Personality Insights service found!")
                svc = services[svcName][0]["credentials"]
                self.url = svc["url"]
                self.username = svc["username"]
                self.password = svc["password"]
            else:
                print("ERROR: The Personality Insights service was not found")

    def getProfile(self, text):
        """Returns the profile by doing a POST to /v2/profile with text"""

        if self.url is None:
            raise Exception("No Personality Insights service is bound to this app")
        response = requests.post(self.url + "/v2/profile",
                          auth=(self.username, self.password),
                          headers = {"content-type": "text/plain"},
                          data=text
                          )
        try:
            return json.loads(response.text)
        except:
            raise Exception("Error processing the request, HTTP: %d" % response.status_code)


class DemoService(object):
    """
    REST service/app. Since we just have 1 GET and 1 POST URLs,
    there is no need to look at paths in the request.
    This class implements the handler API for cherrypy library.
    """
    exposed = True
    
    #Signifies the private contents of this class (only demoservice members can use these methods)
    def __init__(self, service):
        self.service = service
        self.defaultContent = None
        try:
            # Below, you will need to change the path of the text file to match that of your own (USR40URV will need to change at least)
            contentFile = open("/home/USR4OURV/personality-insights-python/public/text/en.txt", "r")
            self.defaultContent = contentFile.read()
        except Exception as e:
            print("ERROR: couldn't read en.txt: " + e)
        finally:
            contentFile.close()

    def GET(self):
        """Shows the default page with sample text content"""
        return lookup.get_template("index.html").render(content=self.defaultContent)
    
    #@cherrypy.tools.json_in()
    def POST(self, text=None, firstname=None, lastname=None, twittername=None, fullname=None):
        if twittername != '':
            try:
                cherrypy.log('Sent Twitter Feed to Service')
                twitterResults = queryTwitter(twittername)
                cherrypy.log('Twitter Results: ' + str(twitterResults))
                profileJson = self.service.getProfile(twitterResults)
                return json.dumps(profileJson)
            except Exception as e:
                cherrypy.log("ERROR: %s" % str(e))
                return str(e)
        elif fullname != None and fullname != "None":
            try:
                cherrypy.log('fullname: ' + str(fullname))
                firstname, lastname = fullname.split(' ')
                cherrypy.log('Firstname: ' + firstname)
                cherrypy.log('Lastname: ' + lastname)
                theText = query_ibm_db(firstname, lastname)
                if theText == "No data on user":
                    raise Exception('No such user exists!')
                cherrypy.log('Developer Data Retrieved from BlogData Database: %s' % theText)
                profileJson = self.service.getProfile(theText)
                return json.dumps(profileJson)
            except Exception as e:
                cherrypy.log("Error: %s" % str(e))
                return str(e)
        else:
            try:
                cherrypy.log('Sent Textbox Entry to Service')
                textEntry = checkUserInput(text)
                cherrypy.log('Textbox Entry: ' + str(textEntry))
                profileJson = self.service.getProfile(text)
                return json.dumps(profileJson)
            except Exception as e:
                cherrypy.log("ERROR: %s" % str(e))
                return str(e)

def queryTwitter(twitterID):
    # These will need to be replaced with your own and can be found through your Twitter page
    ACCESS_TOKEN = '840252234956213023-CflmR7LiX8JyVacBbANoe1vLiFDnwxz'
    ACCESS_SECRET = 'FoQkkImWgUNMm4MbzjfuJz3xDd8zxSd9rBMSTsmjA9oRl'
    CONSUMER_KEY = '2fEnyoMPjfNMrBjRIxTq9mLuV'
    CONSUMER_SECRET = 'JbGBXgR0VM2SiXHt1EwmxUaqsrSTCfn9RGMV2ddET1POp1FmRD'
    
    #declare authorization credentials listed above to the Twitter service
    oauth = OAuth(ACCESS_TOKEN, ACCESS_SECRET, CONSUMER_KEY, CONSUMER_SECRET)
    twitter = Twitter(auth=oauth)
    #retrieve the given useres status history
    statusDict = twitter.statuses.user_timeline(screen_name=twitterID, count = 100, exclude_replies = True, charset='utf8')
    theTextArray = []
    
    #Ensure the proper encoding 
    for line in statusDict:
        text = line['text']
        text = re.sub('[^A-Za-z0-9 !.,&]+', '', text)
        text = re.sub(r"http\S+", "", text)
        theTextArray.append(text)

    simpleText = ''.join(theTextArray)
    return simpleText

def query_ibm_db(firstname, lastname):
    conn = dbi.connect(dsn=None, database='*LOCAL', user=None, password=None)
    if conn:
        db2 = conn.cursor()
        # You need to replace the YOUR_DB_SCHEMA_NAME to your local associated database schema name. 
        db2.execute("SELECT THEDATA FROM YOUR_DB_SCHEMA_NAME.BLOGDATA WHERE FIRSTNAME LIKE ? AND LASTNAME LIKE ?",(firstname, lastname))
        result = db2.fetchall()
        db2.close()
        if not result:
            return "No data on user"
        else:
            #We are cleaining up the database in this section...
            result = ''.join(str(e) for e in result)
            result = re.sub('[^A-Za-z0-9 !.,&]+', '', result)
            result = re.sub(r"http\S+", "", result)
            return result
    else:
        return "No database connection detected."

def checkUserInput(text):
    try:
        #Clean up user input
        result = re.sub('[^A-Za-z0-9 !.,&]+', '', text)
        result = re.sub(r"http\S+", "", result)
        return result
    except Exception as e:
        cherrypy.log("ERROR: %s" % str(e))
        return str(e)

if __name__ == '__main__':
    lookup = TemplateLookup(directories=["templates"])

    # Get host/port from the Bluemix environment, or default to local
    HOST_NAME = os.getenv("VCAP_APP_HOST", "spaces.litmis.com")
    PORT_NUMBER = int(os.getenv("VCAP_APP_PORT", "62268"))
    cherrypy.config.update({
        "server.socket_host": HOST_NAME,
        "server.socket_port": PORT_NUMBER,
    })

    # Configure 2 paths: "public" for all JS/CSS content, and everything
    # else in "/" handled by the DemoService
    conf = {
        "/": {
            "request.dispatch": cherrypy.dispatch.MethodDispatcher(),
            "tools.response_headers.on": True,
            "tools.staticdir.root": os.path.abspath(os.getcwd())
        },
        "/public": {
            "tools.staticdir.on": True,
            "tools.staticdir.dir": "./public"
        }
    }

    # Create the Personality Insights Wrapper
    personalityInsights = PersonalityInsightsService(os.getenv("VCAP_SERVICES"))

    # Start the server
    print("Listening on %s:%d" % (HOST_NAME, PORT_NUMBER))
    cherrypy.quickstart(DemoService(personalityInsights), "/", config=conf)

```



