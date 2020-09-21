Zero to Cloud-Native with IBM Cloud
===================================

Part 3: IBM Cloud Account, Network and Security Configuration
=============================================================

### 

### **Kevin Collins**

kevincollins\@us.ibm.com

### Technical Sales Leader

### IBM Cloud Enterprise Containers -- Americas

**Kunal Malhotra**
------------------

kunal.malhotra3\@ibm.com

#### Cloud Platform Engineer

#### IBM Cloud MEA

1 - Resource Groups
===================

1 -1 Introduction
-----------------

One of the first things that you need to do after you have designed and
architected your application, is planning how to setup your IBM Cloud
Account. We will be leveraging a single IBM Cloud account and segregate
resources with a resource group. A resource group is a way for you to
organize your account resources in customizable groupings so that you
can quickly assign users access to more than one resource at a time. Any
account resource that is managed by using IBM Cloud Identity and Access
Management (IAM) access control belongs to a resource group within your
account.

You can read more about resource groups here:

<https://cloud.ibm.com/docs/account?topic=account-rgs>

1 -2 Tutorial - Resource Groups
-------------------------------

The first part of the tutorial is creating a new resource group for the
zero to cloud native on IBM Cloud tutorial resources. We will place all
the resources we create during the tutorial into this resource group.

1)  After logging into cloud.ibm.com click on Manage and then select
    access (IAM)

![A screenshot of a cell phone Description automatically
generated](.//media/image1.png){width="3.346405293088364in"
height="1.3099606299212598in"}

2)  Click on Resource Groups and then Create. Enter a name, I will be
    use **zero-to-cloud-native**

![A screenshot of a cell phone Description automatically
generated](.//media/image2.png){width="4.189542869641294in"
height="1.501253280839895in"}

2 Virtual Private Cloud - Network Design
========================================

2 -- 1 Overview
---------------

After you have your resource group created, we can start deploying
services for our application. The first consideration to think about is
the network design. In IBM Cloud, we offer two network infrastructure
designs, one being Virtual Private Cloud and the other being classic
infrastructure. VPC on IBM Cloud is that latest and greatest cloud model
that provides advanced security through network isolation, more
granularity for network configuration and security and improved compute
and storage provisioning. For both CloudPak Provisioner and the tutorial
we will be using a virtual private cloud. Generally speaking, you will
want one virtual private cloud for each environment that you plan to
use. For CloudPak provisioner, I had one environment for dev and one for
production. My very simplistic VPC design resulted in two VPCs, not
surprisingly one for dev and one for production. The reason for this is
to provide strong network isolation and control between the two
environments.

![A screenshot of a cell phone Description automatically
generated](.//media/image3.png){width="6.5in"
height="1.1277777777777778in"}

There are several things you need to setup for your VPC.

-   The first thing that you need to setup is a subnet for each
    availability zone that you plan to use.

-   The second is an access control list for the VPC. You can use an
    access control list (ACL) to control all incoming and outgoing
    traffic in IBM Cloud Virtual Private Cloud. An ACL is a built-in,
    virtual firewall, similar to a security group. In contrast to
    security groups, ACL rules control traffic to and from
    the *subnets*, rather than to and from the *instances*.

-   The third is a security group. Security groups give you a convenient
    way to apply rules that establish filtering to each network
    interface of a virtual server instance, based on IP address. When
    you create a security group, you configure it to create the network
    traffic patterns you want. By default, a security group denies all
    traffic. As rules are added to a security group, it defines the
    traffic that the security group permits.

You can read more about IBM Cloud VPC here:

<https://cloud.ibm.com/docs/vpc?topic=vpc-about-vpc>

2 -2 Tutorial -- Creating a Virtual Private Cloud
-------------------------------------------------

Let's start by creating a VPC for our tutorial. From the IBM Cloud
Catalog, click on the hamburger menu, select VPC Infrastructure and then
VPC.

![A screenshot of a cell phone Description automatically
generated](.//media/image4.png){width="1.8632950568678914in"
height="2.1437904636920386in"}

On the next screen click create, to create a new VPC.

