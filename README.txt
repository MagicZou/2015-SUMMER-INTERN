Presentation Link: https://prezi.com/c4vsalyomang/edit/#72_24309637

Document Link: https://confluence.cobalt.com/display/DEV/Storyteller+Applications


Storyteller Applications

Overall concept
   Project Description:
Material link : Storytelling
   Representative Graph:
Problem the app solves
Material link : Storytelling
Implementation
Keywords for Implementation:
Producer web app
Setting up
File Locations 
Tools
Notes
Consumer web app
Setting up
File Locations
Tools
Mobile consumer app
Setting up
Configuration, file locations
Angular: data that persists, services, constants
Notes
Tools
Testing 
Features we wanted to add
Team Members:
Carter, Katherine
Lum, Kimberly
Zou, Shiqi
Mentors:
Zackrone, Keith (DS) 
Makana, Branden (DS)
Reser, Ben

Overall concept
   Project Description:
        Graphite and Grafana are great for capturing time series data, but we need a service to register events against, such as deployments, master site changes, outages, or any other activity. The event listener should be open for all to use as they see fit, and have built in ability to classify and categorize events based on the structure passed in. In addition to single data points, metadata needs to be included such as links to other systems for more information about the events. The events should be accessible to grafana as annotations. And finally, this will need to have it's own visualization engine to allow novice users ability to search, explore, or even compare differences between dates.
Material link : Storytelling
   Representative Graph:

         
 
Problem the app solves
We still struggle to emotionally connect to our customers and communicate the value of our services. We wanted to create new way to communicate the value of Cobalt's services. The goal of this new communication mechanism is to continually engage and communicate to the dealer the value the platform and our services group provides to the dealer every day. The core concept that will be explored is based around the experience of a LIVE Blog webcast (or perhaps simplified to a twitter like tweet feed), communicating the dealers living digital marketing solution. The activity stream could eventually include data from all products.
Material link : Storytelling
Implementation
Keywords for Implementation:
Producer web app: Maven, Docker, Spring, Kafka, ThreadPoolTaskExecutor

Consumer web app: Maven, Docker, Spring, Zookeeper, Websocket
                                  Logstash, Elasticsearch, Restful API, RestTemplate, Java API with Elasticsearch

Mobile consumer app: AppGyver, Supersonic, Steroids

Testing: JUnit, Jasmine, Karma, Grunt
 
Producer web app
Link to Producer Web App : http://stash.cdk.com/projects/SEAINT/repos/storytellerproducer/browse
Live demo : fleet.ord.cdk.com/storytellerproducer/
Setting up
        Our producer web app is supported by maven and docker, so please install maven and boot2docker.
        To set up the ports for kafka and zookeeper, run following command line: 
             docker run -d --name kafka -p 2181:2181 -p 9092:9092 --env ADVERTISED_HOST=`boot2docker ip` --env ADVERTISED_PORT=9092 spotify/kafka
        To run the app locally, go to the project folder (/storytellerproducer) via command line and enter:
             mvn install spring-boot:run
        The default port for producer app will be running on 8080. And the link will be http://localhost:8080/storytellerproducer/
File Locations 
/pom.xml: all maven dependencies, pluginRepositories, properties and dockerfile for this producer web app
/src/main/resources/application.properties: all application properties like server port and kafka ports
/src/main/resources/templates/sendMessage.html: the html file of the web server
/src/main/java/com/cdk/storyteller/producer/ExecutorConfig.java: the configuration for ThreadPoolTaskExecutor
/src/main/java/com/cdk/storyteller/producer/domain/MessageProducer.java: construct kafka configuration and send message to it
/src/main/java/com/cdk/storyteller/producer/controller/SendMessageController.java: start the program when the url is called
Tools
DockerFile: http://perforce.cdk.com/@md=d&cd=//Advertising/babylon/trunk/&cdf=//Advertising/babylon/trunk/pom.xml&c=zc2@//Advertising/babylon/trunk/pom.xml?ac=64&rev1=12
Spring ThreadPoolTaskExecutor: http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html
Spring guides: https://spring.io/guides
Notes
If input hashtag is a field and contains content, eg. #author=XX, then please using quotes around the content, eg. #author="XX".
Consumer web app
Link to Consumer Web App : http://stash.cdk.com/projects/SEAINT/repos/storytellerconsumer/browse
Live demo : fleet.ord.cdk.com/storytellerconsumer/
Setting up
Our consumer web app is supported by maven and docker, so please install maven and boot2docker.
        To set up the ports for kafka and zookeeper, run following command line: (not necessary if already run) 
             docker run -d --name kafka -p 2181:2181 -p 9092:9092 --env ADVERTISED_HOST=`boot2docker ip` --env ADVERTISED_PORT=9092 spotify/kafka
        To run the app locally, go to the project folder (/storytellerconsumer) via command line and enter:
             mvn install spring-boot:run
        The default port for producer app will be running on 8081. And the link will be http://localhost:8081/storytellerconsumer/
