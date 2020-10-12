Zero to Cloud-Native with IBM Cloud
===================================

**Kevin Collins ( kevincollins@us.ibm.com )**  

Part 12: Updating Worker Nodes 
==============================

1 - Introduction
-----------------

When deploying an application to OpenShift, it is very important to keep
your environment up to date with minor updates and patches to address
vulnerabilities and performance improvements. With IBM's RedHat
OpenShift on IBM Cloud service, IBM will automatically update the Master
nodes for you. While arguably, it is more important to keep your master
nodes current with the latest patch level it is also important to keep
your worker nodes current as well.

For major releases, such as moving to 4.4 to 4.5, these releases
typically include major new features. To upgrade your cluster to a new
major version this is something that you need to request as part of the
service.

This session will go through both updating worker nodes to new minor
version and then updating the master and workers nodes to a new major
version.

2 - Updating Worker Nodes
--------------------------

Note: This tutorial will go through the steps of updating worker nodes
on a Virtual Private Cloud (VPC). While the same concepts apply, there
are minor differences in updating worker nodes on Classic
Infrastructure. Refer to IBM Cloud documentation here for more details:
<https://cloud.ibm.com/docs/openshift?topic=openshift-update>

A quick note on terminology:  
**Patch:** includes security fixes and keeps the minor version the same.

**Minor:** A minor update moves up the Kubernetes version of the worker
node to the same version as the master. This type of update often
includes changes to the Kubernetes API or other behaviors that you must
prepare your cluster for. Remember that your worker nodes can be only up
to two versions behind the master version (n-2).

Let's start by looking at the versions of our cluster, both master and
worker node versions. To start with, navigate to your list of OpenShift
clusters. From the IBM Cloud console, click on OpenShift then select
Clusters.

![Graphical user interface, text, application Description automatically
generated](.//media/image2.png)

On the next screen, click on your cluster.
![Graphical user interface, text, application Description automatically
generated](.//media/image3.png)


On the next screen, you will see the current version of your master
nodes.

![Graphical user interface, application Description automatically
generated](.//media/image4.png)

Note the version of the master node. Next, click on the worker nodes
tab.

![A picture containing table Description automatically
generated](.//media/image5.png)

Note the version of the worker nodes:

![Graphical user interface, application Description automatically
generated](.//media/image6.png)

You will see that the first worker node has the very latest patch and
the second is a couple subversions behind. The reason why the first
worker node has the most recent patch is because I had to replace a
failed worker node. When you replace a worker node, the latest is
provisioned.

The second worker node, version 4.4.18_1516 is the version that was
originally provisioned with my worker node. You will notice that there
is an information (i) icon next the version. Hovering over the icon, it
tells me there is an update available. I can also tell there is an
update available by comparing the worker node version 4.4.18_1516 to the
master node version 4.4.20_1518.

To update the worker nodes to the latest patch level, simply select one
or more worker nodes and then click update.

![Graphical user interface, table Description automatically
generated](.//media/image7.png)

The way we update the workers is we take the selected worker nodes,
drain all the deployed pods from it, once they are quarantined, we then
we reload the nodes bottom we bring up everything to date. Once they are
back online and healthy and completely up, you can move to the another
group.

Important: all the worker nodes you select to be update will all be
updated at the same time. Make sure you have enough worker nodes
available to handle the workload of your application. A best practice is
to update worker nodes one by one or zone by zone if you using a
multizone cluster. Once the first worker node is updated, move on to the
second, then the third and so forth.

With a properly architected microservices application, your end users
actually don't know his upgrade happened because you will have multiple
instances of different microservices running. As one instance is shut
down and redeployed to a second healthy node your end users don't know
as they don't feel that, they can't tell that this upgrade is taking
place. When we update the master nodes, we ensure availability as we
role through one master node at a time.

One thing to note however, is if you simply repackage a monolithic
application as a container to get some benefits around packaging,
deployments and monitoring and logging you may run into some issues. The
application may still be stateful and when it shuts down and re-deploys
you may feel that. This is one of the reasons it is very important to
follow a proper cloud native application architecture.

3 - Updating Master Nodes
--------------------------

As new major versions of OpenShift come out, you will want to update
your master nodes from time to time to leverage the new features and
functions of OpenShift. For example, moving from OpenShift 4.4 to 4.5
you gain the capabilities of running virtual Machines alongside your
containers leveraging container native virtualization.

Before you can take advantage of these new features of OpenShift, you
will first need to update your master nodes. After the master nodes are
updated then you go back and update your worker nodes. This ensures a
successful transition to a new version of OpenShift without any downtime
to your application.

Updating the master nodes on IBM Cloud is an incredibly easy process.
From your cluster console, simply click on Actions and select Update
Version.

![Graphical user interface, application Description automatically
generated](.//media/image8.png)

On the next screen, simply select the version you want to update to and
select Update.

![Graphical user interface, text, application Description automatically
generated](.//media/image9.png)

It's as easy as that, IBM will automatically the master nodes for you 1
at a time. Check back in an hour or so and you will see that your
cluster master version has been updated. After the master is updated,
you can go back and update all the worker nodes.
