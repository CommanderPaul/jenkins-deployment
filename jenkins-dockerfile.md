looks like only difference is the base image, adding java, and
hard coding the armhf url.  The hard coding is strange.  It appears that the
default raspbian shell can't evlauate $(something) notation.
had to add unzip command for plugins, makes build super slow too.

** changes to Dockerfile....

# use arm compatable base image
# FROM openjdk:8-jdk
FROM resin/rpi-raspbian

# include java 8
# RUN apt-get update && apt-get install -y git curl && rm -rf /var/lib/apt/lists/*
RUN apt-get update && apt-get install -y unzip git curl oracle-java8-jdk && rm -rf /var/lib/apt/lists/*

# get the right binaries for krallin
#RUN curl -fsSL https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-static-$(dpkg --print-architecture) -o /sbin/tini \
#&& curl -fsSL https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-static-$(dpkg --print-architecture).asc -o /

RUN curl -fsSL https://github.com/krallin/tini/releases/download/v0.18.0/tini-static-armhf -o /sbin/tini \
  && curl -fsSL https://github.com/krallin/tini/releases/download/v0.18.0/tini-static-armhf.asc -o /sbin/tini.asc /



  docker build -t paulwroe/jenkins:v1 .
  login if necessary with docker login
  docker push paulwroe/jenkins:v1

  next, deploy with jenkins-deployment.yaml
    docker run -p 8080:8080 -p 50000:50000 <image:tag>

# plugins index!! https://plugins.jenkins.io/

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
