Zero to Cloud-Native with IBM Cloud
===================================

**Kevin Collins ( kevincollins@us.ibm.com )**  
**Kunal Malhotra ( kunal.malhotra3@ibm.com )**  

Part 9A: Creating a Classic Pipeline 
====================================


## 1 - IBM Cloud Continuous Delivery -- Classic Pipeline

Note: This tutorial will assume you have already setup a toolchain as
describe in Part 9 -- Section 4.

This tutorial will go step through creating is a classic pipeline for
the API frontend microservice. To get started, navigate to the
zero-to-cloud-native-toolchain you created in part 9 for this series.

![](.//media/image1.png)

### 1 - 1 GitHub Integration

The first thing you need to do is connect your SCM (Source Code
Management) repository with the toolchain. You do this by clicking add
tool and then selecting SCM (GitHub, Bitbucket, GitLab, or GitHub
Enterprise Whitewater).

Note: Repositories can be Public or Private. In this tutorial, I will be
the public repository that I've shared and that you clone in part 8. By
default, I recommend setting your repositories to private unless you
plan to share them publicly. This way, if you make a mistake and upload
something like an API key, you are still protected.

Note: if this is the first time you are creating a toolchain with a
GitHub repository, you will first need to Authorize IBM Cloud to access
GitHub. You can follow the instructions here if needed:

<https://cloud.ibm.com/docs/ContinuousDelivery?topic=ContinuousDelivery-integrations#github>

On the next screen, select **GitHub**.

![A screenshot of a cell phone Description automatically
generated](.//media/image2.png)

Select **Existing** as the repository type and enter the repository URL
for your git repository that you setup in Part 8 of this tutorial
series. Click **Create Integration** after entering these settings.

![A screenshot of a social media post Description automatically
generated](.//media/image3.png)

### 1 - 2 Create Delivery Pipeline
-------------------------------

The next tool we need to add is a Delivery Pipeline. Click on **add
tool** and then click on the **Delivery Pipeline tile**.

![A picture containing bird, flower, tree Description automatically
generated](.//media/image4.png)

On the next screen, give you delivery pipeline a name. I will use
**dev-classic-frontend-api-toolchain**. For this part of the tutorial,
keep **Classic** selected as the Pipeline type, and click **Create
Integration**.

![A screenshot of a social media post Description automatically
generated](.//media/image5.png)

#### 1-2-1 Add the Continuous Delivery Service

On the next screen, you will see all the tools we have added to our
toolchain. The next step is to configure the delivery pipeline we just
created. You will also see the following warning notice.

![A close up of a sign Description automatically
generated](.//media/image6.png)

You have 5 days to use the service for free. If you want to use the
service more than 5 days, click on **Add the service**.

The first step on the next page is to select a region and plan. Keep the
region as **Dallas**, like all our other resources, and you can also
keep the plan selected as **Lite**.

![A screenshot of a social media post Description automatically
generated](.//media/image7.png)

Scrolling down, you need to give you service a name and indicate which
resource group to place it in. I will use **Continuous
Delivery-zero-to-cloud-native** as the name and select
**zero-to-cloud-native** as the resource group.

After entering those settings, click on **Create**.

![A screenshot of a cell phone Description automatically
generated](.//media/image8.png)

### 1 - 3 Configure your Delivery Pipeline
---------------------------------------

Navigate back to the toolchain you just created by clicking on the IBM
Cloud hamburger menu and **select DevOps**.

![A screenshot of a cell phone screen with text Description
automatically
generated](.//media/image9.png)

On the next screen, select **Dallas** as the location your toolchain is
in and then click on the **toolchain name**.

![A screenshot of a cell phone Description automatically
generated](.//media/image10.png)

On the next screen, click on the **Delivery Pipeline** tile that you
just created.

![A screenshot of a cell phone Description automatically
generated](.//media/image11.png)

#### 1-3-1 Build Stage

This will bring you to what looks like an empty screen as your pipeline
does not have any stages in it yet. Click on **Add Stage**.

![](.//media/image12.png)

Start by naming the first stage **Build**.

Your git hub repository should already to populated as we have already
created Git integration.

Next, we will be building a dev environment, select the **dev branch**
that you created in part 8 of this tutorial series.

Make sure to keep the '**When a commit is pushed'** option selected.
This will automatically start our toolchain once a commit is pushed to
GitHub.

![A screenshot of a cell phone Description automatically
generated](.//media/image13.png)

Don't click Save quite yet, instead click on the **Jobs** tab. A stage
in a classic toolchain consists of a number of jobs. The first job we
will create is fetching the code from GitHub. Click on **Add Job** and
select **Build**.

![](.//media/image14.png)

Change the name of the Job to **Fetch Code**, the builder type to
**shell script** and enter

**source ./pipeline/scripts/fetch_code.sh**

as the Build script.

![A screenshot of a cell phone Description automatically
generated](.//media/image15.png)

Note: you can view all the scripts used in this tutorial from the
**pipeline/scripts** directory in each git repository:

After entering these settings, click on **Add Job** and **Test** and
name the job **Unit Test**. This job will make sure your code repository
was successfully cloned.

Keep the job as **simple** and enter, set the builder type to **shell
script** and enter

**source ./pipeline/scripts/unit_test.sh**

for Test script.

![A screenshot of a cell phone Description automatically
generated](.//media/image16.png)

**Best Practice:** You will note that we are using scripts stored in our
git repository to perform the various tasks for stage jobs versus
entering the script code directly in the Job. It is much easier to
maintain and develop these scripts in a single place outside of the
toolchain itself. Furthermore, once we commit changes to our scripts,
the commit will invoke our pipeline with the new scripts.

To configure your pipeline with the private worker you created, click on
the **Workers tab** and select the private worker you previously
created. ![A screenshot of a social media post Description automatically
generated](.//media/image17.png)

After selecting your private worker, the last step for the Build stage
is to set an environment variable. Click on the **Environment
properties** tab, click **Add Property** and select **Text Property**.
Enter **IMAGE_NAME** as the property name and the name of the image for
your code to be deployed as the value. For this example, we are building
a CI/CD pipeline for the api frontend, so I will enter
**api-frontend-02cn** as for image name. Click **Save** to save the
Build Stage.

![A screenshot of a social media post Description automatically
generated](.//media/image18.png)

That completes the build stage.

#### 1-3-2 Containerize Stage

The next stage we will create is the containerization stage. Click on
**Add Stage** to create a new stage.

![A screenshot of a cell phone Description automatically
generated](.//media/image19.png)

Change the name to **CONTAINERIZE**, leave the rest of the Input step
as-is. On the **input tab**, **Build artifacts** should be the input
type, **BUILD** as the stage and **Fetch Code** for the job. This will
pass the code that was cloned from the git branch so we can create an
image of the code in this step.

![A screenshot of a cell phone Description automatically
generated](.//media/image20.png)

Next, we need to create the jobs that will build our container. The
first job is making sure the Dockerfile is formatted correctly. Click on
**Add Job** and select **Test**. Click on the **Jobs** tab then click
**Add Job** and select **Test.**

![A screenshot of a cell phone Description automatically
generated](.//media/image21.png)

Change the name to **Check Dockerfile**.

Keep the tester type as Simple and for Test script enter:

**source ./pipeline/scripts/check_dockerfile.sh**

![A screenshot of a social media post Description automatically
generated](.//media/image22.png)

Create a second job by clicking **Add Job** and select **build**.

![A screenshot of a cell phone Description automatically
generated](.//media/image23.png)

Name the job **Check Container Registry**. This job will make sure you
have the appropriate permissions and quota to add new images to your IBM
Cloud Registry.

Enter **Container Registry** of the Builder Type. This job requires an
IBM Cloud API Key in order to access the **IBM Cloud Container Registry
Service**. Input your **IBM Cloud API Key** or we will be following best
practices and use **Key Protect** to store our API Key. Since this is
the first time we will be using an IBM Cloud API Key in the toolchain
service we will create a new API key which we will store in Key Protect.
Click the **New** icon.

![A screenshot of a cell phone Description automatically
generated](.//media/image24.png)

On the next screen, give the key a meaningful name. I will name the key
**API Key for zero to cloud native toolchain.**

Check the box **Save this key in secrets for reuse.**

Under Provider, select the **Key Protect** instance we created in Part
3.

Finally, provide a **Secret Name** for the secret that will be stored in
Key Protect. I will use **dev-classic-api-frontend-02cn** as my secret
name.

![A screenshot of a cell phone Description automatically
generated](.//media/image25.png)

After entering all of these settings click OK.

On the next screen, click Authenticate.

![A screenshot of a social media post Description automatically
generated](.//media/image26.png)

### 

Change the **IBM Cloud Region** to US South which is where our container
registry is deployed.

Select the **container registry namespace** you created earlier. The
container registry namespace I created was zero-to-cloud-native.

For the **docker image**, enter **api-frontend-02cn** since this
pipeline is building the frontend api microservice.

Finally, for the build script, enter:

**source ./pipeline/scripts/unit_test.sh**

![A screenshot of a cell phone Description automatically
generated](.//media/image27.png)

Don't click Save quite yet, we need to create one more job for this
stage.

We need to create one more job to create the container image. To do so,
click on **Add Job** and select **Build**.

![A screenshot of a cell phone Description automatically
generated](.//media/image28.png)

Enter **Build Container Image** as the name and **Container Registry**
as the **Builder type**. With the previous job we added an API key for
our toolchain service to our Key Protect instance which we will be able
to use. Click on the **api key** box, and then this time click on the
**key icon**.

![A screenshot of a social media post Description automatically generated](.//media/image29.png)}

### 

On the next screen, select the **Key Protect instance** you created and
the **Secret Name** we create in the previous step. Then click **OK**.

![A screenshot of a social media post Description automatically
generated](.//media/image30.png)

On the next screen, the api key will be populated. Click
**Authenticate**.

![A screenshot of a social media post Description automatically
generated](.//media/image31.png)

Just like we did on the previous job, you will need to change the **IBM
Cloud Region** to US South which is where our container registry is
deployed.

Select the **container registry namespace** you created earlier. The
container registry namespace I created was zero-to-cloud-native.

For the **docker image**, enter **api-frontend-02cn** since this
pipeline is building the frontend api microservice.

For the build script, enter:

**source ./pipeline/scripts/build_container.sh**

![A screenshot of a cell phone Description automatically
generated](.//media/image32.png)

This finishes setting up the jobs, but there are a couple others things
we need to do before saving the stage.

The next thing we need to do is to configure the containerize stage to
use the private worker we created.

Click on the **wokers** tab:

![A screenshot of a cell phone Description automatically
generated](.//media/image33.png)

Next, select the **private worker** you created.

![A screenshot of a social media post Description automatically
generated](.//media/image34.png)

Before clicking save, we need to first set a couple environment
properties.

Click on the Environment Properties tab.

![A screenshot of a social media post Description automatically
generated](.//media/image35.png)

There are three Environment Properties we will need to add. To add a
property, click on Add Property and enter the property type.

![A screenshot of a cell phone Description automatically
generated](.//media/image36.png)

Referring to the table below, create the following environment
properties.

  **Property Type**   **Property Name**   **Value**
  ------------------- ------------------- ------------------
  Text Property       DOCKER_FILE         Dockerfile
  Text Property       DOCKER_ROOT         .
  Properties File     buildprops          build.properties

After entering all of these properties, click Save.

![A screenshot of a social media post Description automatically
generated](.//media/image37.png)

This completes the containerize job.

#### 1-3-3 Deploy Stage

The final stage we need to create is the **deploy stage** where we will
deploy the container into our OpenShift. Click **Add Stage.**

![A screenshot of a cell phone Description automatically
generated](.//media/image38.png)

On the next screen name the stage **DEPLOY**. While on the **Input
tab**, select **CONTAINERIZE** as the Stage to get inputs from and
select the job **Build Container Image**. This will pass the container
image that we will deploy to the deploy stage.

![](.//media/image39.png)

Next we need to add two more jobs. The first one to deploy the image and
the second to make sure the deployment was successful. To create the
Deploy to OpenShift Job, click on **Add Job**, select **Deploy.**

![](.//media/image40.png)

On the next screen, name the job **Deploy to OpenShift**. Select
**Kubernetes** as the Deployer. Just like we did on the **Containerize
Job**, click on the API Key text box, and then click on the Key Protect
key icon.

![A screenshot of a cell phone Description automatically
generated](.//media/image41.png)

On the next screen, enter your **Key Protect instance** and **Secret
Name** for the toolchain service and click **OK**.

![A screenshot of a social media post Description automatically
generated](.//media/image42.png)

The next screen will populate the API Key automatically, click
**Authenticate**.

![A screenshot of a social media post Description automatically
generated](.//media/image43.png)

Scrolling down, enter US South as the **IBM Cloud Region**. Select the
**Resource Group** where your cluster is created which should be
**zero-to-cloud-native**. Next, selected your **cluster name** which
should be **zero-to-cloud-native**.

Finally, for the deploy script, enter:

**source ./pipeline/scripts/deploy_to_kubernetes.sh**

![A screenshot of a cell phone Description automatically
generated](.//media/image44.png)

The last step in our pipeline is to validate that the deployment was
successful. To do so, scroll back to the top of the page and click
**Create Job** and select **Deploy.**

![A screenshot of a cell phone Description automatically
generated](.//media/image45.png)

Change the name to **Check Deployment Health** and deployment type to
**Kubernetes**. Select your API Key from Key Protect the same way as you
did above.

Scrolling down, enter US South as the **IBM Cloud Region**. Select the
**Resource Group** where your cluster is created which should be
**zero-to-cloud-native**. Next, selected your **cluster name** which
should be **zero-to-cloud-native**.

For the deploy script, enter:

**source ./pipeline/scripts/check_health.sh**

![A screenshot of a cell phone Description automatically
generated](.//media/image46.png)

After entering all the information, click on the Workers tab.

![A screenshot of a cell phone Description automatically
generated](.//media/image47.png)

On the next screen, select the Private Worker that we created.

![A screenshot of a social media post Description automatically
generated](.//media/image48.png)

The final step for this job is to set environment properties. Click on
the Environment properties tab.

![A screenshot of a social media post Description automatically
generated](.//media/image49.png)

There are three Environment Properties we will need to add. To add a
property, click on Add Property and enter the property type.

![A screenshot of a cell phone Description automatically
generated](.//media/image36.png)

Referring to the table below, create the following environment
properties.

  **Property Type**   **Property Name**   **Value**
  ------------------- ------------------- -------------------------------
  Text Property       CLUSTER_NAMEPACE    zero-to-cloud-native
  Text Property       DEPLOYMENT_FILE     ./deployments/deployment.yaml
  Properties File     buildprops          build.properties

After entering all of these properties, click Save.

![A screenshot of a cell phone Description automatically
generated](.//media/image50.png)

That completes setting up our classic toolchain with a private worker
for the api frontend! Now, let's test it.

![A screenshot of a social media post Description automatically
generated](.//media/image51.png)

### 1-4 Testing the Classic Delivery Pipeline

To test our pipeline, all we have to do is make a change to the source
code and commit it to GitHub as we have configured our pipeline to
automatically start after a GitHub commit to our repository.

To make a change, start Visual Studio Code, and open the workspace you
created in step 5. Navigate to the api-frontend-02cn folder and click on
the Dockerfile. Simply add a new line anywhere in the file.

![A screenshot of a cell phone Description automatically
generated](.//media/image52.png)

Next, we will need to push the 'change' to GitHub. Start your **GitHub
Desktop**.

Change to the **api-frontend-02cn** as the repository.

Make sure the **current branch** is dev.

You will notice the highlighted changes which should only be adding the
newline.

Then click on **Commit to dev**.

![A screenshot of a social media post Description automatically
generated](.//media/image53.png)

The final step is the Push the changes to GitHub. On the next screen,
click on Push origin.

![A screenshot of a cell phone Description automatically
generated](.//media/image54.png)

Now, navigate back to your browser with the api frontend pipeline and
watch it run!