![](.//media/image5.png){width="6.5in" height="0.7375in"}

On the following screen, you will need to enter information to create
your VPC.

**Name** -- name of your VPC. Name this something meaningful in the case
you plan to have multiple VPCs in your account. I will use
zero-to-cloud-native in this tutorial.

**Resource Group** -- select the resource group name you created in step
one above. I am using zero-to-cloud-native as the resource group in this
tutorial.

**Default security group** -- I recommend to keep Allow SSH and Allow
Ping enabled for dev environments as it will allow us to ssh and ping
into pods / services while debugging. For a production environment,
consider disabling these settings.

**Classic Access** -- in this tutorial we will not be using resources in
classic infrastructure so you can uncheck this option.

**Default address prefixes** -- leave this option checked as we do want
to create a default prefix for each zone.

Next, you will need to create a new subnet. In this tutorial, we will be
deploying a multizone cluster across three zones which means we will
need to create one subnet for each zone. In this example I will be using
Dallas as my multizone region. When you create a VPC you only have an
option of creating a single subnet during the VPC creation. We will
start by creating a subnet in one of the multizone availability zones
and then go back and create the other two.

**Subnet Name** -- when I create subnets, I include the vpc name and
location in the subnet name so I can easily find the subnets if needed.
I will start with dallas 3 and will name my first subnet
zero-to-cloud-native-dal3

**Location** -- pick one of the locations in the MZR that you are
planning on using. I will start by using Dallas 3.

**IP Range** -- being a rather simple tutorial, we won't need that many
IP addresses for our cluster and application. I will select 64
addresses.

**Public Gateway** -- make sure you select Attached. This will allow us
to access the OpenShift Console. For a production level cluster,
consider un-attaching the public gateway if you do not require Internet
connectivity.

![A screenshot of a cell phone Description automatically
generated](.//media/image6.png){width="6.5in"
height="4.704166666666667in"}

After entering all the information, click Create virtual private cloud
on the right-hand side of the screen.

![A screenshot of a cell phone Description automatically
generated](.//media/image7.png){width="1.457425634295713in"
height="1.6143788276465443in"}

The next screen will take you to a list of virtual private clouds. Click
on the virtual private cloud you just created. We will need to create
additional subnets to support our multizone cluster and we will need to
create access rules for our application in the security group section.

![A screenshot of a cell phone Description automatically
generated](.//media/image8.png){width="6.5in"
height="1.117361111111111in"}

The next thing we will want to do is to create our subnets for the
additional two availability zones for our multizone cluster.

Scrolling down to our subnets in this VPC section. Click on create to
create another subnet.

![A screenshot of a cell phone Description automatically
generated](.//media/image9.png){width="6.5in"
height="1.3458333333333334in"}

Follow the same steps you did when you created your first subnet when
you created your VPC. I will be using these settings for the second
subnet I'm creating in Dallas 2.

**Name:** zero-to-cloud-native-dal2

**Resource Group:** zero-to-cloud-native

**Location:** Dallas 2

**IP Range -- Number of addresses:** 64

**Public Gateway:** Attached

\*leave all the other settings with the default values.

Once you have entered all the settings, click on Create Subnet to create
this new subnet.

![A screenshot of a cell phone Description automatically
generated](.//media/image10.png){width="4.372549212598425in"
height="4.125892388451444in"}

Repeat the same process of the third subnet. I will be using these
settings for the third and final subnet in Dallas 1.

**Name:** zero-to-cloud-native-dal1

**Resource Group:** zero-to-cloud-native

**Location:** Dallas 1

**IP Range -- Number of addresses:** 64

**Public Gateway:** Attached

\*leave all the other settings with the default values.

Now that we have created our three subnets in each zone for or multizone
region, we can now update the security group to support our application.
On the VPC navigation tree on the left, click on Security Groups which
will bring up a list of all the security groups in your account. On the
right-hand side, click on the security group for the VPC you just
created.

![A screenshot of a social media post Description automatically
generated](.//media/image11.png){width="6.5in"
height="2.8180555555555555in"}

By default, when you create a VPC all outbound traffic will be allowed
and all inbound traffic will be blocked. Since we did enable SSH and
Ping those will also be allowed for inbound traffic. For our cloud
native application, we will need to create three additional rules, one
for the OpenShift console, second for web application and third for the
API interface.

Click on create in the Inbound rules section to create a new rule.

![A screenshot of a cell phone Description automatically
generated](.//media/image12.png){width="6.5in"
height="2.2534722222222223in"}

Starting with the ports for OpenShift, enter 30000 as the Port min and
32767 as the Port max and click save.

![A screenshot of a cell phone Description automatically
generated](.//media/image13.png){width="1.9738560804899388in"
height="1.4577799650043743in"}

Repeat the same process for port 80 ( web application ) and port 8000 (
API Interface )

After creating the additional rules, your **Inbound rules** should look
like this.

![A screenshot of a cell phone Description automatically
generated](.//media/image14.png){width="6.5in"
height="2.589583333333333in"}

Next, we also need to create outbound rules for both port 80 and port
8000. Under outbound rules, click on Create.

![](.//media/image15.png){width="6.5in" height="0.6006944444444444in"}

On the next screen, create a new outbound rule for port 80.

![A screenshot of a cell phone Description automatically
generated](.//media/image16.png){width="2.1043318022747157in"
height="1.91503280839895in"}

Repeat the same process to create another outbound rule for port 8000.

3 - Custom Domains
==================

When developing your cloud-native application, you will most likely want
a domain that is easily readable rather than the default domain you get
when you create an OpenShift cluster like the following:

getroks-nonprod-ocp-73aebe0673131e634c608c4167edcc2aeb-0000.us-south.containers.appdomain.cloud

One of the services we have in IBM Cloud is called Cloud Internet
Services which provides reliability, performance, and security for
Internet facing applications, websites, and services using Cloudflare\'s
165+ Global Points of Presence (PoPs). It includes Domain Name Service
(DNS), Global Load Balancer (GLB), Distributed Denial of Service (DDoS)
protection, Web Application Firewall (WAF), Transport Layer Security
(TLS), Rate Limiting, Smart Routing, and Caching. For the tutorial, we
will mainly be using Cloud Internet Services for a Domain Name Service.
IBM Cloud also offers a service called Domain Name Registration Service
that can be used to order and manage domains.

You can read more about IBM Cloud Domain Name Registration here:

<https://cloud.ibm.com/docs/dns?topic=dns-getting-started>

You can read more about IBM Cloud Internet Services here:

[[https://cloud.ibm.com/docs/cis?topic=cis-about-ibm-cloud-internet-services-cis]{.ul}](https://cloud.ibm.com/docs/cis?topic=cis-about-ibm-cloud-internet-services-cis)

3 -- 1 Custom Domain Tutorial
-----------------------------

If you don't already have a domain that you would like to use, then the first thing we will need to do is create one. From the IBM Cloud catalog, search from Domain and then click on Domain Name Registration Service.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 ![A screenshot of a cell phone Description automatically generated](.//media/image17.png){width="2.045178258967629in" height="1.8627449693788276in"}
----------------------------------------------------------------------------------------------------------------------------------------------------

On the next screen, there are no options, on the bottom right corner,
click create.

The following screen will provide you the option to create a new domain.
Click on Register, enter the domain you want, I will be using
zero-to-cloud-native.com. Click on Check Availability to make sure the
domain you want is available and then click Continue.

![A screenshot of a cell phone Description automatically
generated](.//media/image18.png){width="6.5in"
height="1.3979166666666667in"}

Enter your registrant information and click Order Now.

![A screenshot of a social media post Description automatically
generated](.//media/image19.png){width="4.344338363954506in"
height="2.6339873140857395in"}

After about 5 minutes, you will see the domain you requested shows up in
the domain view.

Expand the domain you created and not the custom name servers to use
later.

![A screenshot of a social media post Description automatically
generated](.//media/image20.png){width="6.5in"
height="1.1020833333333333in"}

The next step will be connecting the domain you just created with Cloud
Internet Services. Please note, you will need to verify your domain
before you can complete the process of registering the domain with IBM
Cloud Internet Services. Look for an email from IBM with instructions on
verifying your information.

3 -- 2 Cloud Internet Services Tutorial
---------------------------------------

The next thing we will need to do is create an instance of Cloud
Internet Services from the IBM Cloud catalog. To do so, click on the
Catalog of IBM Cloud, search for Internet Services, and click the
Internet Services tile. We will use Cloud Internet Service to manage the
DNS and Internet security for the domain we just created.

![A screenshot of a cell phone Description automatically
generated](.//media/image21.png){width="3.261438101487314in"
height="2.439458661417323in"}

This brings up a screen to select the pricing plan you want for Internet
Services. Select the Standard plan as being a tutorial and not for a
production system we don't need the all the advanced security options
provided by the other plans. For a production system, we strongly
recommend that you select one of the Enterprise plans.

![A screenshot of a social media post Description automatically
generated](.//media/image22.png){width="3.8436734470691163in"
height="3.1176465441819774in"}

At the bottom of the screen, enter a name for the service and the
resource group you created earlier to place the service in. I will be
using Internet Services-zero-to-cloud-native as the name of my service.
Click create after selecting the plan and entering the name for your
service.

![A screenshot of a cell phone Description automatically
generated](.//media/image23.png){width="6.5in"
height="1.3479166666666667in"}

This will create your service and will bring you to the overview page of
your IBM Cloud Internet Services instance you just created. To get
started ... click Let's get started.

![A screenshot of a cell phone Description automatically
generated](.//media/image24.png){width="4.705882545931758in"
height="1.237305336832896in"}

This will bring up a new page to setup your domain. Enter the domain
name you created earlier and click next.

![A screenshot of a cell phone Description automatically
generated](.//media/image25.png){width="2.1000568678915137in"
height="2.3137259405074366in"}

You can skip the setup your DNS records, just click next.

![A screenshot of a cell phone Description automatically
generated](.//media/image26.png){width="2.2026148293963255in"
height="2.1166272965879265in"}

Next, on the delegate domain management screen, note the new NS records.
Before you click next, you will need to update the domain you created
with these records. Note, you can only update the DNS records name
server once per hour. If you get an error stating so, wait an hour from
when you created the domain.

Open a new browser or tab and navigate to the IBM Cloud Domain Service
like you did in the previous step. Under the domain you created select
Unlock from the Lock Domain drop down.

![A screenshot of a social media post Description automatically
generated](.//media/image27.png){width="6.5in"
height="1.3840277777777779in"}

Next click on Add/ Edit NS and enter the NS specified on the Cloud
Internet Services screen.

Enter the new name servers and click Associate.

![A screenshot of a cell phone Description automatically
generated](.//media/image28.png){width="3.3660126859142605in"
height="1.9750153105861767in"}

Now, go back to Cloud Internet Services window and click the final Next
button.

![A screenshot of a cell phone Description automatically
generated](.//media/image29.png){width="1.9930555555555556in"
height="4.26797353455818in"}

After you click the final next button, you will be taken to the Overview
page of IBM Cloud Internet Services. Your domain names are now being
managed by the service. We will come back to IBM Cloud Internet Services
later on to create a DNS entry for our zero-to-cloud-native application.

4 - Tutorial -- IBM Cloud Certificate Manager
---------------------------------------------

The next thing we will need to do is create a certificate for the domain
we just created so we can secure our application. To do so, click on
Catalog, search for Certificate Manager, and then click the Certificate
Manager tile.

![A screenshot of a social media post Description automatically
generated](.//media/image30.png){width="4.045751312335958in"
height="1.5789665354330709in"}

You can read more about Certificate Manager here:

<https://cloud.ibm.com/docs/certificate-manager?topic=certificate-manager-about-certificate-manager>

On the next screen, enter details for a new instance of IBM Cloud
Certificate Manager.

**Select a Region** -- best practice is to keep all resources in the
same region. I will use Dallas.

**Service Name** -- so we can distinguish different instances give the
service a meaningful name. I will use Certificate Manager --
zero-to-cloud-native.

**Endpoints** -- you can optionally block access to the public network
and use private only endpoints. Being a simply tutorial, I will keep the
default Public and Private.

After entering these values, click Create. This brings you to a screen
showing your certificates. As we have just created the instance of
Certificate Manager you will not see any certificates and will need to
create one. To do so, click the Order button.

![A screenshot of a cell phone Description automatically
generated](.//media/image31.png){width="4.758170384951881in"
height="1.3837325021872267in"}

Because we are using Cloud Internet Services as our DNS provider, click
Continue under IBM Cloud Internet Services (CIS) tile.

![A screenshot of a cell phone Description automatically
generated](.//media/image32.png){width="4.385606955380577in"
height="1.549019028871391in"}

On the next screen, you will see a warning message saying there are no
IBM Cloud Internet Services instances that Certificate Manager has
permission to access.

![A picture containing bird Description automatically
generated](.//media/image33.png){width="4.529411636045494in"
height="0.709413823272091in"}

To fix this, we will need to authorize certificate manager to interact
with Cloud Internet Services.

1.  Navigate to **IBM Cloud \> Manage \> Access (IAM) \>
    Authorizations**.

2.  Click **Create** and assign a source and target service. The source
    service is granted access to the target service based on the roles
    that you set in the next step.

    -   Source: Certificate Manager

    -   Target: Internet Services

3.  Specify a service instance for both the source and the target.

4.  Assign the **Manager** role to allow Certificate Manager to view the
    CIS instance and its domains. Then, click **Authorize**.![A
    screenshot of a cell phone Description automatically
    generated](.//media/image34.png){width="6.5in"
    height="6.826388888888889in"}

Now, when you go back to the Certificate Manager order certificate
screen you will not see that warning message.

Next on the Order Certificate screen, enter the details for your
certificate.

**Name**: zero-to-cloud-native

**Automatic certificate renewal** -- recommendation is to turn this to
yes. The certificate manager service will automatically renew your
certificate and notify you. You can also automate deployments through a
CI/CD process to deploy the renewed certificates.

![A screenshot of a social media post Description automatically
generated](.//media/image35.png){width="4.238221784776903in"
height="5.3856211723534555in"}

Next, click on the Domains tab and select the IBM Cloud Internet
Services (CIS) instance you created. Select both Add Domain and Add
Wildcard, and click Order.

![A screenshot of a social media post Description automatically
generated](.//media/image36.png){width="4.013071959755031in"
height="2.8455938320209975in"}

This will order your new certificates and will take a couple of minutes.
We won't be using the certificates right away so you don't have to wait
for the order to complete.

### 5 - Tutorial -- Key Protect

The last tutorial in this section is to create an instance of Key
Protect that we will be using through the tutorial to store our
encryption keys. IBM Key Protect for IBM Cloud allows you to store
encrypted keys for apps across IBM Cloud services. As you manage the
lifecycle of your keys, you can benefit from knowing that your keys are
secured by FIPS 140-2 Level 3 certified cloud-based hardware security
modules (HSMs) that protect against the theft of information.

You can read more about Key Protect here:

<https://cloud.ibm.com/docs/key-protect?topic=key-protect-about>

For highly regulated workloads, IBM is the only cloud provider to offer
FIPS 140-2 Level 4 encryption where you keep your own key. IBM Cloud
Hyper Protect Crypto Services integrates with Key Protect to enable Keep
Your Own Keys (KYOK) for IBM Cloud, so your organization has more
control and authority over its data. Check out the [Hyper Protect Crypto
Services](https://cloud.ibm.com/catalog/services/hyper-protect-crypto-services)
offering details page.

For this tutorial we will be using BYOK with IBM Cloud Key Protect for
all of the IBM Cloud services including our OpenShift cluster and
workloads.

The first thing we need to do is to create an instance of Key Protect.
To do so, go to the IBM Cloud, search for Key Protect, and click the Key
Protect tile.

![A screenshot of a social media post Description automatically
generated](.//media/image37.png){width="3.620915354330709in"
height="2.046048775153106in"}

Just like you did with the other services so far, there are a couple of
settings to enter to create the service.

**Select a Region** -- best practice is to keep all resources in the
same region. I will use Dallas.

**Service Name** -- so we can distinguish different instances give the
service a meaningful name. I will use Key Protect--zero-to-cloud-native.

**Resource Group** -- to keep everything nicely organized, I will choose
the zero-to-cloud-native resource group.

**Endpoints** -- you can optionally block access to the public network
and use private only endpoints. Being a simply tutorial, I will keep the
default Public and Private.

After entering all your settings, click Create.

![](.//media/image38.png){width="6.5in" height="5.90625in"}

The next thing we need to do is add an encryption key to Key Protect.
Navigate to your Key Protect instance from your IBM Cloud resource list
and click Add Key.

![A screenshot of a cell phone Description automatically
generated](.//media/image39.png){width="6.5in"
height="1.6777777777777778in"}

On the next screen you have the option of either having the service
create a key or you can import your own key. For this tutorial, we will
just have Key Protect create our own Root key, but if you can easily
import your own key if you have one.

![A screenshot of a cell phone Description automatically
generated](.//media/image40.png){width="2.647445319335083in"
height="1.9411767279090113in"}

Give your key a meaningful name such as zero-to-cloud-native-root-key
and click Create Key.

Congratulations!!

If you have made it this far, you have completed the longest step of the
zero-to-cloud-native tutorial series. We have setup a solid and secure
enterprise environment to start deploying our application. To recap, we
have just done the following:

-   Created a secure Virtual Private Cloud opening only the ports our
    application will use.

-   Created a custom domain with IBM Cloud Domain Services.

-   Created an instance of Cloud Internet Services and configured it as
    the DNS provider of our domain.

-   Created a wildcard certificate for our newly created domain using
    IBM Cloud Certificate Manager with automated renewal.

-   Created an instance of IBM Cloud Key Protect where we will store our
    encryption keys.

And we did all of this on IBM Cloud!
