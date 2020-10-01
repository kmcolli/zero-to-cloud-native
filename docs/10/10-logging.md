Zero to Cloud-Native with IBM Cloud
===================================
**Kevin Collins ( kevincollins@us.ibm.com )**  
**Kunal Malhotra ( kunal.malhotra3@ibm.com )**  

Part 10: Logging the Application 
================================

### 

1 - Introduction 
----------------

Now that we have deployed the application and have it running, we can
turn to day 2 operations aspects of running the application. One of the
first things you will want to do is enable logging. IBM Log Analysis
with LogDNA offers administrators, DevOps teams, and developers advanced
features to filter, search, and tail log data, define alerts, and design
custom views to monitor application and system logs.

In this tutorial we will be focusing both on the developer view of
logging in being able to troubleshoot the code itself and the DevOps
team in looking holistically at the application. We will be creating
specific reporting views of each microservice, graphical representation
of the application performance, alerting when something goes wrong, and
finally exporting a subset of the logs to an end-user with relevant
information.

To begin with, let's look at the logging dashboard.

Note: This tutorial assumes you have already completed Part 7, section
1-1 where you have created a LogDNA instance and connected it your
OpenShift cluster.

### 1-1 Starting the LogDNA Dashboard

There are two ways to start the LogDNA Dashboard. The first one is
directly from you cluster dashboard. After logging into IBM Cloud, from
the dashboard, select OpenShift and then Clusters.

