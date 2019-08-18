# The MIT License
#
#  Copyright (c) 2015-2019, CloudBees, Inc. and other Jenkins contributors
#
#  Permission is hereby granted, free of charge, to any person obtaining a copy
#  of this software and associated documentation files (the "Software"), to deal
#  in the Software without restriction, including without limitation the rights
#  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
#  copies of the Software, and to permit persons to whom the Software is
#  furnished to do so, subject to the following conditions:
#
#  The above copyright notice and this permission notice shall be included in
#  all copies or substantial portions of the Software.
#
#  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
#  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
#  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
#  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
#  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
#  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
#  THE SOFTWARE.

FROM ubuntu:18.04

#
# skip interactive prompts
#
ENV DEBIAN_FRONTEND=noninteractive

#
# update package lists
#
RUN apt-get update

#
# using local timezone for convenience
#
RUN apt-get install -y tzdata
ENV TZ=America/Los_Angeles
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata

#
# install dependencies
#
RUN apt-get install -y openjdk-8-jdk
RUN apt-get install -y git
RUN apt-get install -y wget
RUN apt-get install -y curl
RUN apt-get update
RUN apt-get install -y python3-pip
RUN apt-get update
RUN pip3 install awscli

#
# Jenkins Slave
#
# References:
#     https://hub.docker.com/r/jenkinsci/slave/dockerfile
#     https://hub.docker.com/r/jenkinsci/jnlp-slave/dockerfile
#
ARG JENKINS_VERSION=3.29

RUN groupadd -g 1000 jenkins
RUN useradd -d /home/jenkins -u 1000 -g 1000 -m jenkins

RUN curl --create-dirs -fsSLo /usr/share/jenkins/slave.jar https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${JENKINS_VERSION}/remoting-${JENKINS_VERSION}.jar \
    && chmod 755 /usr/share/jenkins \
    && chmod 644 /usr/share/jenkins/slave.jar

ADD jenkins-slave/default/jenkins-slave /usr/local/bin/jenkins-slave
RUN chmod +x /usr/local/bin/jenkins-slave

RUN mkdir /home/jenkins/.jenkins && mkdir -p /home/jenkins/agent
RUN chown -R jenkins:jenkins /home/jenkins

USER jenkins
WORKDIR /home/jenkins

ENTRYPOINT ["jenkins-slave"]