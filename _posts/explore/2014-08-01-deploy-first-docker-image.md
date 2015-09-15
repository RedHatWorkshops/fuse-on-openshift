---
layout: page-fullwidth
title: Deploy your first docker image
subheadline: "Dockah Dockah Dockah!"
teaser: "Let's try to deploy a docker image with OpenShift"
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - fuse
---

### Background: Containers and Pods

Before we start digging in we need to understand how containers and pods are
related. Given the morning sessions where we discussed the OpenShift platform
and how it uses containers and pods, we will not be covering the background on
these technologies in this lab.  Instead, we will dive right in and start using
them.

In OpenShift, the smallest deployable unit is a Pod. A pod is a group of one or
more Docker containers deployed together and guaranteed to be on the same host.
From the doc:

    Each pod has its own IP address, therefore owning its entire port space, and
    containers within pods can share storage. Pods can be "tagged" with one or
    more labels, which are then used to select and manage groups of pods in a
    single operation.

Pods can contain multiple Docker instances. The general idea is for a pod to
contain a "server" and any auxiliary services you want to run along with that
server. Examples of containers you might put in a pod are, an Apache HTTPD
server, a log analyzer, and a file service to help manage uploaded files.

Let's look at the pods that were deployed as part of the *smoke* application in
the *userXX-smoke* Project.

	$ oc get pods
    
You should see output similar to the following:

    NAME            READY     REASON       RESTARTS   AGE
    smoke-1-32gkx   1/1       Running      0          11m
    smoke-1-build   0/1       ExitCode:0   0          15m
    smoke-1-fsghf   1/1       Running      0          8m

The above output lists all of the pods in the current Project, including the pod
name, state, restarts, and uptime for the pod.  Once you have a Pod's name, you
can get more information about the Pod's using the *oc get* command.  To make
the output readable, I suggest changing the output type to *JSON* using the
following syntax:

**Note:** Make sure you use the correct Pod name from your output.

	$  oc get pod smoke-1-32gkx -o json
    
