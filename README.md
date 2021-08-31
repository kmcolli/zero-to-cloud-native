![](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/images/02cn-logo.png)


# ZERO to Cloud-Native on IBM Cloud

With over 200 services available in IBM Cloud Catalog you can develop virtual any cloud native application that you can dream of.  But how can you best get started.  The purpose of this blog series ‘Zero to Cloud-Native with IBM Cloud’ is to showcase how to get started in developing with the IBM Cloud Catalog.  This multi-part series will cover topics such as architecture design and how to select the best services available in the IBM Cloud Catalog based on your architecture and application, how to combine services together and then how to implement day two operations while you are developing and also after you have deployed your application.

# *NEW* - Deploy to IBM Cloud Satellite Tutorial
Learn how to create a satellite location to run your OpenShift Cluster.  This tutorial series can be used either as a stand-alone series to just learn about IBM Cloud Satellite OR ... can be used in conjunction with the main tutorial below.

[Satellite Tutorial Series](https://github.com/kmcolli/satellite) 


## Resources
**GitHub** - [GitHub Repository](https://github.com/kmcolli/zero-to-cloud-native)  
**Videos** - [YouTube Channel](https://www.youtube.com/channel/UCjPMAulESpSRxyxl_tiViJA/videos)   


## Main Tutorial Oveview
The best what to learn from my perspective is getting hands on experience.  The accompanying tutorial will go through the entire cloud-native development lifecycle.   I will walk through step by step to create an entire cloud native application on IBM Cloud leveraging the IBM Cloud Catalog.   

The tutorials will go through the following topics:  

* Microservices design  
* Creating a new isolated resource group
* Creating a custom domain for your application.  
* Setting up and configuring a virtual private cloud including configuring subnets, access control lists and security groups.  
* Configuration of IBM Cloud Internet Services to secure and provide DNS services for a custom domain  
* Leveraging certificate manager to create and managed domain certificates  
* Using Key Protect to manage your own certificates across IBM Cloud services  
* Provisioning and configuring a managed OpenShift Cluster in a multi-zone region  
* Provisioning and configuring Rabbit MQ for messaging  
* Provisioning and configuring IBM Cloud Databases for a data layer  
* Creating a classic CI/CD pipeline with integration with an enterprise git repository  
* Creating a tekton CI/CD pipeline with integration with an enterprise git repository  
* Deploying and running the application on OpenShift  
* Leveraging IBM Log Analysis with LogDNA to provide logging and troubleshoot both from a development and support perspective.  
* Leveraging IBM Systems Monitoring with SysDig to monitor IBM Cloud Platform components, OpenShift cluster and the application itself.  
* Tuning your cluster to optimize availability and resources.  
* New functionality from IBM Cloud to clone and re-instate cluster namespaces  

![](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/images/zero-to-cloud-native.png)

## ZERO to Cloud Native on IBM Cloud Application
The tutorial application will comprise of three APIs:  
1)	**getOCPVersions** – will return a list of OpenShift versions supported by IBM Cloud Managed OpenShift  
2)	**getOCPToken** – will return an OCP token to login into your cluster.  
3)	**enableNodeSSH** – will enable your IBM Cloud Managed OpenShift worker nodes to be SSH’d into.  

To support these APIs, the following microservices, all written in Python, will be created.
  
* **frontend-web.py** – HTML front end to invoke the three public facing APIs  
* **frontend-api.py** – API interface to call the public facing APIs   
* **getOCPVersions.py** – implementation of the getOCPVersions API which will quickly return a list of APIs leveraging a Redis Cache.  
* **loadOCPVersions.py** – Kubernetes cronjob that will load current versions of OCP supported by IBM Cloud into a Redis database.   
* **getOCPToken.py** – implementation of the real-time getOCPToken API.  
* **enableNodeSSH.py** – implementation of the ‘long’ running enableNodeSSH API

## ZERO to Cloud Native Index


