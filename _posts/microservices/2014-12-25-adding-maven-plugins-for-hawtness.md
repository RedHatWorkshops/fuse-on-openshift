---
layout: page-fullwidth
title: Adding maven plugins for hawtness
subheadline: "mvn plugin hawtness"
teaser: "Work with docker and OpenShift using tools that you're familiar with as a Java developer, including maven plugins. This lab will introduce you to those plugins tha allow you to containerize your java app/service"
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - microservices
---

We mentioned _immutable deployment_ theory in the previous labs and for our microservice project in these sets of labs, we want to be able to package up everything in a single deployment unit with all of the necessary dependencies (java dependencies) and deploy that with a JVM.

Just like in the karaf-based labs, we want to be able to pacakge things up as a `zip` or `tar.gz` To do this, let's edit the `pom.xml` file and add this plugin:


    <pluginManagement>
    		<plugins>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.2.1</version>
                    <configuration>
                        <mainClass>org.apache.camel.cdi.Main</mainClass>
                        <includePluginDependencies>false</includePluginDependencies>
                    </configuration>
                </plugin>
    		</plugins>
    </pluginManagement>
    
    
This allows us to package up our app in a single distributable. To be able to reach this plugin, let's add a `<pluginRepository>` configuration to our pom.xml so we can hit the fuse/jboss mvn repos:

    <pluginRepositories>
        <pluginRepository>
            <id>fusesource</id>
            <url>http://repo.fusesource.com/nexus/content/groups/m2-proxy/</url>
        </pluginRepository>
    </pluginRepositories>

Now let's run the build (can be done with JBDS or command line... for JBDS, click RunAs-->Maven Build...)

    mvn package hawt-app:build -DskipTests
    
    
Let's take a look at what was generated on disk in the `./target` folder:

    -rw-r--r--  1 ceposta  staff    13M Sep 15 14:02 camel-rest-service-0.0.1-SNAPSHOT-app.tar.gz
    -rw-r--r--  1 ceposta  staff   8.0K Sep 15 14:02 camel-rest-service.jar
    drwxr-xr-x  6 ceposta  staff   204B Sep 15 14:02 classes
    drwxr-xr-x  3 ceposta  staff   102B Sep 15 14:02 fabric8
    drwxr-xr-x  4 ceposta  staff   136B Sep 15 14:02 hawt-app
    drwxr-xr-x  3 ceposta  staff   102B Sep 15 14:02 maven-archiver
    drwxr-xr-x  4 ceposta  staff   136B Sep 15 13:40 test-classes
    
Yay! Our distribution of our microservice has been packaged as a tar.gz (this can be configured to output zip also) and can be run without having to deploy to an app server. It also includes a "run" script which knows how to configure the classpath (ie, the classpath ordering is preserved from how maven interprets the classloading order) and run the service

We could hand this off to an ops team to deploy, but let's build this deployment with docker using OpenShift! 