You should see something like the following output (which is have truncated due
to space considerations of this workshop manual):

    {
        "kind": "Pod",
        "apiVersion": "v1",
        "metadata": {
            "name": "smoke-1-32gkx",
            "generateName": "smoke-1-",
            "namespace": "userXX-smoke",
            "selfLink": "/api/v1/namespaces/userXX-smoke/pods/smoke-1-32gkx",
            "uid": "d0273a35-36c8-11e5-8992-0a8636c3fd6f",
            "resourceVersion": "161650",
            "creationTimestamp": "2015-07-30T14:39:41Z",
            "labels": {
                "deployment": "smoke-1",
                "deploymentconfig": "smoke",
                "generatedby": "OpenShiftWebConsole",
                "name": "smoke"
            },...............
            
### Exercise 1: Deploying your first Image 

Let's start by doing the simplest thing possible - get a plain old Docker image
to run inside of OpenShift. This is incredibly simple to do. We are going to use
the Kubernetes Guestbook application
(https://registry.hub.Docker.com/u/kubernetes/guestbook/) for this example.

The first thing we want to do is create a new Project called *userXX-guestbook*.
Remember that Projects group resources together. Ensure that you replace
*userXX* with your correct user number:

	$ oc new-project userXX-guestbook

The *new-project* command will automatically switch you to use that Project. You
will see something like the following:

    Now using project "user36-guestbook" on server "https://master.fuse.osecloud.com:8443".

To see all the Projects you have access to, you can simply use *oc get*.

        $ oc get projects
 
You should see a list like the following:

    NAME               DISPLAY NAME   STATUS
    user36-guestbook                  Active
    user36-smoke       Smoke Test     Active

With the new Project created, in order to tell OpenShift to define and run the
Docker image, you can simply execute the following command:

	$ oc new-app kubernetes/guestbook
    
You will see output similar to the following:

    imagestreams/guestbook
    deploymentconfigs/guestbook
    services/guestbook
    Service "guestbook" created at 172.30.208.199 with port mappings 3000.
    Run 'oc status' to view your app.

Pretty easy, huh?

This may take a while to complete depending on whether you are the first or last
student to create your "application". Each OpenShift node has to pull (download)
the Docker image for kubernetes/guestbook from the Docker hub if it does not
already have it locally. You can check on the status of the image download and
deployment by:

1. Going into the web console
1. Select Project *userXX-guestbook* 
1. Select *Browse*
1. Select *Pods* 

Under status you will see pending with the arrows circling (rather than
running).

You can also use the *oc* command line tool and keep checking on the pod
status:

	$ oc get pods 

	NAME                READY     REASON    RESTARTS   AGE
	guestbook-1-xaav1   1/1       Running   0          1m

Whenever OpenShift asks the node's Docker daemon to run an image, the Docker
daemon will check to make sure it has the right "version" of the image to run.
If it doesn't, it will pull it from the specified registry. 

There are a number of ways to customize this behavior. They are documented in
[specifying an
image](https://docs.openshift.com/enterprise/3.0/dev_guide/new_app.html#specifying-an-image)
as well as [image pull
policy](https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/builds_and_image_streams.html#image-pull-policy).

WINNING! These few commands are the only ones you need to run to get a "vanilla"
Docker image deployed on OpenShift 3. This should work with any Docker image
that follows best practices, such as defining an EXPOSE port, not running as the
*root user* or specific user name, and a single non-exiting CMD to execute on start.

You may be wondering how you can access this application. There was a Service
that was created, but Services are only used inside OpenShift - they are not
exposed to the outside world by default. Don't worry though, we will cover that
later in this lab.

**Note:** It is important to understand that, for security reasons, OpenShift 3
does not allow the deployment of Docker images that run as *root* by default.
If you want or need to allow OpenShift users to deploy Docker images that do
expect to run as root (or any specific user), a small configuration change is
needed. You can learn more about the [Docker
guidelines](https://docs.openshift.com/enterprise/3.0/creating_images/guidelines.html)
for OpenShift 3, or you can look at the section on [enabling images to run with
a USER in the
dockerfile](https://docs.openshift.com/enterprise/3.0/admin_guide/manage_scc.html#enable-images-to-run-with-user-in-the-dockerfile).

**Note:** The "new-app" command currently only creates a Service for the first
EXPOSEd port in the Docker image.  If additional Services are required, you can
always create them using the *oc expose* command.

#### Background: Services 

You can see that when we ran the *new-app* command, OpenShift actually created
several resources behind the scenes in order to handle deploying this Docker
image. *new-app* made a Service, which maps to a set of Pods (via labels and
selectors). Services are assigned an IP address and port pair that, when
accessed, balance across the appropriate back end (Pods).

Services provide a convenient abstraction layer inside OpenShift to find a
group of like Pods. They also act as an internal proxy/load balancer between
those Pods and anything else that needs to access them from inside the OpenShift
environment. For example, if you needed more Guestbook servers to handle the
load, you could spin up more Pods. OpenShift automatically maps them as
endpoints to the Service, and the incoming requests would not notice anything
different except that the Service was now doing a better job handling the
requests.

There is a lot more information about
[Services](https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/pods_and_services.html#services),
including the JSON format to make one by hand, in the official documentation.

Now that we understand the basics of what a Service is, let's take a look at the
Service that was created for the kubernetes/guestbook image that we just
deployed.  In order to view the Services defined in your Project, enter in the
following command:

	$ oc get services
    
You should see output similar to the following:

	NAME        LABELS    SELECTOR                     IP(S)            PORT(S)
	guestbook   <none>    deploymentconfig=guestbook   172.30.208.199   3000/TCP
    
In the above output, we can see that we have a Service named *guestbook* with an
IP/Port combination of 172.30.208.199/3000. Your IP address may be different, as
each Service receives a unique IP address upon creation. Service IPs never
change for the life of the Service.

You can also get more detailed information about a Service by using the
following command to display the data in JSON:

	$ oc get service guestbook -o json
    
You should see output similar to the following:

    {
        "kind": "Service",
        "apiVersion": "v1",
        "metadata": {
            "name": "guestbook",
            "namespace": "userXX-guestbook",
            "selfLink": "/api/v1/namespaces/userXX-guestbook/services/guestbook",
            "uid": "65f22d41-36e3-11e5-8992-0a8636c3fd6f",
            "resourceVersion": "177904",
            "creationTimestamp": "2015-07-30T17:50:00Z"
        },
        "spec": {
            "ports": [
                {
                    "name": "guestbook-tcp-3000",
                    "protocol": "TCP",
                    "port": 3000,
                    "targetPort": 3000,
                    "nodePort": 0
                }
            ],
            "selector": {
                "deploymentconfig": "guestbook"
            },
            "portalIP": "172.30.208.199",
            "type": "ClusterIP",
            "sessionAffinity": "None"
        },
        "status": {
            "loadBalancer": {}
        }
    }

Take note of the *selector* stanza. Remember it.

It is also of interest to view the JSON of the Pod to understand how OpenShift
wires components together.  For example, run the following command to get the
name of your *guestbook* Pod:

	$ oc get pods
    
You should see output similar to the following:

    NAME                READY     REASON    RESTARTS   AGE
    guestbook-1-xaav1   1/1       Running   0          17m

Now you can view the detailed data for your pod with the following command:

	$ oc get pod guestbook-1-xaav1 -o json
    
Under the *"metadata"* section you should see the following:

    "labels": {
                "deployment": "guestbook-1",
                "deploymentconfig": "guestbook"
            },

* The Service has *selector* stanza that refers to "deploymentconfig=guestbook".
* The Pod has multiple labels, one of which is "deploymentconfig=guestbook". 

Labels are just key/value pairs. Any Pod in this Project that has a label that
matches the *selector* will be associated with the Service. To see this in
action, issue the following command:

	$ oc describe service guestbook
    
You should see the following output:

    Name:                   guestbook
    Labels:                 <none>
    Selector:               deploymentconfig=guestbook
    Type:                   ClusterIP
    IP:                     172.30.208.199
    Port:                   guestbook-tcp-3000      3000/TCP
    Endpoints:              10.1.1.74:3000
    Session Affinity:       None
    No events.

You may be wondering why only one end point is listed. That is because there is
only one *guestbook* Pod running.  In the next lab, we will learn how to scale
an application, at which point you will be able to see multiple endpoints
associated with the *guestbook* Service.
