Zero to Cloud-Native with IBM Cloud
===================================
**Kevin Collins ( kevincollins@us.ibm.com )**  
**Kunal Malhotra ( kunal.malhotra3@ibm.com )**

Part 7: Preparing to Deploy the Application 
===========================================


Now that we have provisioned all the IBM Cloud Services our application
will use and we have a solid development environment setup, we can now
configure all the services so we can deploy our tutorial application.

## 1 - RedHat OpenShift on IBM Cloud

Starting with RedHat OpenShift on IBM Cloud, there are a couple of
things we need to do to prepare our cluster to deploy our application.

To begin with, we are going to install IBM Log Analysis with LogDNA and
IBM Monitoring with SysDig. Future sections will go through a deep dive
on using both LogDNA and SysDig. This section will just go through the
install process so logs and metric data will start flowing to LogDNA and
SysDig.

To get started, we are going to navigate to our OpenShift cluster. From
the IBM Cloud Console, click on the hamburger menu -\> OpenShift -\>
clusters.

![A screenshot of a cell phone Description automatically
generated](.//media/image1.png)

The next screen will show a list of all your clusters. Click on the
zero-to-cloud-native cluster.

![A screenshot of a cell phone Description automatically
generated](.//media/image2.png)

The next screen will show an Overview of your cluster. You can high
level information about the status of your cluster, CPU and memory
usage.

## 1 - 1 - Configure Logging

We will start by configuring logging for our cluster. IBM Cloud
OpenShift has made it incredibly easy to enable Logging and Monitoring
with tight integration to both services. Starting with enabling logging,
click on 'Connect' next to Logging.

![A screenshot of a cell phone Description automatically
generated](.//media/image3.png)

On the next screen you will have an option to either create new instance
of IBM Log Analysis with LogDNA or create a new instance. We will be
creating a new instance, click on create an instance.

![A screenshot of a cell phone Description automatically
generated](.//media/image4.png)

On the next screen, enter Dallas or the same region you have been using
throughout the tutorial.

Select 7 day Log Search as we will be going through more advanced
features of LogDNA such as integrating alerts with Slack in future
tutorials.

Give the service a meaningful name, I will use IBM Log Analysis with
LogDNA-zero-to-cloud-native.

Finally, make sure to select the zero-to-cloud-native resource group we
have been using throughout the tutorial.

![A screenshot of a social media post Description automatically
generated](.//media/image5.png)

After entering all these settings click create.

You will now see on the cluster overview screen that you can launch the
Logging dashboard. Future tutorials will go through how to use IBM Log
Analysis with LogDNA.

![A screenshot of a cell phone Description automatically
generated](.//media/image6.png)

## 1 - 2 - Configure Monitoring

Next, we will follow a similar process to connect the IBM Cloud
Monitoring with SysDig service.

Click the connect button next to Monitoring.

![A screenshot of a cell phone Description automatically
generated](.//media/image7.png)

As with LogDNA we can either connect SysDig to an existing instance or
create a new instance. In this tutorial we will be creating a new
instance, click on create an instance.

![A screenshot of a cell phone Description automatically
generated](.//media/image8.png)

On the next screen, enter Dallas or the same region you have been using
throughout the tutorial.

Select Graduated Tier as the tutorial uses advance features of SysDig.

Give the service a meaningful name, I will use IBM Cloud Monitoring
Sysdig-zero-to-cloud-native

Make sure to select the zero-to-cloud-native resource group we have been
using throughout the tutorial.

Finally, enable IBM platform metrics so we can have a single dashboard
which will also monitor all the cloud services we have deployed.

![A screenshot of a social media post Description automatically
generated](.//media/image9.png)

After entering all these settings, click Create. As we saw with Logging,
the Monitoring line will now have an option to Launch the monitoring
dashboard. A future tutorial will go through a deep dive on using SysDig
to monitor our application.

![A screenshot of a social media post Description automatically
generated](.//media/image10.png)

## 2 - OpenShift 

### 2 - 1 Create an OpenShift Project

Next, we need to create a namespace or OpenShift project where our
application will run. To create a new OpenShift project, we need to
first log into our OpenShift cluster and retrieve a login token.

From the detailed view of your cluster, click on OpenShift web console.

![A screenshot of a cell phone Description automatically
generated](.//media/image11.png)

This will launch the OpenShift web console. To retrieve a login token,
click on you IBM Cloud IAM user name, and select Copy Login Command.

![A screenshot of a computer Description automatically
generated](.//media/image12.png)

This will bring you to a page where you need to click Display Token.

Clicking Display Token will give you a screen like the one below.

![A screenshot of a social media post Description automatically
generated](.//media/image13.png)

Copy the Log in with this token field.

Next, open up your iTerm2 terminal and paste the login command you just
copied.

Being the first time you log into the cluster, you will be on the
default project.

![](.//media/image14.png)

Next, we need to create our zero-to-cloud-native project. In the
terminal type:

```
oc new-project zero-to-cloud-native
```
![](.//media/image15.png)

### 2 - 2 Create Image Pull Secrets

While we are in the terminal, we also need to copy the image pull secret
from the default namespace to the zero-to-cloud-native namespace we just
created. The image pull secret allows our cluster to pull images from
the IBM Cloud Container Registry. When you create an Managed OpenShift
Cluster an image pull secret named all-icr-io is created in the default
namespace automatically. In order for to us the IBM Cloud Container
Registry, we need to copy this secret to the new namespace we created.
Enter the command below in your terminal to copy the image pull secret
from the default namespace to the namespace we just created.

```
kubectl get secret all-icr-io -n default -o yaml | sed 's/default/zero-to-cloud-native/g' | kubectl create -n zero-to-cloud-native -f -
```
![](.//media/image16.png)

This will create an image pull secret in our newly created namespace so
that we can pull images from IBM Cloud Container Registry.

## 3 - Configure IBM Cloud Messages with RabbitMQ

Our zero-to-cloud-native application will use message queues to
communicate between the frontend and backend microservices. We will need
to create message queue that our microservices will use before we deploy
our code.

From the dashboard view on IBM Cloud, click on Services.

![A screenshot of a cell phone Description automatically
generated](.//media/image17.png)

Under services, find and click on your Messages for RabbitMQ instance.

![A screenshot of a cell phone Description automatically
generated](.//media/image18.png)

### 

### 3 -1 Create an admin password

The first thing we need to do is generate an admin password for our
Rabbit MQ Instance.

Click on settings, enter a good password, and click create Update
Password.

![A screenshot of a cell phone Description automatically
generated](.//media/image19.png)

**IMPORTANT:** Throughout this tutorial, there are several settings that
you will need to remember for access later on. Create a temporary text
file to store these settings. The first that you will need to remember
is the **admin password for RabbitMQ**.

### 3 - 2 Rabbit MQ Certificate, Hostname and Port

Next, we will be storing the TLS certificate that we created for
RabbitMQ in our instance of certificate manager that we are using to
manage all of our certificates. To do so we will need to download the
certificate. To do so, click back to the Overview view and click on the
AMQPS Connection

![A screenshot of a cell phone Description automatically
generated](.//media/image20.png)

Scroll down to TLS certificate section and click Download. While you on
this screen, also note the Hostname and Port number as we will need this
in the next section. Make sure you are on the AMQPS tab. Our application
will use the Python Pika library which will use the AMQPS connection.

![A screenshot of a cell phone Description automatically
generated](.//media/image21.png)

**IMPORTANT:** add the RabbitMQ **Hostname** and **port** to your
temporary file, you will need these values later.

Also note where you saved the certificate. Navigate to the directory
where you saved the certificate file and rename the file so it ends with
.pem.

### 3 - 3 Add RabbitMQ Certificate to Certificate Manager

Next, we will add the Messages for RabbitMQ certificate to Certificate
Manager. Navigate to your IBM Cloud Dashboard and click on Services.

![A screenshot of a cell phone Description automatically
generated](.//media/image22.png)

Next, click on your Certificate Manager instance.

![A screenshot of a cell phone Description automatically
generated](.//media/image23.png)

On the next screen, click on Import.

![A screenshot of a cell phone Description automatically
generated](.//media/image24.png)

Enter a name that so that you can find the certificate for RabbitMQ
later. I will name the certificate RabbitMQ-Certificate.

Next, click browse to find the certificate file you downloaded and
renamed in the previous step and click Import.

![A screenshot of a social media post Description automatically
generated](.//media/image25.png)

On the next page, you should see two certificates. One for the domain we
created in Part 3, and the newly created RabbitMQ Certificate.

![A screenshot of a cell phone Description automatically
generated](.//media/image26.png)

To access the contents of the certificate later on from Certificate
Manager, we will need to use the Certificate ID or CRN. Click on the
name you gave for the RabbitMQ Certificate. This will bring up a second
window that will show the Certificate CRN.

![A screenshot of a cell phone Description automatically
generated](.//media/image27.png)

**IMPORTANT:** add the **RabbitMQ Certificate CRN** to your temporary
file, you will need this value later.

## 3 - 4 - Create RabbitMQ Queue

Next, we need to create our RabbitMQ Queue. The microservices that we
will be using a queue are 'get OpenShift tokens' and 'get OpenShift
versions'. Using the domain driven model, we can view both of these
microservices as an 'OpenShift' domain. As these are both very quick
realtime APIs, we can combine them together in a single microservice
sharing a single queue.

To create a queue, navigate back to your Messages by RabbitMQ service.
Scrolling down to the Connections section, click on Launch next to the
Public HTTPS endpoint.

![A screenshot of a cell phone Description automatically
generated](.//media/image28.png)

On the next login screen, you will get a warning that the site is not
secure. Just ignore that and continue to the site. Enter the admin
username and password you saved from the earlier step.

![A screenshot of a cell phone Description automatically
generated](.//media/image29.png)

On the next page, click on Queues.

![A screenshot of a cell phone Description automatically
generated](.//media/image30.png)

Next, click on Add a new queue.

![A screenshot of a cell phone Description automatically
generated](.//media/image31.png)

On the next screen, you need to give your queue a name. I'm going to
treat this environment as a dev environment so taking into consideration
which microservice this queue will service, I am going to name the queue
dev-ocp-realtime-02cn

![A screenshot of a social media post Description automatically
generated](.//media/image32.png)

After entering a name, click on Add Queue.

**IMPORTANT:** add the **RabbitMQ Queue** name to your temporary file,
you will need this value later.

That finishes the setup and configuration of Messages by RabbitMQ!
Overall, a very quick and easy process for setting up RabbitMQ. The
service not only provisioned an instance of Rabbit MQ, but is also
encrypting all the data on queues with an encryption key we create in
part 3 of the series, and will automatically we be backed up with
encryption.

## 4 - Configure IBM Cloud Databases for Redis

Our cloud-native application architecture will use Redis as a caching
database to store versions of OpenShift supported by IBM Cloud. To use
Redis in our application, we will need to get the TLS certificate for
our service and store it in our certificate manager instance.

From the IBM Cloud dashboard, click on Services.

![A screenshot of a cell phone Description automatically
generated](.//media/image22.png)

Under services, click on your instance of Databases for Redis.

![A screenshot of a cell phone Description automatically
generated](.//media/image33.png)

### 4 - 1 Obtain Redis Certificate, Hostname and Port

After clicking on the service, you will see an Overview screen for the
service. As we did with the RabbitMQ, scroll down to the TLS certificate
and click Download.

**IMPORTANT:** add the Redis **Hostname** and **Port number** to your
temporary file, you will need these values later.

![A screenshot of a cell phone Description automatically
generated](.//media/image34.png)

Navigate to where you saved the certificate, and like we did with
RabbitMQ, rename the certificate so is ends with .pem.

### 4 - 2 Add Redis Certificate to Certificate Manager

Now that we have the certificate, we need to store the certificate in
our certificate manage instance.

Going back to certificate manager, click on Import.

![A screenshot of a cell phone Description automatically
generated](.//media/image35.png)

Give your certificate a meaningful name. I will be using
Redis-zero-to-cloud-native-cert. Select the certificate file you
downloaded and renamed and click Import.

![A screenshot of a social media post Description automatically
generated](.//media/image36.png)

As we did with the Rabbit MQ certificate, we need to copy the
certificate CRN for our Redis certificate. Click on the Redis
certificate row in certificate manager and then copy the certificate CRN
value the screen the pops up on the right-hand side.

![A screenshot of a cell phone Description automatically
generated](.//media/image37.png)

**IMPORTANT:** add the **Redis Certificate** CRN to your temporary file,
you will need this value later.

### 4 - 3 Create Redis Service Credentials

We also need to create service credentials for our Redis Instance.

Going back to your Redis instance from your resource list, click on
Service Credentials.

![A screenshot of a cell phone Description automatically
generated](.//media/image38.png)

On the next screen, click New Credential, give your service credentials
a good name, and click Add.

![A screenshot of a cell phone Description automatically
generated](.//media/image39.png)

Expand the service credentials and scroll down to the authentication
section and note the username and password. Make sure to store this user
id and password in a place you will remember as we will be using it in
the next section.

![A screenshot of a cell phone Description automatically
generated](.//media/image40.png)

**IMPORTANT:** add the Redis **username** ( should be admin ), and
**password** to your temporary file, you will need these values later.

And that is it, Redis is ready to be used.

## 5 - Configure App to Log with LogDNA

There will be a future part of this series that will go through a deep
dive in using LogDNA. However, in able for our application to send
meaningful messages to LogDNA, there are a couple of things we need to
do.

First, we need to retrieve our LogDNA API key. From the IBM Cloud
hamburger menu, select Observability.

![A screenshot of text Description automatically
generated](.//media/image41.png)

Click on Logging.

![A screenshot of a cell phone Description automatically
generated](.//media/image42.png)

On the next screen, click on the options icon and select view Key. ![A
screenshot of a social media post Description automatically
generated](.//media/image43.png)

Click on show key and copy the key to a temporary file that we will use
later.

**IMPORTANT:** add the LogDNA Key to your temporary file, you will need
these values later.

![A screenshot of a social media post Description automatically
generated](.//media/image44.png)

 6 - Create an IBM Cloud API Key
-------------------------------

If you don't already have an API Key, you will need to create one. From
the IBM Cloud Console, select Manage and then Access (IAM). On the next
screen, click API keys and then Create an IBM Cloud API key.

![A screenshot of a cell phone Description automatically
generated](.//media/image45.png)

On the next screen, give your API Key a name and click create copy and
paste it in a file that you won't lose. We will be using this API key
throughout the tutorial so make sure to store it somewhere so that you
won't lose it. Do not share this API key with anyone as the API key will
allow whoever has it do pretty much anything to your IBM Cloud account.

![A screenshot of a cell phone Description automatically
generated](.//media/image46.png)

**IMPORTANT:** add the API Key you just created to your temporary file,
you will need this value later.

## 7 - Container Registry

Before we can deploy our images into a container registry, we will need
to create a namespace in IBM Cloud to store our images. To create a
container registry namespace, start iTerm2 and login into IBM Cloud with
the following command.

```
ic login --apikey=<apikey you copied above>
```

Next, we need to log into the container registry.

```
ic cr login
```
Now, we can create a new container registry namespace.

```
ic cr namespace-add zero-to-cloud-native
```
Note that you cannot have the same container registry namespace as
someone else. You will need to create a unique name, suggestion is to
use zero-to-cloud-native + your initials

**IMPORTANT:** note the namespace you just create in your temporary
file, you will need this value later.

That completes the setup and configuration of all the services our
application will use, next up we will go through the code base and then
deploy the application.
