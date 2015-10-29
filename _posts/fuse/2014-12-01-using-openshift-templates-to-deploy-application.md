---
layout: page-fullwidth
title: Use OpenShift to deploy Fuse
subheadline: "fuse"
teaser: "Let's use OpenShift source 2 image builders to deploy our Fuse application"
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - fuse
---

From the command line, let's create our project using s2i (source 2 image) builders. Recall from the previous labs,
s2i allows us to build our projects directly from source without have to know how to build the docker images and 
start/stop containers, etc. 

    oc new-app --strategy=source fabric8/s2i-karaf:1.1.5~https://github.com/<YOUR-REPO>/camel-hello-world 
    
Now, we can create a new app with the command-line, or the web console. For this lab, let's use the webconsole.

Navigate to the console, and login (the URL will be provided for the in-person workshop, otherwise if you have it running locally on your laptop it will be `https://vagrant.f8:8443` if you've set up the [fabric8.io](http://fabric8.io) vagrant image.



Off to the left, find the "Browse" icon and click it. You should see a list of services running including the new build that's running inside of a Kubernetes pod.

![insta-app]({{ site.url }}/images/fuse/buildrunning.png)

To see the current status of the build, and view it's logs to verify it's coming along, let's go back to the command line and type the following:

    [vagrant@vagrant ~]$ oc get build
    NAME                   TYPE      STATUS     POD
    camel-hello-world-1    Source    Running    camel-hello-world-1-build
    
We should see that the build is currently running. What is it doing? Let's peak inside:

    [vagrant@vagrant ~]$ oc build-logs camel-hello-world-1
    
You should see the maven build proceeding and ultimately pushing a docker image to the internal docker registry. You should see something like this when the build is completed:

    I0915 14:26:35.280102       1 sti.go:153] Successfully pushed 172.30.20.184:5000/default/camel-hello-world:latest
    
    
Now, let's verify that our camel-hello-world pod is correctly running:

    [vagrant@vagrant ~]$ oc get pod
    NAME                                 READY     STATUS       RESTARTS   AGE
    camel-hello-world-1-build            0/1       ExitCode:0   0          8m
    camel-hello-world-1-doxgd            1/1       Running      0          26s
    
The build completed and has exited and now there is a running instance of the pod named `camel-hello-world-1-doxgd` is running. You can try some of the previous steps like killing the pod `oc delete pod camel-hello-world-1-doxgd` and watch the Replication Controller bring back the pod. Let's take a look at what that pod is doing:
 
 
    oc logs camel-hello-world-1-doxgd
    
    2015-09-15 14:28:11,756 | INFO  | #0 - timer://foo | log-route                        | 21 - org.apache.camel.camel-core - 2.15.2 | >>> Hello from Fabric based Camel route! : 
    2015-09-15 14:28:16,756 | INFO  | #0 - timer://foo | log-route                        | 21 - org.apache.camel.camel-core - 2.15.2 | >>> Hello from Fabric based Camel route! : 
    2015-09-15 14:28:21,767 | INFO  | #0 - timer://foo | log-route                        | 21 - org.apache.camel.camel-core - 2.15.2 | >>> Hello from Fabric based Camel route! : 
    2015-09-15 14:28:26,761 | INFO  | #0 - timer://foo | log-route                        | 21 - org.apache.camel.camel-core - 2.15.2 | >>> Hello from Fabric based Camel route! : 
    2015-09-15 14:28:31,761 | INFO  | #0 - timer://foo | log-route                        | 21 - org.apache.camel.camel-core - 2.15.2 | >>> Hello from Fabric based Camel route! : 
    2015-09-15 14:28:36,762 | INFO  | #0 - timer://foo | log-route                        | 21 - org.apache.camel.camel-core - 2.15.2 | >>> Hello from Fabric based Camel route! : 
    2015-09-15 14:28:41,762 | INFO  | #0 - timer://foo | log-route                        | 21 - org.apache.camel.camel-core - 2.15.2 | >>> Hello from Fabric based Camel route! : 
    2015-09-15 14:28:46,762 | INFO  | #0 - timer://foo | log-route                        | 21 - org.apache.camel.camel-core - 2.15.2 | >>> Hello from Fabric based Camel route! : 
    2015-09-15 14:28:51,762 | INFO  | #0 - timer://foo | log-route                        | 21 - org.apache.camel.camel-core - 2.15.2 | >>> Hello from Fabric based Camel route! : 
    
    
Yay! the camel route is deployed and logging correctly!

This lab illustrated a few points:

* How to deploy using an OpenShift template / instant app builder
* How to deploy with the webconsole
* How to navigate the currently running builds and pods with the command line