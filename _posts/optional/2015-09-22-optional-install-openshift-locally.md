---
layout: page-fullwidth
title: Install OpenShift Locally (Optional)
subheadline: "optional"
teaser: "For the onsite workshop, use the environment set up by the instructor. If you'd like to do these labs and further explore openshift outside of the workshop (ie, locally, on your laptop), follow these instructions"
breadcrumb: true
categories:
    - fuse
---

## <a name="docker"></a>Install Docker and OpenShift on your machine (optional)
This step is optional. For those folks who will not attend the workshops in person, or for those people who did attend in person and would like to run these labs on their local computer, you have the option to install Docker and OpenShift onto your local machine. Since Docker and Linux run on OpenShift (preferably  RHEL :) ), we'll be using a vagrant + virtualbox set up to run the image. 
 
First, you'll need to download and install [vagrant][dlvagrant], [VirtualBox][dlvbox], and [Docker](https://docs.docker.com/installation/binaries/) (you can use the latest version, but the 1.6.2 version on my laptop has been proven to work :) ).

Then, we'll use the [fabric8.io][fabric8] image of OpenShift which has everything installed, configured, and ready to use out of the box. [Follow the instructions for setting up the OpenShift VM using vagrant from the fabric8 website][installvm]. Raise your hands (in person) or [open a ticket for the github repo][repo] if you have issues getting this installation working correctly.

It would be nice to be able to use the openshift tools (client tools installed from above) and the docker CLI tools directly from your host machine as well. To do this, let's set the following environment variables:


* export `KUBERNETES_MASTER=https://172.28.128.4:8443`
* export `KUBERNETES_DOMAIN=vagrant.f8`
* export `KUBERNETES_TRUST_CERT=true`
* export `DOCKER_HOST=tcp://172.28.128.4:2375`
* export `DOCKER_TLS_VERIFY=`
* export `DOCKER_CERT_PATH=`

Lastly, we'll want to install the docker binaries on your local development machine [using the instructions from the Docker website](https://docs.docker.com/installation/binaries/)

Following these instructions will have docker and openshift running on the VirtualBox VM that was installed and have connectivity between your local Docker client and the VM (so you can run Docker from the command line as though it was Native). 

Note, those are the environment export instructions for linux or MacOSX. For windows you'll need to add them to your environment the way windows does it (I'm really not sure how that is; feel free to PR this documentation to add it. It's probably Config Panel->System but I'm not totally sure :) see above for more links for how to configure environment variables for Windows)

[download]: http://www.jboss.org/products/devstudio/download/
[repo]: https://github.com/RedHatWorkshops/fuse-on-openshift
[stash]: https://www.atlassian.com/software/stash
[gitlab]: https://about.gitlab.com
[gogs]: http://gogs.io
[dlvagrant]: https://www.vagrantup.com/downloads.html
[dlvbox]: https://www.virtualbox.org/wiki/Downloads
[fabric8]: http://fabric8.io
[installvm]: http://fabric8.io/guide/getStarted/vagrant.html