![Graphical user interface, text, application Description automatically
generated](.//media/image2.png)

Next, click on your cluster name where you deployed the zero to cloud
native tutorial application.

![Graphical user interface, application Description automatically
generated](.//media/image3.png)

From this overview page of your cluster, click on Launch next to
logging.

![Graphical user interface, text, application Description automatically
generated](.//media/image4.png)

When you start logDNA in this manner, it will take you automatically in
context to the log messages just for that cluster. If you look at the
URL, you will notice that it ends in tags=\<cluster_name\>, so you will
only see relevant log messages for this cluster.

![](.//media/image5.png)

The other way to launch LogDNA s through the Observability from the IBM
Cloud menu.

![A picture containing graphical user interface Description
automatically
generated](.//media/image6.png)

On, the next screen, click on Logging and then View LogDNA.

![Graphical user interface, application Description automatically
generated](.//media/image7.png)

This time, you will see all the log messages for all the data sources
you have enabled. Note, that even if you go through the cluster overview
to get to LogDNA, you can still see all the log messages that LogDNA is
capturing. You are just starting with a view of log messages only for
that cluster.

### 1-2 Searching Log Messages

One of the great features of LogDNA is being able to perform a natural
language search on all of your log messages. The following example will
assume you have run the get OCP Token API from the tutorial application
that you deployed and tested in the previous section.

I will search for OCPToken to see when this API has been called. In the
search criteria at the bottom of the page, I'll enter **OCPToken**.

![Text Description automatically
generated](.//media/image8.png)

You can see all the log messages that contain OCPToken. I can then limit
the results by specifying a time frame. To view all log messages
containing OCPToken from today, I simply enter today at the timeframe.

![A picture containing graphical user interface Description
automatically
generated](.//media/image9.png)

You can play around the time frame and enter other criteria like search
the last 12 hours.

![](.//media/image10.png)

From say 9 am and 11 am today.

![](.//media/image11.png)

To turn on a live tail to view log messages as they arrive, click on the
Live button.

![](.//media/image12.png)

Now, execute the get OCP Token API and you will see new log messages
appear.

![A screenshot of a cell phone screen with text Description
automatically
generated](.//media/image13.png)

This just gives you an idea of how powerful and useful this search is. I
probably use this every day to narrow down log messages in various
applications I have running.

On the left-hand side of each message, you will see a triangle to
example. Clicking on the triangle gives you more information about the
log message.

![A screenshot of a cell phone screen with text Description
automatically
generated](.//media/image14.png)

This view shows us some very important information.

You can see which pod the log message came from in the Source section:  
![Graphical user interface Description automatically
generated](.//media/image15.png)  

Which environment it came from:  
![](.//media/image1.png)  

The microservice or App:  
![](.//media/image16.png)

##

Plus any special tags that we add to the log message. In this case, our
python code is adding api.\<domain_name\> to each log entry so we can
easily filter only on those log messages relevant to the tutorial
application.

Having this information added to each log messages is very beneficial to
track down exactly where a log message came from especially if there is
an error.

At the top of the message, there several helpful options as well.

Clicking on **View in Context** shows you other log messages that
occurred around the same time as the original message you viewed. Be
default, the context is set to source and app, you can expand this by
just app, just source, or everything if you wish.

![Graphical user interface, text Description automatically
generated](.//media/image17.png)

Click **copy to clipboard** copies the log message itself to the
clipboard. Note, that this only copies the log next and not the
metadata.

```
10.240.128.4 - - \[28/Sep/2020 15:53:12\] \"\[37mPOST/api/v1/getOCPToken/ HTTP/1.1\[0m\" 200 --
```

**Share this line item**, allows you to share a URL that another user
can look at to view the log messages starting with the specific message
you have selected. Note that whomever opens the link must have
permissions to view log messages in this logDNA instance. The nice thing
about sharing the link however, is the that recipient will view the log
message in context. The next part of this tutorial series 10A will go
through how you can share log messages for those who do not have access
to the LogDNA instance itself.

#### 1-2-1 Configure Python and LogDNA

In Visual Studio code, or your favorite IDE, navigate to the
app/api-frontend-02cn.py file in the api-frontend-02cn repository. In
the beginning of the file, you will see where we set the logging
parameters that show up as metadata in each log entry.

![A picture containing text Description automatically
generated](.//media/image18.png)

A couple of things to note here:

**level** -- by default, the tutorial application is logging very
granularly so we can view all log messages using the DEBUG level. For a
production level system, you might consider changing this to INFO or
ERROR.

**key** -- this is the logDNA key we previously retrieved in part 7
section 5 of this tutorial series.

**app** -- we are setting our microservice name as the app.

**tags** -- we are using the server name that we set in our Kubernetes
secrets as a special tag. In this case, the tag is
api.zero-to-cloud-native.

**env** -- environment this application represents. In our case dev,
which we are setting in our Kubernetes secrets.

**url** -- this is the LogDNA ingestion URL we retrieved with our key in
section 7 part 5 and set in our Kubernetes secrets.

 1 - 3 Filtering Log Messages
-----------------------------

By default, LogDNA captures log messages from every component of your
Kubernetes cluster which can be overwhelming if you don't narrow down
your search criteria. LogDNA provides several tools to filter the log
messages so you can focus on what is important to you.

### 1-3-1 Filter on an Application

One very helpful view is to only view those log messages for a specific
application. In this example, we will filter only on those log messages
for our tutorial application. To do so, click on All Tags, and select
the tag for our tutorial application -- api.zero-to-cloud-native.com

![Graphical user interface, text, application, email Description
automatically
generated](.//media/image19.png)

After clicking this, our view will change to only show those log
messages for our tutorial application. This will be a very common and
helpful view that that we will want to view often. To save this view,
click on **Unsaved View** and select **Save as new view/**alert.

![Graphical user interface, text, application, email Description
automatically
generated](.//media/image20.png)

The next thing we will want to do is create a category or grouping of
like views. We will create a **dev zero-to-cloud-**native category by
typing in **dev zero-to-cloud-native** under Category and then selecting
A**dd this as a new view category**.

![Graphical user interface, text, application Description automatically
generated](.//media/image21.png)

Next, enter a name of the View, in this case set the name to **All**,
then click **Save View**.

![Graphical user interface, text, application, email Description
automatically
generated](.//media/image22.png)

Now, on the left hand side of the View tab, you will see a new category
**DEV ZERO-TO-CLOUD-NATIVE** with one view called **ALL**, that we just
created. Now we can easily go back to this view by clicking on the name
of the view.

![Text Description automatically
generated](.//media/image23.png)

While viewing log messages at the application level view is very useful,
there are still a lot of log messages across all microservices that make
up the application. A best practice is to have a separate view for each
microservice.

While on the ALL view of the DEV ZERO-TO-CLOUD-NATIVE category, we can
now filter on each microservice. Starting with the api-frontend-02cn
microservice, click on **All Apps**, and then select
**api-frontend-02cn.py**

![Graphical user interface, text, application, email Description
automatically
generated](.//media/image24.png)

The view will now update to only show those log messages from this
microservice. To save this view, click on ALL and then **Save as new
view / alert**.

![Graphical user interface, text, application, email Description
automatically
generated](.//media/image25.png)

Change the name of **API Frontend**.

![Graphical user interface, text, application Description automatically
generated](.//media/image26.png)

Now you will see a second view that we can quickly access to view log
messages from the api frontend microservice.

![Graphical user interface, application Description automatically
generated](.//media/image27.png)

Repeat the same steps for the remaining microservices:

-   enable-node-ssh-02cn

-   load-ocp-version-02cn

-   ocp-realtime-02cn

-   utility-02cn

\* note: we are not logging the web frontend as it is a simple web
server but this is something you can do as extra credit if you like.

After you add new views for each microservice, you should see the
following views.

![Graphical user interface, application Description automatically
generated](.//media/image28.png)

Now, we can very easily view log messages specific to any of our
microservices which is very useful for developers of those
microservices.

The last thing to point out on the Views tab is being able to filter on
different levels of log messages. By default, all levels are selected.

![Text Description automatically
generated](.//media/image29.png)

One view that is useful, is to see all error messages for the
application. While on the 'ALL' view, select **Error** as the logging
level.

![Text Description automatically
generated](.//media/image30.png)

Now, save this view as new view, by selecting Save a new view/alert.

![Graphical user interface, text, application, email Description
automatically generated](.//media/image31.png)

Enter, **ERRORS -- ALL** as the name of the view. For errors, we want to
be notified when an error occurs. To so, under Alert, select
**View-specific alert**.

![Graphical user interface, text, application Description automatically
generated](.//media/image32.png)

When you create an alert, you have four choices out of the box, email,
slack, pager duty, and a webhook.

![Graphical user interface, application Description automatically
generated](.//media/image33.png)

For this tutorial, we will be creating simple email alert. To do so,
I'll click on **email** and then enter my email address. You can also
see options of aggregating alerts and log messages together to limit the
number of emails you receive.

![Graphical user interface Description automatically
generated](.//media/image34.png)

I will just keep the default settings and click on **Save View**. Now,
when there is an error coming from the tutorial application running on
my cluster, I will be alerted with the log message.

1 - 4 Creating Logging Graphs and Boards
-----------------------------------------

Another of the great features of LogDNA is the ability to create a
graphical representation of your log messages. For example, we can
create a graph that shows error messages over a period of time.

A board is a named collection of graphs. Boards can be found in
the [Log-viewer
sidebar](https://docs.logdna.com/docs/how-to-use-the-dashboard#log-viewer-sidebar-left) on
the left. Changes made to a board are automatically saved. Similar to
views, all of your users have access to all boards in your account.

You can view more about boards from the LogDNA website.

<https://docs.logdna.com/docs/graphs>

In this tutorial, we are going to create a couple of different views.

Total API Calls for the application itself.

Number of calls per microservice.

To start with, click on the **Boards icon** on the left hand navigation
pane, click **New Board** and then the **edit icon** next to New Board.

![Graphical user interface, application Description automatically
generated](.//media/image35.png)

On the next screen, enter **API Calls** as the name and create a new
category **called DEV Zero To Cloud Native** as the category name and
then '**Add this as a new board category**', and then click **Save**.

![Graphical user interface, text, application, email Description
automatically
generated](.//media/image36.png)

Next, we are going to add a graph to display the number of API calls.
Click on **Add Graph**.

![A picture containing graphical user interface Description
automatically
generated](.//media/image37.png)

Next, in the Field selection, **select tag**.

![Graphical user interface, text, application Description automatically
generated](.//media/image38.png)

Next, select the tag for the tutorial application**,
api.zero-to-cloud-native.com** and then click **add graph**.

![Graphical user interface, application Description automatically
generated](.//media/image39.png)

At this point, we are showing a graph showing the number for all log
messages for the application.

Looking into python code under **app/api-frontend-02cn.py** file under
the api-frontend-02cn repository, scroll down to one of the APIs such as
**EnableSSH**. You will notice that each time an API is called, we are
creating a log message that starts with '**Zero to Cloud Native API
Starting'**

![Text Description automatically
generated](.//media/image40.png)

To change the graph to count the number of api calls, create an
additional filter by clicking on the **graph filter icon**.

![Graphical user interface, text Description automatically
generated](.//media/image41.png)

On the next screen, enter '**Zero to Cloud Native API Starting**' as the
search string and hit enter.

![Graphical user interface Description automatically
generated](.//media/image42.png)

Next, to test the graph, execute one of the APIs such as get OCP Token.
After you run the API, click on the **Live button**.

![Graphical user interface Description automatically
generated](.//media/image43.png)

You will notice the graph was updated to show that 1 API call was just
made.

![Graphical user interface Description automatically
generated](.//media/image44.png)

Depending on how many APIs you have run in your tutorial application,
you will see different results. To view the graph over several days as
an example, click on the 60 MINS and change this to **7 days**.

![A picture containing graphical user interface, calendar Description
automatically
generated](.//media/image45.png)

You can see my example, I called one of the APIs a number of times last
Friday and now just a couple today.

![A picture containing graphical user interface Description
automatically
generated](.//media/image46.png)

Now I can see how the number of APIs are trending over time. While this
is interesting, viewing just the number of times doesn't give us a view
on how the application is performing. What would be more insightful is
adding the number of errors to this view.

We can do that by clicking on **Add Plot**.

![A picture containing graphical user interface Description
automatically
generated](.//media/image47.png)

On the next screen, select **level**.

![Graphical user interface, text, application Description automatically
generated](.//media/image48.png)

Select error and then **save**.

![A picture containing graphical user interface Description
automatically
generated](.//media/image49.png)

Now looking at the graph, I can see that I had an error last Friday when
there was a higher load.

![A picture containing graphical user interface Description
automatically generated](.//media/image50.png)
Clicking on the error graph where there was an error message, I can then
click on **Show Logs** to see what caused the error.

![Diagram Description automatically
generated](.//media/image51.png)

This takes me in-context to where log messages where the error occurred.

![Graphical user interface, text Description automatically
generated](.//media/image52.png)

Again, you will have different log messages. As extra credit, you can
modify the code and introduce errors if you would like to see similar
log messages.

Next, let's create one more board showing the number of times the
microservices are called. Because the load OCP versions microservice is
a cronjob and runs every 5 minutes tracking the number of times this
microservice is called is not that interesting or insightful. We will
create a new board that will count the number of times the following
microservices are invoked:

-   Enable SSH

-   Utility

-   OpenShift Realtime

-   Frontend API

To start with, click on **New Board** and then **Edit Board** to change
the name.

![Graphical user interface, application Description automatically
generated](.//media/image53.png)

Enter **Number of Micro-Services Calls** as the name and select '**DEV
Zero to Cloud Native**' as the Category, then click **Save**.

![Graphical user interface, text, application, email Description
automatically
generated](.//media/image54.png)

Next, click on **Add Graph**.

![Graphical user interface, text Description automatically
generated](.//media/image55.png)

On the next screen, enter **app** as the field, select
**ocp-realtime-02cn** as the field value. Click on **Advanced
Filtering** and enter '**Starting Openshift Realtime**' to limit the log
messages so we can get a count of the number of times this microservice
is called. After entering all of these settings, click on **Add Graph**.

![Graphical user interface, application Description automatically
generated](.//media/image56.png)

Next, we need to enter additional plots of the remaining microservices.

Starting with Enable SSH on Worker Nodes, click on **Add Plot**.

For enable node ssh enter:
Field: **app**  
Field Value: **enable-node-ssh-02cn**  
Advanced Filtering: **Enabling SSH on Worker Nodes**  
Click **Save**  

![Graphical user interface, text Description automatically
generated](.//media/image57.png)

Repeat the same process for Utility Microservice.

Click on **Add Plot**  

For utility enter:
Field: **app**  
Field Value: **utility-02cn**  
Advanced Filtering: **Starting Zero To Cloud Native Utility**  
Click **Save**  

Repeat the same process of the API Frontend Microservice.

Click on **Add Plot**  

For api frontend:
Field: **app**  
Field Value: **api-frontend-02cn**  
Advanced Filtering: **Zero to Cloud Native API Starting**  
Click **Save**  

Depending on how many APIs you have made, you will see a graph showing
how many times each of the four APIs were called.

![Chart, histogram Description automatically
generated](.//media/image58.png)

Hovering over the graph, you can view specifics of how many times each
API was called.

![Chart, histogram Description automatically
generated](.//media/image59.png)

Clicking on one of the plots you will also have the option to hide the
plot from the graph to further dive into deals of the graph.

![Graphical user interface, text, application Description automatically
generated](.//media/image60.png)

1 - 5 Logging Metrics via Screens
----------------------------------

The last visibility tool we will go through with LogDNA is Screens.
LogDNA Screens let you create detailed customized dashboards using your
log data. Screens provide several configurable widgets, which display
aggregate metrics about logs over a given period of time. You can use
Screens to visualize overall log volume, key performance metrics (KPIs),
and more. You can view more about screens on the LogDNA site:

<https://docs.logdna.com/docs/screens>

To create a new screen, click on the **Screens icon**, and then **New
Screen**.

![Graphical user interface, application Description automatically
generated](.//media/image61.png)

The first screen we will build is a gauge showing how many API calls we
have processed.

Click on **Add Widget** and select **count**.

![Graphical user interface, text, application Description automatically
generated](.//media/image62.png)

The gauge will now show the total number of logging lines, which is not
that helpful. To display the number of API calls, we will need to
customize the view.

Click on the gauge and you will be able to customize the view on the
right hand side.

![A picture containing background pattern Description automatically
generated](.//media/image63.png)

As we did when we created a board showing the total number of API calls,
enter the following values:

Field: **tag**  
Field Value: **api.zero-to-cloud-native.com**  
Advanced Filtering: **Zero to Cloud Native API Starting**  
Duration: **Last 1 Week**  

Note: your field value should refer to the domain you created.

![Graphical user interface, application Description automatically
generated](.//media/image64.png)  
Next, we will customize how the guage looks. Scrolling down enter the
following:

Units: **Total API Calls**  
Label: **Total API Calls**  
Maximum: **50**  

![Graphical user interface, application Description automatically
generated](.//media/image65.png)

Depending on how many API calls you have made in the last week, your
gauge should look like this.

![Graphical user interface, application Description automatically
generated](.//media/image66.png)

Next, let's add a simple count of the number of API calls that failed.
Click on **Add Widget** and then **Count**.

![Graphical user interface, application Description automatically
generated](.//media/image67.png)

Just like we did before, we will need to customize the count view. By
default, the count shows the total number of log lines. Before we
customize the view, let's move the count of errors next to the total
number of api calls. **Click and drag** the count widget you just
created next to the gauge. Then click on the count widget to customize
it.

![Graphical user interface, application Description automatically
generated](.//media/image68.png)  
On the right-hand side of the screen, enter the following information:

Field: **level**  
Field value: **error**  
Advanced Filtering: **tag:api.zero-to-cloud-native.com AND Problem**  
Duration: **Last 1 Week**  

![Graphical user interface, application Description automatically
generated](.//media/image69.png)

Lastly, we will customize the view, enter the following values:

Units: **Errors**  
Label: **Errors**  

![Graphical user interface, application Description automatically
generated](.//media/image70.png)

Hopefully, your error will show zero meaning your application hasn't
encountered any problems. While making a couple changes to the code base
last week, I had a problem and show 1 error.

![Graphical user interface, application Description automatically
generated](.//media/image71.png)

Next, we will create another view, this time a table showing the number
of calls to each microservice. Click on **Add Widget** and choose
**Table**.

![Graphical user interface, text, application, email Description
automatically
generated](.//media/image72.png)

Click on the empty table to customize it:

![A screenshot of a video game Description automatically
generated](.//media/image73.png)

On the right-hand side, enter the following information:

Group By: **app**  
Field: **All Lines**  

Advanced Filtering:

**(app:ocp-realtime AND Starting Openshift Realtime) OR**  
**(app:utility-02cn AND Starting Zero To Cloud Native Utility) OR**  
**(app:api-frontend-02cn AND Zero to Cloud Native API Starting) OR**  
**(app:enable-node-SSH-02cn AND Enabling SSH on Worker) OR**  
**(app:load-OCP-versions-02cn AND Starting to load)**  

Duration: **Last 1 Week**

![Graphical user interface, application Description automatically
generated](.//media/image74.png)

Next, we need to customer the view:

Number of Rows: **5**

Appearance:
Table Label: **Number of microservices calls**  
Left Column Label: **Microservice**  
Right Column Label: **Calls**  

![Graphical user interface, application Description automatically
generated](.//media/image75.png)

Finally, to make the table look nice, resize the table so it fits under
the Total API Callas and Errors Widgets.

Your table should look like this:

![Graphical user interface, application, Teams Description automatically
generated](.//media/image76.png)

The last widget we are going to create is a graph showing the total
number of microservice calls compared week to week.

Click on **Add Widget** and then select **Time Shifted Graph**.

![Graphical user interface, text, application Description automatically
generated](.//media/image77.png)

Click on the graph to customize it:

![Chart Description automatically
generated](.//media/image78.png)

Enter the following values:

Field: **app**  
Field Value: **ANY**  

Advanced Filtering:
**(app:ocp-realtime AND Starting Openshift Realtime) OR
(app:utility-02cn AND Starting Zero To Cloud Native Utility) OR
(app:api-frontend-02cn AND Zero to Cloud Native API Starting) OR
(app:enable-node-SSH-02cn AND Enabling SSH on Worker) OR
(app:load-OCP-versions-02cn AND Starting to load)**  

Operation: **Counts**  
Duration: **Today vs Yesterday**  
Label: **Microservices Calls**  

![A screenshot of a cell phone Description automatically
generated](.//media/image79.png)
The resulting graph should look something like this:

![Chart, histogram Description automatically
generated](.//media/image80.png)

To make this screen look nice, drag the time series graph next to the
other graphs and expand it for better readability. Your screen should
now look something like this.

![A picture containing graphical user interface Description
automatically generated](.//media/image81.png)

The last thing we need to do is save the screen. Click on **Save
Screen**. Enter **Overview** for the Name, **DEV Zero to Cloud Native**
( and click **Add this as a new screen category** ), finally click
**Save**.

So now we have a good graphical overview of which APIs are being called,
how many errors occurred and some insight into the actual microservices
that are being called as well.

Keep in mind, this is just an example of what we can do with LogDNA. In
a future session, we will dive into RedHat Service Mesh and gain much
more visibility into our microservices.

Now we have our logging dashboard setup so we can see what is going with
our application both at the API and micro-service level and we are
getting alerts to when something goes wrong with the application. In the
next section, we will go through how to export log entries for users to
see the status of their long running API requests.
