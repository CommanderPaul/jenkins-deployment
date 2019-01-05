# Jenkins Dockerfile Modification
Only a few lines in the official Dockerfile need to be changed for ARM and plugin customization.

The changes are to the base image, adding java, and hard coding the armhf url for krallin.
The unzip command needs to be added for plugins.
Adding plugins makes build super slow.
```
git clone https://github.com/jenkinsci/docker.git
```
## Changes to Dockerfile
### Use arm compatable base image
```
# FROM openjdk:8-jdk
FROM resin/rpi-raspbian
```
### Include java 8, unzip for plugins, and libltdl for connecting to host docker
```
# RUN apt-get update && apt-get install -y git curl && rm -rf /var/lib/apt/lists/*
RUN apt-get update && apt-get install -y libltdl-dev unzip git curl oracle-java8-jdk && rm -rf /var/lib/apt/lists/*
```
### Change jenkins user id and group id
```
# ARG uid=1000
# ARG gid=1000
ARG uid=500
ARG gid=500
```
### Get the right binaries for krallin
```
#RUN curl -fsSL https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-static-$(dpkg --print-architecture) -o /sbin/tini \
#&& curl -fsSL https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-static-$(dpkg --print-architecture).asc -o /
RUN curl -fsSL https://github.com/krallin/tini/releases/download/v0.18.0/tini-static-armhf -o /sbin/tini \
  && curl -fsSL https://github.com/krallin/tini/releases/download/v0.18.0/tini-static-armhf.asc -o /sbin/tini.asc /
```
### Add plugins
* Plugins can be found at the plugin index: https://plugins.jenkins.io
* Use the light grey ID in the upper left to identifiy the plugin.
* Plugin RUN statements can be added at the end of the Dockerfile.
```
# Distributed Builds plugins
RUN /usr/local/bin/install-plugins.sh ssh-slaves

# install Notifications and Publishing plugins
RUN /usr/local/bin/install-plugins.sh email-ext
RUN /usr/local/bin/install-plugins.sh mailer
RUN /usr/local/bin/install-plugins.sh slack
RUN /usr/local/bin/install-plugins.sh dockerhub-notification

# Artifacts
RUN /usr/local/bin/install-plugins.sh htmlpublisher

# UI
RUN /usr/local/bin/install-plugins.sh greenballs
RUN /usr/local/bin/install-plugins.sh simple-theme-plugin

# Scaling
RUN /usr/local/bin/install-plugins.sh kubernetes

# Building
RUN /usr/local/bin/install-plugins.sh python

# Programming
RUN /usr/local/bin/install-plugins.sh golang

# SCM
RUN /usr/local/bin/install-plugins.sh github
```
