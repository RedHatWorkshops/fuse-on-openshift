---
layout: page-fullwidth
title: Creating routes
subheadline: "explore"
teaser: "Test out creating routes in OpenShift to be able to access your applications"
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - fuse
---

### Background: Routes 

By default, the *new-app* command does not expose the Service it creates to the
outside world. If you want expose a service as an HTTP endpoint you can easily
do this with a Route. The OpenShift router uses the host HTTP header to
determine where to proxy the incoming request. You can optionally define
security, such as TLS, for the route. If you want your Pods to be accessible to
the outside world, you need to create a route.

#### Exercise 2: Creating a route

Fortunately, creating a Route is a pretty straight-forward process.  You simply
expose the Service. First we want to verify that we don't already have any
existing routes:

	$ oc get routes
    
    NAME      HOST/PORT   PATH      SERVICE   LABELS

Now we need to get the Service name to expose:

	$ oc get services

    NAME        LABELS    SELECTOR                     IP(S)            PORT(S)
    guestbook   <none>    deploymentconfig=guestbook   172.30.208.199   3000/TCP

Once we know the Service name, creating a route is a simple one-command task:

	$ oc expose service guestbook

Verify the route was created with the following command:

	$ oc get routes

    NAME        HOST/PORT      
    guestbook   guestbook.userXX-guestbook.apps.fuse.osecloud.com

You can also verify the route by looking at the project in OpenShift web console:

![Route]({{ site.url }}/images/explore/create-routes.png)

Pretty nifty, huh?  This application is now available at the above URL:

![Route]({{ site.url }}/images/explore/guestbook.png)