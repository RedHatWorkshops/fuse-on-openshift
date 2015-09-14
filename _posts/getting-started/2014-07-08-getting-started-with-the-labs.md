---
layout: page-fullwidth
title: Getting Started with the Labs
subheadline: "Get the right tools!"
date: 2014-07-01T16:10:30-07:00
meta_teaser: "To get started on the right foot with this workshop, let's download all of the required software and set up the required tools!"
teaser: "These labs require some basic developer tools which this guide helps you install and set up. You can <a href='/workshop-locations/'>attend the workshop in person in select cities</a> or you can work at your own pace off-line. The tools will help you get along whether using the in-person, cloud-deployed tools or the on-your-own-laptop tools"
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - getting-started
---

## Welcome!
Welcome to the JBoss Fuse on OpenShift workshop! 

The labs for this workshop will guide you through using JBoss Fuse integration components on a docker-based PaaS -- OpenShift v3
This getting started guide will walk you through setting up the following components

* [JBoss Developer Studio](#jbds)
* [OpenShift v3 client tools](#os3)
* [Github.com account](#gh)
* [Docker and OpenShift on your local machine](#docker) (optional)


## <a name="jbds"></a>Install JBoss Developer Studio
To install JBoss Developer Studio (JBDS), first download the distribution appropriate for your operating system. You can select either the full installation (bundled with EAP) or the stand-alone installer (an executable Java JAR). [Navigate to the download page][download] 

Stand-alone installer

![Standalone installer](/images/jbds-install/jbds-table.png)

Once you've downloaded the distribution, you can run the installer by either double-clicking the jar in your file-system explorer, or you can run from the command line (make sure `JAVA_HOME` environment variable is set):

> java -jar jboss-devstudio-8.1.0.GA-installer-standalone.jar

Follow the on-screen instructions to complete the installations.

### Install the integration pack

The Fuse tools are part of the integration pack, that you can install by selecting the "Software/Update" tab and choose "JBoss Fuse Development". Then follow the on-screen instructions to install the tools.  

![Choose Fuse](/images/jbds-install/choosefuse.png)


   
### Smoke test the installation

To verify you that you have properly installed the tools, click "File->>New->Other" and do a search for "fuse". 

![NewFuse](/images/jbds-install/newfuse.png)

If you have the option to create a new "Fuse" project, you've installed things correctly! WooHoo! If not, yell and kick the instructor (if live) or file an issue in [this git repository][repo]

## <a name="os3"></a>Install OpenShift v3 client tools

You will be interacting with an OpenShift environment that is installed on top
of Amazon's EC2 infrastructure. The OpenShift environment for the road show
consists of the following:

* One OpenShift Master
* One OpenShift Node providing an "infra(structure)" region
* Five OpenShift Nodes providing the "demo" region

The OpenShift Master provides both the API endpoint for the CLI as well as the
OpenShift web console. It is essentially the only system you will directly
interact with.

The "infra(structure)" region is where OpenShift's internal Docker registry and
OpenShift's router are running. The "demo" region is where all of your builds
and application instances will run. 

This topology of "infra" and "demo" is completely configurable and very advanced
topologies can be crafted to suit the needs of your organization.

###**Command Line Interface**

OpenShift 3 ships with a feature rich web console as well as command line tools to provide users with a nice interface to work with applications deployed to the platform.  The OpenShift tools are a single executable written in the Go programming language and is available for the following operating systems:

- Microsoft Windows
- Apple OS X
- Linux

####**Downloading the tools**
During this lab, we are going to download the client tool and add them to our operating system $PATH environment variables so the executable is accessible from any directory on the command line. 

The first thing we want to do is download the correct executable for your operating system as linked below:

- [Microsoft Windows](http://repo.openshift3roadshow.com/clients/oc-3.0.1.0-windows.zip)
- [Apple OS X](http://repo.openshift3roadshow.com/clients/oc-3.0.1.0-macosx.tar.gz)
- [Linux](http://repo.openshift3roadshow.com/clients/oc-3.0.1.0-linux.tar.gz)


Once the file has been download, you will need to extract the contents as it is a compressed archive.  I would suggest saving this file to the following directories:

**Windows:**

	C:\OpenShift

**OS X:**

	~/OpenShift

**Linux:**

	~/OpenShift

####**Extracting the tools**
Once you have the tools downloaded, you will need to extract the contents:

**Windows:**
In order to extract a zip archive on windows, you will need a zip utility installed on your system.  With newer versions of windows (greater than XP), this is provided by the operating system.  Just right click on the downloaded file using file explorer and select to extract the contents.

**OS X:**
Open up a terminal window and change to the directory where you downloaded the file.  Once you are in the directory, enter in the following command:

	$ tar zxvf oc-3.0.1.0-macosx.tar.gz

**Linux:**
Open up a terminal window and change to the directory where you downloaded the file.  Once you are in the directory, enter in the following command:

	$ tar zxvf oc-3.0.1.0-linux.tar.gz

####**Adding *oc* to your PATH**

**Windows:**
Because changing your PATH on windows varies by version of the operating system, we will not list each operating system here.  However, the general workflow is right click on your computer name inside of the file explorer.  Select Advanced system settings. I guess changing your PATH is considered an advanced task? :) Click on the advanced tab, and then finally click on Environment variables.  Once the new dialog opens, select the Path variable and add ";C:\OpenShift" at the end.  For an easy way out, you could always just copy it to C:\Windows or a directory you know is already on your path. For more detailed instructions:



[Windows XP](https://support.microsoft.com/en-us/kb/310519)

[Windows Vista](http://banagale.com/changing-your-system-path-in-windows-vista.htm)

[Windows 7](http://geekswithblogs.net/renso/archive/2009/10/21/how-to-set-the-windows-path-in-windows-7.aspx "Windows 7")

[Windows 8](http://www.itechtics.com/customize-windows-environment-variables/)

Windows 10 - Follow the directions above.

**OS X:**

	$ export PATH=$PATH:~/OpenShift

**Linux:**
	
	$ export PATH=$PATH:~/OpenShift


####**Verify**
At this point, we should have the oc tool available for use.  Let's test this out by printing the version of the oc command:

	$ oc version

You should see the following:

    oc v3.0.1.0-503-g7cc6deb
    kubernetes v1.0.0

If you get an error message, you have not updated your path correctly.  If you need help, raise your hand and the instructor will assist.

## <a name="gh"></a>Create a github.com account
Parts of this lab will use the PaaS which can use git repos from which to build source code on demand. In this workshop, we'll rely on github.com but for a more realistic usage, you'd use your own internal git repos (eg, [Stash][stash], [Gitlab][gitlab], [gogs][gogs], etc)
 
If you navigate over to [http://github.com/join](http://github.com/join), you will be greated with a signup form. Complete the form and create your repo/account:

![github.com](/images/jbds-install/github.png)

## <a name="docker"></a>Install Docker and OpenShift on your machine (optional)
This step is optional. For those folks who will not attend the workshops in person, or for those people who did attend in person and would like to run these labs on their local computer, you have the option to install Docker and OpenShift onto your local machine. Since Docker and Linux run on OpenShift (preferably  RHEL :) ), we'll be using a vagrant + virtualbox set up to run the image. 
 
First, you'll need to download and install [vagrant][dlvagrant] and [VirtualBox][dlvbox].

Then, we'll use the [fabric8.io][fabric8] image of OpenShift which has everything installed, configured, and ready to use out of the box. [Follow the instructions for setting up the OpenShift VM using vagrant from the fabric8 website][installvm]. Raise your hands (in person) or [open a ticket for the github repo][repo] if you have issues getting this installation working correctly.

Following these instructions will have docker and openshift running on the VirtualBox VM that was installed. It would be nice to be able to use the openshift tools (client tools installed from above) and the docker CLI tools directly from your host machine as well. To do this, let's set the following environment variables:


* export `KUBERNETES_MASTER=https://172.28.128.4:8443`
* export `KUBERNETES_DOMAIN=vagrant.f8`
* export `KUBERNETES_TRUST_CERT=true`
* export `DOCKER_HOST=tcp://172.28.128.4:2375`
* export `DOCKER_TLS_VERIFY=`
* export `DOCKER_CERT_PATH=`

Note, those are the environment export instructions for linux or MacOSX. For windows you'll need to add them to your environment the way windows does it (I'm really not sure how that is; feel free to PR this documentation to add it. It's probably Config Panel->System but I'm not totally sure :) )
