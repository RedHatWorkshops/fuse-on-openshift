---
layout: page-fullwidth
title: Exploring the flexible deployment options of Fuse on OpenShift
subheadline: "Let's deploy!"
teaser: "Fuse on OpenShift has many deployment options and you're welcome to select the best for your environment. In this lab, we'll explore some of those deployment options and discuss which may be right for you"
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - fuse
---

When you wish to deploy your application on OpenShift, you have a few options as a developer:

* Source
* Docker image
* From a template
* As an instant app

## Source
As a developer, you worry about your code and let the PaaS take care of how to build, deploy, and manage it. With OpenShift built on top of Kubernetes and Docker, OpenShift takes care of building your application source code into a Docker image and deploying it. OpenShift can also watch the status of the source code (webhooks, etc) and the status of the underlying base Docker images (ImageStreams, etc) and trigger redeploys automatically. For example, if you've deployed your application on the PaaS which makes use of RHEL7 as a base image, and a vulnerability in `bash` is found (ahem.. shellshock), then the base image can be updated and OpenShift can trigger an automatic redeploy of all apps built with the RHEL7 base.

OpenShift includes a [Source 2 Image](https://github.com/openshift/source-to-image) build option which allows [you to specify a git repository that hosts your code](https://docs.openshift.org/latest/dev_guide/new_app.html#specifying-source-code).

For example, you could deploy an application from its git repo like this:

    oc new-app https://github.com/yourorg/hello-ruby-app
    
OpenShift will automatically find that this is a "ruby" app and will use a ruby s2i builder. You can also specify a specific s2i builder to use:

    oc new-app openshift/ruby-20-centos7~https://github.com/yourorg/hello-ruby-app
    
Note the `~` character to delimit the S2I image and the git repo.


## Docker image 
OpenShift supports native Docker image deployments. This allows teams who have an existing, complicated build process, or images from third-party vendors to also deploy into the PaaS. 
 
For example, in the example above, if OpenShift detected a `Dockerfile` in the github repo, it would automatically try to build the Dockerfile and deploy the app as a docker image.

You can also specify a specific docker image like this:

    oc new-app mysql
    
## From a template
You can also create re-usable templates with parameters that can be substituted at deploy time. For example, you could create a template for you application that specifies docker images, service, replication controllers, and pods and then parametrize the values that are specific for certain environment deploys (DEV, QA, PROD, etc) and then move entire sets of artifacts consistently and atomically. 

## As an instant app
An instant app is a special type of template that is a bit more generic and allows you to deploy a framework with your code inside of it. For example:

* Rails (ruby) instant app that you point your code to
* Django (python) instant app
* node.js
* Cake (PHP)

For this lab, we already have an instant app builder declared in our `camel-hello-world` repository named `os3-app-template.json`

In the next lab, we'll deploy this app!