###  **Part 1 Introduction and Overview**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/1/1-intro.md)  
[Presentation](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/ZeroToCloudNative-1-Introduction.pdf)  

[![Part 1](http://img.youtube.com/vi/Eta09EJ5bvY/0.jpg)](https://youtu.be/Eta09EJ5bvY)

***


###  **Part 2 Microservices Architecture and Design**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/2/2-microservices.md)  

[![Part 1](http://img.youtube.com/vi/UGyIlZfHYok/0.jpg)](https://youtu.be/UGyIlZfHYok)

***

###  **Part 3 - Network and Security Configuration**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/3/3-network.md)  

[![Part 1](http://img.youtube.com/vi/xBCjiH0Ud1g/0.jpg)](https://youtu.be/xBCjiH0Ud1g)

***


###  **Part 4 - RedHat OpenShift on IBM Cloud Setup and Configuration**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/4/4-openshift.md)  

[![Part 1](http://img.youtube.com/vi/2oCHWzLPq3M/0.jpg)](https://youtu.be/2oCHWzLPq3M)

***


###  **Part 5 - IBM Cloud Databases and Messaging**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/5/5-dbaas.md)  

[![Part 1](http://img.youtube.com/vi/Npq3dAtMtI8/0.jpg)](https://youtu.be/Npq3dAtMtI8)

***


###  **Part 6 - Setting up a Cloud-Native Development Environment**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/6/6-dev.md)  

[![Part 1](http://img.youtube.com/vi/kerMUm_1OsA/0.jpg)](https://youtu.be/kerMUm_1OsA)

***

###  **Part 7 - Preparing to Deploy**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/7/7-code.md)  

[![Part 1](http://img.youtube.com/vi/irAwcsY7mN8/0.jpg)](https://youtu.be/irAwcsY7mN8)

***

###  **Part 8 - Cloning and Importing the Code Base**    
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/8/8-codebase.md)  
[Presentation](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/part8-presentation.pdf)

[![Part 1](http://img.youtube.com/vi/61t1548B6UQ/0.jpg)](https://youtu.be/61t1548B6UQ)

***

###  **Part 9 - Deploying the Application**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/9/9-deploy.md)  
[Presentation](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/part9-presentation.pdf) 

[![Part 1](http://img.youtube.com/vi/JF7rxSHYX7U/0.jpg)](https://youtu.be/JF7rxSHYX7U)

***

###  **Part 9A - Deploying with a 'Classic' Pipeline**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/9a/9a-classic.md)  

[![Part 9A](http://img.youtube.com/vi/CrU5aHtQROc/0.jpg)](https://youtu.be/CrU5aHtQROc)

***

###  **Part 9B - Deploying with a Tekton Pipeline**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/9b/9b-tekton.md)   

[Video]  **coming soon**

***


###  **Part 9C - Finishing Deploying and Testing the Application**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/9c/9c-finish.md)  

[![Part 1](http://img.youtube.com/vi/EOzqCQhm7Bc/0.jpg)](https://youtu.be/EOzqCQhm7Bc)

***

# Day 2 Operations  

###  **Part 10 - Log Analysis with LogDNA**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/10/10-logging.md)   

[![Part 1](http://img.youtube.com/vi/RjgdxSptl4Y/0.jpg)](https://youtu.be/RjgdxSptl4Y)


***

###  **Part 10A - Exporting Logs**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/10A/10A-export-logs.md)   

[![Part 1](http://img.youtube.com/vi/Vzo6uFfUtdY/0.jpg)](https://youtu.be/Vzo6uFfUtdY)

***


###  **Part 11 - Monitoring with SysDig**  
* coming soon


***

###  **Part 12 - Updating OpenShift Worker and Master Nodes**  
[Tutorial](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/12/12-update-openshift.md)   

[![Part 1](http://img.youtube.com/vi/k2PpvsWZ7BE/0.jpg)](https://youtu.be/k2PpvsWZ7BE)





