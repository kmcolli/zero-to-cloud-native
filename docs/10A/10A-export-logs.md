Zero to Cloud-Native with IBM Cloud
===================================
**Kevin Collins ( kevincollins@us.ibm.com )**  
**Kunal Malhotra ( kunal.malhotra3@ibm.com )**

Part 10A: Export Logs 
=====================



1 - Introduction 
================

In part 10 of this tutorial series, you created a logging dashboard with
several logging views and graphical representations of both APIs and
Microservices that make up our application. In this part, we will be
exporting specific log messages so that our users can understand what is
happening with their long running API requests.

1 - 1 Enable Node SSH
----------------------

Enable Node SSH is our long running API task that will immediately
return that the request to enable ssh on worker nodes was received.
Since it is a long running task, we don't want to block other requests
for this one to complete.

How can users determine if their request was actually successful? One
easy way is with LogDNA! We can export relevant logging lines to a user
so they can see what is happening in the backend. As you will remember,
when you submit the request to enable SSH a request id is returned.

![A picture containing graphical user interface Description
automatically
generated](.//media/image2.png)Using that Request id, we can go to our logDNA dashboard and search for
that request id to see what happened.

![Text Description automatically
generated](.//media/image3.png)

You can see here a number of messages both at the Debug and Info level.

When our microservices create a log entry, we specify the type; such as
Info, Debug, Error, etc

![](.//media/image4.png)

![](.//media/image5.png)

By having different levels of logging messages we can then filter which
level you want to see. In our case, if we are sharing log messages with
end users, we will only want to show them the high-level logging ( INFO
) messages.

2 - Customizing LogDNA to Export Logs
======================================

Before we can actually export logs from LogDNA, there are a couple of
things we need to do with LogDNA. The first thing is to add the domain
you created to a list of whitelisted domains that have access to LogDNA.

2 - 1 Whitelist your Domain
--------------------------

From your LogDNA dashboard, click on the settings Icon.

![Graphical user interface, application, Teams Description automatically
generated](.//media/image6.png)

Next, click on Organization and then Whitelist Domains.

![Graphical user interface, application Description automatically
generated](.//media/image7.png)

Next, you will need to add the domain you created to a list of domains
that will permitted to make requests to LogDNA servers.

I will add the domain and the status application we will deploy next,
**https://status.zero-to-cloud-native.com** and then click on **Add
Domain**. Note: you will need to substitute zero-to-cloud-native with
the domain you previously created.

![A picture containing background pattern Description automatically
generated](.//media/image8.png)

### 

2 - 2 Create a Service Key
-------------------------

Next, we will need to get a service key for our LogDNA instance. To do,
click on API Keys from the navigation tab and then click on Generate
Service Key.

![Graphical user interface, application, Teams Description automatically
generated](.//media/image9.png)

Copy the resulting Service Key into your clipboard.

![Graphical user interface, text, application, Teams Description
automatically generated](.//media/image10.png)

2 - 3 Base64 Encode Service Key
-----------------------------

Next, we need to convert this key to base64. Start iTerm2 and run the
following command. Note: make sure to add a : at the end of service key
you copied.

```
echo -n <logdna service key>: | base64
```

![Graphical user interface, text, application, Teams Description
automatically generated](.//media/image11.png)

Copy the result in a safe place, you will need this later.

3 - Status Application
=======================


In order to provide a way for users to view log messages, we will need to first create an additional microservice / web application. This application will simply show log messages after being given a request id. We will then update the API application to include a link to the log messages after the API has been called.

This is a view of the status app. You can see the request id was given as a parameter in the URL.

![Graphical user interface, application Description automatically generated](.//media/image12.png)

This is just a very simply application to give you a feel for what we can do with exporting logs with LogDNA.

3 - 1 Cloning the Status Repository
------------------------------------

**Status-02cn** -- is the status web application that will return 'Info'
log messages when given a request id. You can find the GitHub repository
here:

<https://github.com/kmcolli/status-02cn>

Following the same steps in Part 8, section 2 of this tutorial series, we will first clone the repository and create our own git repository.

Start your iTerm2 terminal and make sure you are in the directory you created that contains all of your git repositories.

Next, clone the status repository.

```
git clone <https://github.com/kmcolli/status-02cn>
```
Go to github.com and create a new repository called **status-02cn**. You can view the directions if needed in Part 8, Section 2-2.

Import the code you just cloned into this new repository and then create
a new **'dev' branch.** If you need a refresher on doing this, refer to
the instructions in Part 8 -- Section 5

Now that we have the source code in a dev branch, we can create a CI/CD
pipeline for this new micro-service web application, but before we push
the code with our CI/CD pipeline, we need to customize this microservice
a little.

3 - 2 Customizing the Status Microservice
------------------------------------------

Because the status application is web facing, the next thing you will
need to do is add the certificates for the domain you created to the
nginx container that the status microservice uses.

To do so, just **copy the certificates** from api-frontend-02cn to the
certs directory under the status-02cn repository.

![A picture containing drawing Description automatically
generated](.//media/image13.png)

Next, we need to update the **js/script.js** file in the status-02cn
repository. To do so, open Visual Studio Code and open the script.js
file in the status-02cn repository.

Note: If you haven't already, you will need to add the status-02cn
folder to your workspace like you did for the other repositories.

### 

![Graphical user interface, application Description automatically
generated](.//media/image14.png)

Depending on where you deployed your LogDNA instance, you may need to
update the url that will be called. If you deployed in Dallas, you can
leave the URL as is that refers to the us-south region.

You will need to update the value in the blue box below. This value
should be the base64 encoded value you retrieved in step 2-3.

![A close up of a screen Description automatically
generated](.//media/image15.png)

### 

4 - Create Status CI/CD pipeline
================================

Following best practices, we will create a CI/CD pipeline for this new
micrservice. To create this new CI/CD pipeline, choose from either a
classic pipeline ( Part 9A ) or a Tekton pipeline ( Part 9B ).

I will be creating a Tekton pipeline and will provide the high-level
instructions here. If you need a refresher on the entire process, refer
to Part 9A of this tutorial series.

4 - 1 
-----

Start by opening your **dev-zero-to-cloud-native toolchain**.

![Graphical user interface, application Description automatically
generated](.//media/image16.png)

4 - 2 
-----

Add a GitHub tool to the toolchain and specify the **status-02cn**
repository you just created.

![Graphical user interface, text, application, email Description
automatically
generated](.//media/image17.png)

4 - 3 
-----

Add a Delivery Pipeline tool to your toolchain, giving it the name of
**dev-tekton-status-02cn** and type **Tekton**.

![Graphical user interface, text, application Description automatically
generated](.//media/image18.png)

4 -4
----

Specify the definition of the repository by selecting the **dev branch**
of the **status-02cn repository** you created.

![Graphical user interface, text, application Description automatically
generated](.//media/image19.png)

4 - 5
-----

Select the **IBM Cloud Managed Worker**.

![Graphical user interface, text, application, Teams Description
automatically
generated](.//media/image20.png)

4 - 6
-----

Add a **Git Repository trigger** for the status repo and **dev branch**.

![Graphical user interface, application, Teams Description automatically
generated](.//media/image21.png)

4 - 7
-----

Enter your **environment variables**.

![Table Description automatically
generated](.//media/image22.png)

4 - 8
-----

Commit a change to your GitHub repo and watch the pipeline run. You
should see that it was successful.

![Graphical user interface, application Description automatically
generated](.//media/image23.png)

5 - Configure Cloud Internet Services
======================================

Next, we need to map that status application in Cloud Internet Services
so we can expose the application to our domain.

If you look in the deployment file for the status-02cn application, you
will see that we create a load balancer service. We need to map the load
balancer we just created to our Cloud Internet Services domain.

Start your iTerm2 terminal, log into your OpenShift Cluster, and make
sure you are in the zero-to-cloud-native project. Next, run **oc get
svc** to retrieve a list of services. Note the 'External-IP' for the
status-02cn service.

![A picture containing text, website Description automatically
generated](.//media/image24.png)

Going back to your browser, click on your Cloud Internet Services
instance from the IBM Cloud portal and click on **DNS Records**.

![Graphical user interface, application, Teams Description automatically
generated](.//media/image25.png)

As we did before, create a new **CNAME** record. Enter **status** as the
name and paste the **load balancer URL** you retrieved in the previous
step.

![Graphical user interface, application Description automatically
generated](.//media/image26.png)

6 - Customizing the API Microservice
-------------------------------------

The last thing we need to do before we can test the status application
is modify the API frontend application to return the status URL that a
user can open to view log messages for their API requests.

From Visual Studio Code open the **api-frontend-02cn.py** file under in
the app directory of the api-frontend-02cn repository.

![Graphical user interface, application Description automatically
generated](.//media/image27.png)

Update all three APIs to return an additional field named View Logs.

In the **class GetOCPVersions** update the return blocks of code as
follows:

```
class GetOCPVersions(Resource):
    def get(self):
        try:
            reqid=getRequestId()
            app.logger.info("{} Zero to Cloud Native API Starting Get OCP Versions.".format(reqid))
            
            headers = { "Content-Type": "application/json" }
            port = os.environ.get("OCP_REALTIME_02CN_SERVICE_SERVICE_PORT")
            url = "http://"+os.environ.get("OCP_REALTIME_02CN_SERVICE_SERVICE_HOST")
            openshift_realtime_url = url+":"+port+"/api/v1/getOCPVersions/"
            data={ "reqid": reqid}
            response = requests.get(openshift_realtime_url,headers=headers,data=json.dumps(data))
            versions=response.json()
            app.logger.info("{} Successfully got these ocp versions {}".format(reqid, versions)) 
            return { 
                "versions": versions,
                "View Logs": "https://status.zero-to-cloud-native.com/?reqid="+reqid
            }
        except Exception as e:
            app.logger.error("{} Error Zero to Cloud Native API getting OCP versions  {}".format(reqid, e))
            return {
                "Status":"Problem getting roks versions for request id "+reqid,
                "View Logs": "https://status.zero-to-cloud-native.com/?reqid="+reqid
            }
```
![Text Description automatically
generated](.//media/image28.png)

GetOCPToken:

```
class GetOCPToken(Resource):
    def post(self):
        try:
            input_json_data = request.get_json()
            reqid=getRequestId()
            app.logger.info("{} Zero to Cloud Native API Starting Get OCP Token.".format(reqid))
            apikey = input_json_data['apikey']
            clustername = input_json_data['cluster_name']
            
            headers = { "Content-Type": "application/json" }
            port = os.environ.get("OCP_REALTIME_02CN_SERVICE_SERVICE_PORT")
            url = "http://"+os.environ.get("OCP_REALTIME_02CN_SERVICE_SERVICE_HOST")
            openshift_realtime_url = url+":"+port+"/api/v1/getOCPToken/"
            data={ "reqid": reqid,
                   "apikey": apikey,
                   "clustername": clustername}
            response = requests.get(openshift_realtime_url,headers=headers,data=json.dumps(data))
            token = response.json()["token"]
            server = response.json()["server"]
            app.logger.info("{} Successfully got ocp token.".format(reqid))
            return { "Status": "Successfully got ocp token",
                     "token": token,
                     "login": "oc login --token="+token+" --server="+server,
                     "View Logs": "https://status.zero-to-cloud-native.com/?reqid="+reqid
                    }
        except Exception as e:
            app.logger.error("{} Zero to Cloud Native API Starting Get OCP token  {}".format(reqid, e))
            return {
                "Status":"Problem getting ocp token for request id "+reqid,
                "View Logs": "https://status.zero-to-cloud-native.com/?reqid="+reqid
            }
```

![Text Description automatically
generated](.//media/image29.png)

Enable SSH

```
class EnableSSH(Resource):
    def post(self):
        try:
            input_json_data = request.get_json()
            reqid=getRequestId()
            app.logger.info("{} Zero to Cloud Native API Starting enable SSH.".format(reqid))
            apikey = input_json_data['apikey']
            clustername = input_json_data['cluster_name']

            message = { "reqid": reqid,
                        "action": "enableSSH",
                        "APIKEY": apikey,
                        "CLUSTER_NAME": clustername
                        }
            app.logger.debug("{} Sending real time message to enable SSH.".format(reqid))
            json_message = json.dumps(message)
            realtimemessage(app.config["RABBITMQ_QUEUE"], json_message ) 
            app.logger.info("{} Successfully requested enable SSH".format(reqid))    
            return {
                "Status":"Successfully requested enable SSH. Request id = "+reqid,
                "View Logs": "https://status.zero-to-cloud-native.com/?reqid="+reqid
            }
        except:
            app.logger.error("{} Problem requesting enable SSH".format(reqid))
            return {
                "Status":"Problem requesting enable SSH. Request ID = "+reqid,
                "View Logs": "https://status.zero-to-cloud-native.com/?reqid="+reqid
            }
```
       
![Text Description automatically
generated](.//media/image30.png)

After making those changes, **commit** both the **api frontend** and the
**status** microservices to GitHub. After committing those changes,
navigate to your toolchain and watch the pipelines run that will build
and deploy the changes you just made.

![Graphical user interface, application, Teams Description automatically
generated](.//media/image31.png)

7 -- Testing the Status Application
===================================

Pick your favorite API and run it. In my case, I ran the Enable Node SSH
API and got the following response:

![Graphical user interface, application Description automatically
generated](.//media/image32.png)

Copy and paste the URL into your browser to see logs for the request.

![A picture containing text Description automatically
generated](.//media/image33.png)

Now, by simply using LogDNA, end users can see the status of their
'long' running API requests!

This completes the session on LogDNA. I hope these two sessions give you
a good overview on why IBM has standardizing logging on IBM Cloud with
LogDNA and you understand the type of things you can do with LogDNA.
Check out our official documentation on Log Analysis with LogDNA to
learn even more what LogDNA can do.

[Getting Starting with LogDNA](https://cloud.ibm.com/docs/Log-Analysis-with-LogDNA?topic=Log-Analysis-with-LogDNA-getting-started#getting-started) 