File Locations
/pom.xml: all maven dependencies, pluginRepositories, properties and dockerfile for this consumer web app
/src/main/resources/application.properties: all application properties like server port, zookeeper ports, and elasticsearch ports
/src/main/resources/templates/output.html: the html file of the web server
/src/main/java/com/cdk/storyteller/consumer/config/WebSocketConfig.java: the configuration for websocket
/src/main/java/com/cdk/storyteller/consumer/controller/: controllers to start the program(LiveViewController.java), return json callbacks(JSONPControllerAdvice.java), and Restful APIs 
/src/main/java/com/cdk/storyteller/consumer/wrapper/: wrapper classes for the result from Elasticsearch, used by RestTemplate
/module.xml: used by Java API with Elasticsearch
Under /src/main/java/com/cdk/storyteller/consumer/domain/,
AutocompleteResult.java: the wrapper class for restful api for search popular hashtags feature
QueryResult.java: the wrapper class for restful api
Consumer.java: construct zookeeper configuration and get message from kafka
SearchService.java: interface for all queries
JavaAPISearchService.java: detailed implementation for all queries
Restful APIs
Each controller in the /src/main/java/com/cdk/storyteller/consumer/controller corresponding to one Restful API, except JSONPControllerAdvice.java and LiveViewController.java. 
The URL for each Restful Api appends to the end of the host. For example, if running locally, Api's url would be like http://localhost:8081/storytellerconsumer/api_suffix.
To get the last 50 messages in the local cache for all hashtags(including no hashtags), using following suffix:
/messages
The responding controller is MessageController.java. It returns the data in reverse order.
 
     2.  To search messages on keywords or hashtags for a certain period of time and a certain amount results, using following suffix:
/search?query=KEYWORDS&start=STARTTIME&end=ENDTIME&count=NUMBER
The responding controller is QueryController.java. It returns the result in descending time order.
In this API, the parameter for query is required but parameters are not required for start, end, and count.
For example,
if we need 20 results, we can do :  /search?query=KEYWORDS&count=20
if we want messages from July 10th 2015, we can do : /search?query=KEYWORDS&start=2015-07-10
Rules for parameters:
For query parameter: separate by comma, returned result will be the intersection of all queries
For time parameters: general time formate is YYYY-MM-DD and concrete time formate is YYYY-MM-DDTHH:MM:SSZ. Eg. 2015-07-22 or 2015-07-22T16:45:12Z
For count parameter: it should be a number

     3.  To get  a certain amount messages in a time period without keywords search, using following suffix:
/time?start=STARTTIME&end=ENDTIME&count=NUMBER
The responding controller is RangeController.java. It returns the result in descending time order.
In this API, time parameters must exist one, no matter STARTTIME or ENDTIME. However, the parameter is not required for count.
For example,
if we want messages from July 10th 2015, we can do : /time?start=2015-07-10
Rules for parameters:
For time parameters: general time formate is YYYY-MM-DD and concrete time formate is YYYY-MM-DDTHH:MM:SSZ. Eg. 2015-07-22 or 2015-07-22T16:45:12Z
For count parameter: it should be a number

    4.  To get a certain number of messages from Elasticsearch cluster for all hashtags(including no hashtags), using following suffix:
/records?count=NUMBER
The responding controller is RecordController.java. It returns the result in descending time order.
In this API, the parameter for count is required.
Rules for parameters: should be a number.
 
   5.  To get popular hashtags for an incomplete keyword(no matter hashtags or messages), using following suffix:
