---
layout: page-fullwidth
title: Leveraging OpenShift to deploy and scale our microservice
subheadline: "OpenShift is a natural place for microservices!"
teaser: "In this lab, we'll deploy our microservices using OpenShift S2i builders"
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - fuse
---

To deploy our microservice with OpenShift using s2i, we'll want to first add it to a git repo and let openshift pull the code directly from our github.com repo.

First, create a new repo in github.com called "camel-microservice"

![Create project]({{ site.url }}/images/microservices/newgithubproject.png)
![Import code]({{ site.url }}/images/microservices/howtoimportgithub.png)

Now we need to push this code up to github.com. Let's navigate to the command line and to the folder that has our camel-rest-service microservice and run:

    git init

This will make our project a local git repository. Then we want to edit the `.gitignore` file to exclude `.settings` and `target` folders:

    echo "target" >> .gitignore; echo ".settings" >> .gitignore
    
Now let's add the new .gitignore file and add all the rest of the files:

    git add .gitignore
    git add .
    git commit -m 'initial commit'
    
Now let's push this repo up to github.com:

    git remote add origin git@github.com:christian-posta/camel-microservice.git
    git push -u origin master
    
Now if we navigate to our repo in the browser, we should see our source code. 

To deploy our microservice, we can now use the `oc new-app` command and point to our code repository on github.com

Make sure you're logged in to an OpenShift instance with the `oc` tool. See the previous labs for help with that.

    oc new-app --strategy=source dhirajsb/fuse-sti~https://github.com/christian-posta/camel-microservice

