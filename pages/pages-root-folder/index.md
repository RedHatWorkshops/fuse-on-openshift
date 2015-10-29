---
#
# Use the widgets beneath and the content will be
# inserted automagically in the webpage. To make
# this work, you have to use › layout: frontpage
#
layout: frontpage
header:
  image_fullwidth: redhat.jpg
widget1:
  title: "OpenShift v3"
  url: 'https://www.openshift.com/products'
  image: openshift.png
  text: 'OpenShift v3 is a developer PaaS built on popular open-source communities like <a href="https://docker.io">Docker</a> and <a href="http://kubernetes.io">Kubernetes</a> that makes it easy to deploy cloud-native apps and microservices using container technology'
widget2:
  title: "JBoss Fuse"
  url: 'http://www.jboss.org/products/fuse/overview/'
  text: 'JBoss Fuse is a lightweight integration platform built on top of popular open-source technology like <a href="http://activemq.apache.org">Apache ActiveMQ</a> and <a href="http://camel.apache.org">Apache Camel</a> that brings powerful integration capabilities to your apps whether deployed as cloud-native microserivces or traditional apps'
  image: 'fuse.png'
widget3:
  title: "Download Materials"
  url: '/download/'
  image: download.jpg
  text: 'The <a href="/workshop-locations/">in-person workshop</a> will have the options to run locally or in a public deployment of OpenShift v3, but for offline labs, please download the requisite material'
permalink: /index.html
---

<div id="videoModal" class="reveal-modal large" data-reveal="">
  <div class="flex-video widescreen vimeo" style="display: block;">
    <iframe width="1280" height="720" src="https://www.youtube.com/embed/3b5zCFSmVvU" frameborder="0" allowfullscreen></iframe>
  </div>
  <a class="close-reveal-modal">&#215;</a>
</div>
