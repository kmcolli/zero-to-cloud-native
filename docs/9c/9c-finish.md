Zero to Cloud-Native with IBM Cloud
===================================

**Kevin Collins ( kevincollins@us.ibm.com )**  
**Kunal Malhotra ( kunal.malhotra3@ibm.com )**  

Part 9C: Finish Deploying and Testing the Application
=====================================================

1 - Create remaining Pipelines
----------------------------

If you followed parts 9A and 9B, you created toolchains for the
**frontend api** and **frontend web** microservices. Next, you will need
to create additional pipelines for the following microservices:

-   ocp-realtime-02cn

-   enable-node-ssh-02cn

-   load-ocp-versions-02cn

-   utility-02cn

Choose your favorite method from with Classic or Tekton to create the
remaining delivery pipelines.

After you have created all your delivery pipelines, make sure all of
your pods are running. Log into your OpenShift cluster using the
terminal and run:

```
oc get pods
```

![A close up of text on a black background Description automatically
generated](.//media/image1.png)

Your output should look like the above. Note that the
load-ocp-versions-02cn microservice is a cronjob that runs every 5
minutes. You may see a different number of completions.

## 2 - Configure Cloud Internet Service

Part of the deployment.yaml files that were run from the frontend api
and web delivery pipelines created a load balancer service. RedHat
OpenShift on IBM Cloud will automatically create a VPC load balancer
when you indicate in your deployment file that you want to create a load
balancer service.

What we will need to do is map the load balancer url to a DNS entry for
the domain we created with IBM Cloud Internet Services. The first thing
we need to do is find the load balancer external IP.

To do so, log into your OpenShift cluster using the terminal. Once you
are logged in, switch to the zero-to-cloud-native projects and list all
the services using the following commands:

```
oc project zero-to-cloud-native

oc get svc
```

![A close up of a screen Description automatically
generated](.//media/image2.png)

This will output 4 services.

**api-frontend-02cn-service** --external load balancer service for the
api frontend.

**ocp-realtime-02cn-service** -- this is an internal service for the ocp
realtime microservice. This means the microservice is only available
within the cluster. In our case, only the api-frontend will call this
service.

**utility-02cn-service** -- another internal service that provides
common APIs that the other microservices will call.

**web-frontend-02cn-service** --external load balancer service for the
web frontend.

Starting with the api frontend microservice, copy the external ip that
you retrieved when you listed all of your services. In this case,
**2f9437d3-us-south.lb.appdomain.cloud.** Note, you will have a
different value.

Next, we need to create a DNS CNAME in Cloud Internet Services, mapping
this external ip to the domain we created. From you IBM Cloud dashboard
click on Services.

![A screenshot of a cell phone Description automatically
generated](.//media/image3.png)

Next, click on your Cloud Internet Services for the zero to cloud native
tutorial.

![A screenshot of a cell phone Description automatically
generated](.//media/image4.png)

On the next page, click on DNS Records.

![A screenshot of a social media post Description automatically
generated](.//media/image5.png)

To create a DNS entry, we are going to create a CNAME DNS record.

**Type:** CNAME

**Name:** Past in the value you copied from the
zero-to-cloud-native-api-service

**Alias Domain:** api.\<the domain CIS is managing\>

See an example below:

![A screenshot of a cell phone Description automatically
generated](.//media/image6.png)

Repeat the same steps for web frontend service. After you are done, you
should see two new DNS entries.

![A screenshot of a cell phone Description automatically
generated](.//media/image7.png)

Congratulations!! You have now successfully deployed and configured the
zero to cloud native application. Now let's test it!

3 - Test the application
-------------------------

As we have discussed, there are two frontend interfaces to the zero to
cloud native tutorial application. A traditional web frontend and an API
frontend.

### 3 - 1 Testing the API Application

To test the API interface, I will be using Postman which you installed
in Part 5. Feel free to use any other API tool or even the curl command
if you like.

#### 3-1-1 Get OCP Versions

Start by testing the get OCP Versions API. Open the PostMan application,
change to a GET call, and enter your host name. If you followed the same
naming conventions, your host name of this API should be api.\<the
domain you created in part 3\>:8000/api/v1/getOCPVersions/

In my case, the host for this API is:

https://api.zero-to-cloud-native.com:8000/api/v1/getOCPVersions/

![A screenshot of a social media post Description automatically
generated](.//media/image8.png)

After entering this hostname, you will get a JSON response like this:

![A screenshot of a cell phone Description automatically
generated](.//media/image9.png)

The output will contain the current version of RedHat OpenShift that are
supported by IBM.

#### 3-1-2 Get OCP Token

Next, we will test retrieving a token to log into OpenShift. Just like
you did to test the get OCP versions API, select **POST**, keep the
domain you had above, but change the endpoint to getOCPToken.

In my example, the url is:

https://api.zero-to-cloud-native.com:8000/api/v1/getOCPToken/

For this API, we will need to pass a couple of variables, an IBM Cloud
API Key for the account where the cluster that you want to retrieve the
OCP login token is in.

Under the Body tab, click on raw and on the right, select JSON. Then in
the input window enter JSON in this format:

```
{
    "apikey": "<IBM CLOUD API KEY>",
    "cluster_name": "e.g. zero-to-cloud-native"
}
```
![A screenshot of a social media post Description automatically
generated](.//media/image10.png)

After entering all of these settings, click Send. This will return a
JSON response like the following:

![A screenshot of a cell phone Description automatically
generated](.//media/image11.png)

Now, let's test the login command to see if we can log into our
OpenShift cluster. Copy the login field:

```
oc login --token=YLv3cL3tMdJVTbM1gbpruc-sdqX8xSlt24qD5dFTg88 --server=https://c107-e.us-south.containers.cloud.ibm.com:31899
```
Start your iTerm terminal and paste the command and hit enter.

![](.//media/image12.png)

You should see output like the above. Now when you want to log into your
OpenShift cluster, you can just run this API and don't have to log into
the IBM Cloud and then the OpenShift cluster.

Note, you can also call the API using curl from the command line.

Example:

```
curl -X POST -H "Content-Type: application/json" "https://api.zero-to-cloud-native.com:8000/api/v1/getOCPToken/" -d '{"apikey": "IFvxsWmtv0r3IDDHJAJAHJHAJHJHJHHGres24uByKbc12H", "cluster_name": "zero-to-cloud-native"}'
```

![](.//media/image13.png)

Note, the example above uses a temporary API that has been removed.

#### 3-1-3 Enable SSH

Generally speaking, we don't recommend SSHing directly into OpenShift
worker nodes, however when you are doing advanced configuration and
debugging, you might find that you need to. With IBM's Managed OpenShift
Service, you can but there are a number of steps that you have to take
that are rather cumbersome. You can find the manual instructions here:

<https://developer.ibm.com/technologies/containers/tutorials/ssh-connect-nodes-kubernetes-cluster-openshift/>

As I find myself doing this frequently, I have automated it through the
enableSSH API. Let's test it.

Following the same steps as we did with get OCP Token, you will select
POST, change the endpoint to enableSSH. The JSON input take the same
parameters, your IBM Cloud apikey and the cluster_name.

https://api.zero-to-cloud-native.com:8000/api/v1/enableSSH/

```
{
  "apikey": "<IBM CLOUD API KEY>",
  "cluster_name": "e.g. zero-to-cloud-native"
}
```

![A screenshot of a cell phone Description automatically
generated](.//media/image14.png)

After entering these settings, click Send. You will get output like the
following:

![A screenshot of a cell phone Description automatically
generated](.//media/image15.png)

Note, this API is long running ( \~10 seconds ). Following the CQRS
Command microservices pattern, the output will indicate that the request
to enable SSH has been accepted. The reason for this is we don't want
the frontend API to block other requests while this request completes.
In the logging section, we will go through how you and your users can
find out the status of the request.

For now, let's test if we can in fact SSH into a worker node. What the
API will do, is it will created 'inspect' pods that will give us access
to the underlying worker node the pod is running on.

Using **iTerm2**, log into your OpenShift cluster. Use the getToken API
if you like! After logging in, run:

```
oc get pods -n kube-system
```

![A screenshot of a cell phone Description automatically
generated](.//media/image16.png)

Note the nodes that start with inspect. You will see the pods follow the
naming convention of:  
 **inspectnode-\<cluster id\>-\<clustername\>-\<worker pool\>-\<worker id\>**

Following this naming convention will allow us to easily identify which
pod is associated with which worker node.

Now let test it. Pick a pod and copy the pod name, I will choose  
**inspectnode-6sqohaugmvg-zerotocloud-default-00000a63**

Next, we are going to 'exec' in the pod by running this command. Make
sure to use the name of the pod you just copied.

```
kubectl exec -it inspectnode-6sqohaugmvg-zerotocloud-default-00000a63 /bin/sh -n kube-system
```

Your prompt should now change to **\#** ... indicating you are now
connected inside the inspect pod.

Next, to SSH in the worker node type:

```
ssh root\@localhost
```

You should see your prompt now change to something like this:

**\[root\@kube-bt2nhevd06sqohaugmvg-zerotocloud-default-00000a63 \~\]\#**

Congratulations, you are now SSH'd into one of your worker nodes. BE
CAREFUL!

![A screenshot of a cell phone Description automatically
generated](.//media/image17.png)

4 -- Testing the Web Application
================================

The last step we want to do is test the web frontend of the application.
To do so navigate to:

web.\<your domain\>. In my case web.zero-to-cloud-native.com

![A screenshot of a social media post Description automatically
generated](.//media/image18.png)

Enter your apikey and cluster name for the Get OpenShift Token and
Enable SSH APIs and click each api to execute the api and get a result.

![A screenshot of a social media post Description automatically
generated](.//media/image19.png)

5 -- Next Up
============

If you have made it this far, congratulations you have successfully
deployed a cloud-native application end-to-end. I hope you have learned
a lot and have found this tutorial to be useful. This completes phase 1
or in other words day 1.

Stay tuned for future sessions that will go through day two operations
like logging, monitoring, deploying to a remote location, automating and
optimizing the environment. This will be a living series where we will
be adding new sessions covering a variety of topics. As always, we would
love your feedback and suggestions on future sessions.
