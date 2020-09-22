![](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/images/02cn-logo.png)


# ZERO to Cloud-Native on IBM Cloud

With over 200 services available in IBM Cloud Catalog you can develop virtual any cloud native application that you can dream of.  But how can you best get started.  The purpose of this blog series ‘Zero to Cloud-Native with IBM Cloud’ is to showcase how to get started in developing with the IBM Cloud Catalog.  This multi-part series will cover topics such as architecture design and how to select the best services available in the IBM Cloud Catalog based on your architecture and application, how to combine services together and then how to implement day two operations while you are developing and also after you have deployed your application.

## Resources
**GitHub** - [GitHub Repository](https://github.com/kmcolli/zero-to-cloud-native)  
**Videos** - [YouTube Channel](https://www.youtube.com/channel/UCjPMAulESpSRxyxl_tiViJA/videos)   


## Tutorial Oveview
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
**Part 1 Introduction and Overview**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/1/1-intro.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part1-introduction.pdf)   
[Video](<https://youtu.be/Eta09EJ5bvY>)  
[Presentation](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/ZeroToCloudNative-1-Introduction.pdf)  
   

**Part 2 Microservices Architecture and Design**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/2/2-microservices.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part2-microservices-design.pdf)    
[Video](<https://youtu.be/UGyIlZfHYok>)  
[Presentation](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/ZeroToCloudNative-2-microservices.pdf)  

**Part 3 - Network and Security Configuration**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/3/3-network.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part3-network-and-security-configuration.pdf)  
[Video](<https://youtu.be/xBCjiH0Ud1g>)

**Part 4 - RedHat OpenShift on IBM Cloud Setup and Configuration**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/4-openshift.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part4-openshift.pdf)  
[Video](<https://youtu.be/2oCHWzLPq3M>)

**Part 5 - IBM Cloud Databases and Messaging**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/5-dbaas.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part5-cloud-databases-and-messaging.pdf)  
[Video](<https://youtu.be/Npq3dAtMtI8>) 

**Part 6 - Setting up a Cloud-Native Development Environment**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/6-dev.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part6-setting-up-a-development-environment.pdf)  
[Video](<https://youtu.be/kerMUm_1OsA>)  

**Part 7 - Preparing to Deploy**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/7-code.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part7-preparing-to-deploy.pdf)  
[Video](<https://youtu.be/irAwcsY7mN8>) 

**Part 8 - Cloning and Importing the Code Base**    
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/8-codebase.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part8-cloning-importing-codebase.pdf)  
[Video](<https://youtu.be/61t1548B6UQ>)  
[Presentation](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/part8-presentation.pdf)

**Part 9 - Deploying the Application**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/9-deploy.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part9-deploying-the-application.pdf).   
[Video](<https://youtu.be/JF7rxSHYX7U>)  
[Presentation](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/part9-presentation.pdf) 

**Part 9A - Deploying with a 'Classic' Pipeline**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/9a-classic.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part9A-creating-classic-pipeline.pdf)  
[Video](<https://youtu.be/CrU5aHtQROc>)

**Part 9B - Deploying with a Tekton Pipeline**  
[Blog](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/zero-to-cloud-native-part9B-creating-Tekton-Pipeline.pdf)  
[Video]  **coming soon**

**Part 9C - Finishing Deploying and Testing the Application**  
[Blog](https://github.com/kmcolli/zero-to-cloud-native/blob/master/docs/pc-finish.md)  
[PDF](https://raw.githubusercontent.com/kmcolli/zero-to-cloud-native/master/docs/ZeroToCloudNative -part9C-Finish-deploying-testing.pdf)  
[Video](<https://youtu.be/EOzqCQhm7Bc>)

## Coming soon - Day 2 Operations
* **Logging**
* **Monitoring**
* **Automating with Terraform**
* **RedHat Service Mesh**
* **Deploying to a remote location with IBM Cloud Satellite**
* **Managing code with Code Ready Workspace**




