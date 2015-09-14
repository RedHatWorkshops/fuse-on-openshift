---
layout: page-fullwidth
title: Importing a base fuse project
subheadline: "A simple project to start"
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

### Clone the project using JBDS
Once you've [forked the project to your own repo][fork] you can clone it with the JBDS tooling. If you've already done this with the CLI, then you don't need to clone it again with the IDE. This step is just for those who prefer to clone it through the IDE.

[fork]: https://github.com/RedHatWorkshops/camel-hello-world/fork