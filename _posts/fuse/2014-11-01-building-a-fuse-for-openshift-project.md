---
layout: page-fullwidth
title: Building a Fuse project
subheadline: "Building a Fuse project!"
teaser: "Fuse takes advantage of typical Java tooling to maintain it's lifecycle. In this lab, we'll walk you through building a Fuse project and explore the outputs."
breadcrumb: true
image:
    homepage: openshift_story.jpg
categories:
    - fuse
---

In the previous lab, we cloned the example fuse project and we imported it into JBDS. We also did a simple `mvn install` of the project which builds the java artifacts.
 
Upon closer inspection, we see that this project is a typical Fuse project intended to be deployed into the Fuse OSGI container. We can see this by looking at the top few lines of the `pom.xml` file:

      <groupId>org.jboss.fuse.openshift.examples</groupId>
      <artifactId>camel-hello-world</artifactId>
      <version>1.0-SNAPSHOT</version>
      <packaging>bundle</packaging>
      <name>Camel Hello World</name>
      <description>Camel Hello World in custom Karaf4 assembly</description>
      
The `<packaging>` type is bundle, which means we'll be building an OSGI bundle when we run `mvn install`. From the command line at the root of the project we can see that indeed that's what we've built:

    ceposta@postamac(camel-hello-world (master)) $ unzip -c target/camel-hello-world.jar META-INF/MANIFEST.MF
    Archive:  target/camel-hello-world.jar
      inflating: META-INF/MANIFEST.MF    
    Manifest-Version: 1.0
    Bnd-LastModified: 1442275547430
    Build-Jdk: 1.7.0_75
    Built-By: ceposta
    Bundle-Description: Camel Hello World in custom Karaf4 assembly
    Bundle-ManifestVersion: 2
    Bundle-Name: Camel Hello World
    Bundle-SymbolicName: org.jboss.fuse.openshift.examples.camel-hello-world
    Bundle-Version: 1.0.0.SNAPSHOT
    Created-By: Apache Maven Bundle Plugin
    Import-Package: org.osgi.service.blueprint;version="[1.0.0,2.0.0)"
    Tool: Bnd-1.50.0
    
We can take this bundle and drop it into a running Fuse instance and expect it to run, insofar the dependencies defined by the `Import-Package` meta info. When we start to think about how we want to run this service inside of a Docker environment, and ultimately a more cloud-native environment, we want to explore a more atomic and immutable deployment model