/approximate?query=KEYPART
The responding controller is AutocompleteController.java. It returns the result in descending used times order.
In this API, the parameter for query is required. 
Rules for parameters: anything to search.
The program will search for every message that contains a word which starts with the "KEYPART". Then it extracts hashtags from those messages and returns these hashtags
in the order of using times. The most popular(most used) hashtag is at the top.
Tools
Websocket: http://jmesnil.net/stomp-websocket/doc/
Spring RestTemplate: http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.htmlSpring Autowired: http://www.tutorialspoint.com/spring/spring_autowired_annotation.htm
Spring Restful Api: https://spring.io/guides/gs/rest-service/
Java API with Elasticsearch: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/index.html
Kafka to Logstash and Logstash to Elasticsearch: https://github.com/brmakana/coreos-elk
Mobile consumer app
Link to Consumer Mobile App: http://stash.cdk.com/projects/SEAINT/repos/storytellermobile/browse
Our mobile app relies on our various APIs to get data. Therefore, it performs pull requests at regular user-selected intervals (anywhere from 3 seconds to an hour).
Setting up
Our mobile app uses the Supersonic framework. To run the app, you must install Steroids (a tool for Supersonic): http://www.appgyver.com/steroids/getting_started
Then, once you've followed all the instructions, pull the code from our repo, go to the project folder (/storytellermobile) via command line, and enter:
steroids connect
Scan the QR code that comes up with the AppGyver Scanner app.
Configuration, file locations
The configuration files are found in /storytellermobile/config, and all of the views, controllers, and stylesheets are found in /storytellermobile/app. These are the only two folders of interest. Inside of /app there is a /consumer folder that contains the bulk of our created content; also in /app there is a /common folder that contains common code used across the app (we were mostly concerned with the .css files here).
There are several pages to the mobile app (listed with their view HTML file and their controller JS file):
Front page (index.html, FrontController) - an index of all the streams a user is following, the "main" page (called the root view in Supersonic). Users follow new streams from this page.
Master settings page (master-settings.html, DrawerController) - contains options that apply to the whole app, i.e. delete all data.
Story page (story-stream.html, MessageController.js) - displays one specific stream.
Story settings page (settings.html, SettingsController.js) - contains options to:
Pre-set filters that restrict both the stream and any searches
Pre-set dates to restrict the stream
Delete the story (redirects to front page)
Search page (advanced-search.html, SearchController.js) - allows users to search by keyword (which applies to message content or filters) or by a date range. Any pre-set filters also apply.
Angular: data that persists, services, constants
We used localStorage to store pieces of data that needed to persist across the app, even when it was opened and closed.
Individual story information: stored in allStoriesService, a service in IndexController.js
Story names (user-defined), a string
Pre-set tags for each story, an array of strings
Pre-set date for each story, an integer indicating the index corresponding to the date input drop-down list (i.e. a date value of 0 corresponds to the 0th index of the dropdown)
Latest view stamp (the timestamp of the last message the user viewed), a string, for helping count notifications when data is pulled
Notifications (the number of messages that have come in since the user last viewed a stream), an integer
Latest notification stamp (the timestamp of the newest message of the stream), a string, used solely for sorting the stories on the main screen
The above fields are stored as such, one entry per story:
[
{name: "My Story", tags: ["tag1", "tag2"], date: 2, latestViewStamp: "8/5/2015 12:30:37 PM", notifications: 16, latestNotifStamp: "8/6/2015 1:34:55 PM"},
{name: "Chevy", tags: ["chevy", "chevrolet"], date: 0, latestViewStamp: "8/9/2015 08:05:17 AM", notifications: 9, latestNotifStamp: "8/10/2015 11:44:01 AM"},
...
]
Story preview information: stored in cachePreviews, a service in IndexController.js
Stores story previews, for displaying on the main page. There will three or less previews per story, and they will be sorted with the latest message first:
[
{name: "My Story", threePreviews: [
{content: "Story one #one", stamp: "8/7/2015 3:01:56 PM"},
{content: "Story two #two", stamp: "8/7/2015 2:13:25 PM"},
{content: "Story three #three", stamp: ""8/7/2015 1:54:01 PM""}
]
},
...
]
Other relevant services and constants: besides allStoriesService and cachePreviews, and the aforementioned controllers, important aspects of our AngularJS files are:
Services:
validateService: returns true if input is defined, not an empty String, and is not null
modTimestamp: changes a timestamp from GMT time to the user's local time
basicStoryURL: returns a URL to access the correct API depending on the preset filters and date (used for notification, previews use, not the actually message displaying)
dateService: takes a date field from the allStoriesService structure (an integer) and returns the current past date.
example: if "1" is passed in, this corresponds to the index 1 of presetTimes, which is the value "1 month ago". Therefore, the service returns the date 1 month ago from today.
notifDelayService: allows the app to see and set how often data is pulled depending on what the user chooses (necessary for cross-view communication between the app settings page and the main page)
Constants:
presetTimes: an array with values for the preset dates (how far back the data returned goes, i.e. 1 month ago, 2 months ago...)
presetDelays: an array with values for the pulling interval (how often the API is queried by the app, 3 seconds, 1 minute,..., 1 hour)
increaseAmount: currently 15, how many messages are returned by a default query (no presets), as well as how many more message are shown when "Get More" is pressed
Controllers:
LinkController: trusts all message HTML
AutocompleteController: controls the autocomplete feature in search
Notes
IndexController.js holds all of our services and constants for the app. It also contains a few controllers (FrontController, DrawerController) that Supersonic did not recognize as controllers when they had their own individual files.
ButtonController.js holds pure Javascript, responsible for controlling page scrolls.
Any external scripts should be placed in /app/consumer/scripts, and the associated <script> tag should be placed in index.html.
CSS changes have been placed in /app/common/stylesheets/application.css.
There is no sanitization at this point in time. We trust all HTML input; the only modification we make is that when the data is received, we add a special Supersonic onclick property to URLs in anchor tags.
Tools
Supersonic documentation and tutorials: http://docs.appgyver.com/supersonic/
Ionicons: http://ionicons.com
Useful for searching for the name of icons. Ionicons are named according to the convention ion-[icon name]. For Supersonic, we use the convention super-[icon name]. So, an icon in Ionic named "ion-arrow-resize" becomes "super-arrow-resize" in our app. To apply to the mobile app, ensure that you include "icon" as a class, and the name of the icon in the class. For example:
<span class="icon super-ios-arrow-forward"></span>
creates an icon of an arrow pointing right.
Chrome DevTools: chrome://inspect/#devices
With an Android device plugged in, we could see any error messages that came up, and debug using console.log() statements.
Testing 
Setting up

