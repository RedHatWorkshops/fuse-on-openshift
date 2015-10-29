---
layout: page-fullwidth
title: Importing a base fuse project
subheadline: "fuse"
teaser: "Use this base project to get started with! This lab  will show you all of the salient pieces necessary to use JBoss Fuse in a docker environment, like OpenShift"
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - fuse
---

For this lab, we'll clone an existing Fuse project and walk you through it and eventually deploy it to OpenShift.

## Clone the project

There are a couple different ways to import this first project. You are encouraged to [fork the sample repository][fork] into your own github.com repo. This will allow you to make changes to it and explore it deeper than the labs may guide (in future iterations of the labs, we may also dig into making changes, and already having it forked to your repo would be handy). 
  
The [sample project is located here][fork]:

    https://github.com/RedHatWorkshops/camel-hello-world
    
Once you fork the repo, it will live in your github.com account, for example `https://github.com/christian-posta/camel-hello-world/` So now let's clone the repo locally using either the command line tools or the JBDS IDE:

### Clone with the command-line tools

Verify you have a git CLI tool on the command line:

    git version
    
If this command line returns something like `git version 1.9.5 (Apple Git-50.3)` then you should be good to use the command line. If not, jump ahead to using the IDE directly.
    
Now navigate to the directory where you created your JBDS workspace and try cloning your repo:

    git clone git@github.com:<YOUR ACCOUNT>/camel-hello-world.git
    
If this completes successfully, you can now `cd camel-hello-world` and see the sample project we'll use for this lab.

Next, we'll want to "import" the project into JBDS. 

Click File->Import and that should take you to a dialog. Enter "Existing" into the filter and you should see "Existing Maven project" in the list:

![Existing]({{ site.url }}/images/fuse/import-existing.png)

Select "Existing Maven project" and then you can navigate to an existing project (ie, the Maven project we just cloned from github.com) and import it by clicking the "Browse" button and finding it on the file system

![Select]({{ site.url }}/images/fuse/select-project.png)

Once you've imported it, you should have it in the workspace:

![success]({{ site.url }}/images/fuse/success.png)


### Clone the project using JBDS
Alternatively, you can clone the project and import it all in one go with JBDS. If you've already done this with the command line, then you don't need to clone it again with the IDE. This step is just for those who prefer to clone it through the IDE.

Click File->Import and type in "maven" into the filter. You should se an option to select an existing project from SCM:

![import git]({{ site.url }}/images/fuse/import-git.png)

Then you can input the correct URL to your git repo and click `Finish` or click Next to choose a location to clone into:

![Git URI]({{ site.url }}/images/fuse/git-uri.png)

You should now have the sample Fuse project correctly imported into the workspace. 

### Building the project
Now that you've got the project checked out, let's build it and make sure Maven can build it correctly:

![build]({{ site.url }}/images/fuse/build.png)

At the end of the build, if everything built correctly, you should see a "Build Success" message like below:

![build]({{ site.url }}/images/fuse/build-success.png)


[fork]: https://github.com/RedHatWorkshops/camel-hello-world/fork