## Immutable infrastructure
The DevOps community has been exploring the idea of immutability of deployments for some time. Just as immutability in the concurrent-programming space has led to simplifications of assumptions with regard to concurrency (you don't have to worry about things changing out from under you and the synchronization penalties you must take when handling an object, for example mutexes), so has the same thing simplified some of the assumptions of deployments. 

We want to be able to deploy to a cloud where-ever that infrastructure resides, whether internal, public, or some hybrid variety. If we can package our applications in such a way that makes them portable with minimal human intervention and manipulation, then we can reduce the friction of deployments and costly human-provoked mistakes. So we focus on deploying our application and its constituent run-time dependencies (operating system, process dependencies, configuration, etc.. everything) as a single atomic unit. Then we can simplify the tooling necessary (no more moving parts, either automated or manual steps, to configure an application depending on where it is, install runtime dependencies that may conflict with other processes, etc) to deploy our applications so that we can deploy as many times as we care to. We can also participate in complex deployment pipelines, confidentally revert deployments to previously known states, and repeat our deployments as often as necesary knowing we've reduced (ideally eliminated) the variability and expect known states each time we deploy, regardless of the environment we're deploying (DEV deploys the same as IST as UAT as PROD).

### Immutablity and Java containers (including OSGI!)
Docker helps get us to a more immutable deployment model, but [how does that fit in with Java containers, especially OSGI containers](https://medium.com/fabric8-io/the-decline-of-java-application-servers-when-using-docker-containers-edbe032e1f30)? 

Most Java app servers and especially OSGI containers were built with the idea of dynamic reinvention of itself at runtime (ie, the process is mutable and can change depending on what's deployed or undeployed at runtime). So what value does OSGI, and consequently the Fuse container, in a "immutable" world?

Well, two things:

* Existing workloads
* Modularity
* Microservices

There are many folks who use Fuse and OSGI containers and have no plans to migrate off of them anytime soon. So having a path for them to deploy their services and take advantage of the immutability constraints may be appealing. Additionally, and more importantly, until there is a suitable modularity system for the JVM (Java 9?), OSGI has been (20+ yrs) and will continue to be a good choice to write modular applications/services on the JVM. 

Lastly, with the current invigoration about microservices, we can rest comfortably knowing that the modularity opinions from the OSGI container have allowed us a path toward microservices easier than our monolithic friends who jamed everything into a single deployment with waning regard for modularity. 

## Building an immutable Fuse deployment
We've run `mvn install` but as we just discussed, that builds us just the Java artifacts that could run inside a container. But instead of manually deploying a container, typing keystrokes at the command line to install our service/appliction, what if we could just produce a fully-configured/deployed container with our maven build? Then we could take that container and deploy it wherever we'd like knowing our application and its configuration is consistently deployed, consistently started (ordering, etc) and can run anywhere a JVM can run?

Let me introduce you to the [Karaf maven plugin][karaf-plugin]. Don't get confused with the [Felix maven plugin][felix-plugin] (which generates the bundle), the [Karaf plugin][karaf-plugin] allows us the generate a fully built Karaf-based distribution with our service/app embedded.

Poke around at the `pom.xml` (in XML mode in JBDS) and find the `karaf-assemble` mvn profile:

        <profile>
          <id>karaf-assemble</id>
          <build>
            <plugins>
              <!-- karaf-maven-plugin creates custom microservice distribution -->
              <plugin>
                <groupId>org.apache.karaf.tooling</groupId>
                <artifactId>karaf-maven-plugin</artifactId>
                <executions>
                  <execution>
                    <id>karaf-assembly</id>
                    <goals>
                      <goal>assembly</goal>
                    </goals>
                    <phase>prepare-package</phase>
                  </execution>
                  <execution>
                    <id>karaf-archive</id>
                    <goals>
                      <goal>archive</goal>
                    </goals>
                    <phase>package</phase>
                  </execution>
                </executions>
              </plugin>
            </plugins>
          </build>
        </profile>
        
Now if we run `mvn install` with the `-Pkaraf-assemble` profile enabled, we should see a fully-built distro of Karaf/Fuse with our application built and deployed as an embedded service:

    mvn clean install -Pkaraf-assemble
    
Now take a look at the `./target` folder and you see the following:

    ceposta@postamac(camel-hello-world (master)) $ ll target/
     total 48728
     drwxr-xr-x  8 ceposta  staff   272B Sep 14 17:53 assembly
     -rw-r--r--  1 ceposta  staff    12M Sep 14 17:54 camel-hello-world-1.0-SNAPSHOT.tar.gz
     -rw-r--r--  1 ceposta  staff    12M Sep 14 17:54 camel-hello-world-1.0-SNAPSHOT.zip
     -rw-r--r--  1 ceposta  staff   6.3K Sep 14 17:54 camel-hello-world.jar
     drwxr-xr-x  4 ceposta  staff   136B Sep 14 17:54 classes
     drwxr-xr-x  3 ceposta  staff   102B Sep 14 17:53 fabric8
     
The entire immutable distro of Karaf (camel-hello-world-1.0-SNAPSHOT.tar.gz or the .zip variety) has been produced! You can now take this and deply it with our service already embedded and configured!
    
## How to configure the Immutable Karaf distro

If you take a look at the `<pluginManagement>` section of the `pom.xml` you'll see the following:

           <plugin>
              <groupId>org.apache.karaf.tooling</groupId>
              <artifactId>karaf-maven-plugin</artifactId>
              <version>${karaf.plugin.version}</version>
              <extensions>true</extensions>
              <configuration>
                <!-- do not include build output directory -->
                <includeBuildOutputDirectory>false</includeBuildOutputDirectory>
                <!-- no startupFeatures -->
                <bootFeatures>
                  <feature>jaas</feature>
                  <feature>management</feature>
                  <feature>log</feature>
                  <feature>system</feature>
                  <feature>aries-blueprint</feature>
    <!-- commented because it pulls in bundles like camel-commands, which should be optional
                  <feature>camel-core</feature>
                  <feature>camel-blueprint</feature>
    -->
                </bootFeatures>
                <bootBundles>
                  <bootBundle>mvn:org.apache.camel/camel-core/${camel.version}</bootBundle>
                  <bootBundle>mvn:org.apache.camel/camel-catalog/${camel.version}</bootBundle>
                  <bootBundle>mvn:org.apache.camel/camel-blueprint/${camel.version}</bootBundle>
                  <bundle>mvn:${project.groupId}/${project.artifactId}/${project.version}</bundle>
                </bootBundles>
              </configuration>
            </plugin>
            
This plugin snippet, declared as a maven plugin configuration, tells the [Karaf maven pluing][karaf-plugin] what to include inside of the Karaf distro. Notice that we can deploy `<bootFeatures>` which control the boot features (instead of the features karaf config file we all know and love). Lastly, note, we're specifically including our project as a bundle at boot time.

## What about Docker?
Well, I'm glad you asked. Because this maven `pom.xml` also includes plugins for generating a docker image based on some of this config. But let's not get too ahead of ourselves, because using OpenShift we may not even need to worry about this. However, this paradigm is _very_ useful for developers and differentiates how we can use Fuse/Microservices on our local-developer boxes using Docker and expect the same behavior from our PaaS. This is a notable difference from competitors that don't allow deploying native docker... some fumbling foundry crap :) .. they'll come along one day... they've [already listened to our advice on some things](https://blog.openshift.com/chose-not-join-cloud-foundry-foundation-recommendations-2015/) :)

[karaf-plugin]: https://karaf.apache.org/manual/latest/developers-guide/karaf-maven-plugin.html
[felix-plugin]: http://felix.apache.org/documentation/subprojects/apache-felix-maven-bundle-plugin-bnd.html