Any tests written for Java classes (for the producer and consumer) use the JUnit framework http://junit.org/

Any tests written for JavaScript controllers (for the mobile app) use a combination of Grunt (a JavaScript task runner) http://gruntjs.com/, Karma (a test runner) http://karma-runner.github.io/0.10/index.html, and Jasmine (a framework for writing tests) http://jasmine.github.io/

In order to run these tests, all of the above frameworks and runners must be downloaded and the below config files must be present (they are all included in the repo).
To run any of the JUnit tests, simply open up your preferred Java IDE and run them as JUnit Tests
To run the Jasmine tests, run the following command line and all tests should be executed:
grunt unittest
Configuration, file locations

The config files necessary for testing are storytellermobile/package.json, storyteller/Gruntfile.js, bower.json, and storytellermobile/test/karma.conf.js and are all included in the repo.

Actual testing files are located under storytellerconsumer/src/test/java/test/ (for the consumer) and storytellermobile/test/ (for the mobile app). Test files within the mobile app should follow the same path as the file being tested, except in storytellermobile/test/ rather than storytellermobile/app/, which is where all the actual app files are located. For example, when writing a test file for storytellermobile/app/consumer/scripts/IndexController.js, I would locate it under storytellermobile/test/consumer/scripts/IndexControllerSpec.js
Features we wanted to add
Pull to refresh
Ability to accept/control and safely inject certain HTML tags (i.e. allows bold tags but not i frames, etc.)
Allow safe, approved JS to be injected (i.e. "Do you approve this ad?" with accept and reject options)