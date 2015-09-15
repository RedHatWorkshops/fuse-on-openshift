---
layout: page-fullwidth
title: Create the scaffolding for your microservice
subheadline: "Microservices make the world a better place"
teaser: "This lab will show you how to create a project that we can customize to build a microservices deployment"
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - fuse
---

This lab will walk you through setting up a maven project that we will use to build out our REST-style microservice. We will use an existing maven archetype that assembles a CDI-based Camel java project that we will then alter to implement the service. 

> We won't give an introduction to microservices; ask the instructor at the on-site workshops and if it's @christianposta giving the workshop, expect a soap-box performance

First, let's create the project using JBDS (though creating the archetype project can also be done from the command line). Click to File->New, and select "Maven project". If you don't see "Maven Project" let's click "Other" and type "maven" into the filter box. 

![choose new]({{ site.url }}/images/microservices/newmvnproject.png)

On the next screen, give a project name, and configure any other project-location details you'd like. Leave the "Create simple project" checkbox **unchecked**. Then click next. 

In the "New Maven project" screen, enter the following into the filter: `io.fabric8.archetypes` and select `java-camel-cdi-archetype`


![choose new]({{ site.url }}/images/microservices/archselection.png)

Give it the maven coordinates:

groupId: org.openshift.workspace
artifactId: camel-rest-service
version: 1.0-SNAPSHOT


You should now have a working maven project.

> to create the project from the command line type in this command: `mvn archetype:generate -DarchetypeGroupId=io.fabric8.archetypes -DarchetypeArtifactId=java-camel-cdi-archetype -Dversion=2.2.28 -DgroupId=org.test -DartifactId=foo -Dversion=1.0-SNAPSHOT`

Yay! now we have a maven project that we can use as the base for our project. If you take a look at the pom.xml, you'll see some interesting plugins and configurations including for the `docker maven plugin`. If you have Docker installed on your local machine (this is an optional step), you could run `mvn clean install docker:build` and this will build a docker image from the project. But since we're using OpenShift, you can use Docker natively on your box, but you can also deploy directly using [S2I](https://docs.openshift.org/latest/dev_guide/new_app.html#specifying-source-code).

## Creating the REST services

Let's use [Camel's new REST DSL](http://camel.apache.org/rest-dsl.html) to build and expose our RESTful service. 

Camel's REST DSL uses an in-process servlet container that you can specify; for example, you can have an embedded Jetty or Netty to expose your HTTP endpoints and invoke the Camel integration functionality. Let's add the following dependency to the pom.xml so we can boot up the embedded servlet container:

    <dependency>
        <groupId>org.apache.camel</groupId>
        <artifactId>camel-netty-http</artifactId>
    </dependency>
    
    
Now, let's open up `MyRoutes.java` in the src/main/java directory and we're gonna edit it to look like this:


    package org.openshift.workshop.camel_rest_service;
    
    import org.apache.camel.builder.RouteBuilder;
    import org.apache.camel.cdi.ContextName;
    import org.apache.camel.model.rest.RestBindingMode;
    
    /**
     * Configures all our Camel routes, components, endpoints and beans
     */
    @ContextName("myCdiCamelContext")
    public class MyRoutes extends RouteBuilder {
    
        @Override
        public void configure() throws Exception {
            restConfiguration().component("netty-http").port(9091).bindingMode(RestBindingMode.auto);
            
            rest("/service")
            .get("/sayhello").to("direct:sayHello");
           
          
            from("direct:sayHello").log("hello, from phxjug").setBody(constant("hello!"));
        }
    
    }


This creates a Camel route using the REST DSL that exposes a simple GET service at the `/rest/hello` path.

To test this out, add the following section to the `<build>` part of your `pom.xml`

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
    
    
Then you can run `mvn exec:java` or Run As-->Maven build... from the IDE

You should see something like this in the console:

    2015-09-15 13:41:02,552 [cdi.Main.main()] INFO  CdiCamelContext                - Total 0 routes, of which 0 is started.
    2015-09-15 13:41:02,553 [cdi.Main.main()] INFO  CdiCamelContext                - Apache Camel 2.15.3 (CamelContext: myCdiCamelContext) started in 0.136 seconds
    2015-09-15 13:41:02,673 [cdi.Main.main()] INFO  CdiCamelContext                - Route: route2 started and consuming from: Endpoint[direct://sayHello]
    2015-09-15 13:41:02,731 [cdi.Main.main()] INFO  HttpServerBootstrapFactory     - BootstrapFactory on port 9091 is using bootstrap configuration: [NettyServerBootstrapConfiguration{protocol='tcp', host='postamac.local', port=9091, broadcast=false, sendBufferSize=65536, receiveBufferSize=65536, receiveBufferSizePredictor=0, workerCount=0, bossCount=1, keepAlive=true, tcpNoDelay=true, reuseAddress=true, connectTimeout=10000, backlog=0, serverPipelineFactory=org.apache.camel.component.netty.http.HttpServerPipelineFactory@5b91cf45, nettyServerBootstrapFactory=null, options=null, ssl=false, sslHandler=null, sslContextParameters='null', needClientAuth=false, enabledProtocols='TLSv1,TLSv1.1,TLSv1.2, keyStoreFile=null, trustStoreFile=null, keyStoreResource='null', trustStoreResource='null', keyStoreFormat='JKS', securityProvider='SunX509', passphrase='null', bossPool=null, workerPool=null, networkInterface='null'}]
    2015-09-15 13:41:02,787 [cdi.Main.main()] INFO  TCPNettyServerBootstrapFactory - ServerBootstrap binding to postamac.local:9091
    2015-09-15 13:41:02,802 [cdi.Main.main()] INFO  NettyConsumer                  - Netty consumer bound to: postamac.local:9091
    2015-09-15 13:41:02,805 [cdi.Main.main()] INFO  CdiCamelContext                - Route: route1 started and consuming from: Endpoint[http://postamac.local:9091/service/sayhello?httpMethodRestrict=GET]
    2015-09-15 13:41:02,855 [cdi.Main.main()] INFO  MainSupport                    - Apache Camel 2.15.3 starting
    
Note, the log line that says the route is bound to port `9091` We can try to hit that URL in our browser or with curl:

    curl -s https://postamac.local:9091
    
    
We should see "hello" returned

That's it for this lab. In the next lab, we'll explore some packaging plugins that give us an immutable deployment and how we can plug that into OpenShift's s2i builders.