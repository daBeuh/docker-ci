FROM centos:6.8

ENV JAVA_VERSION 1.8.0

RUN yum -y update && \
  yum -y install java-${JAVA_VERSION}-openjdk && \
  yum clean all

ENV JAVA_HOME /usr/lib/jvm/jre-$JAVA_VERSION