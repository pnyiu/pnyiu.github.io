---
title: Docker on RHEL 7.4 (Apache HTTPD and Tomcat)
date: 2017-11-17 13:00:34
tags: 
- Apache
- Tomcat
- Red Hat
- Java 9
- Docker
---
## Environment
1. Red Hat Enterprise Linux 7.4 VM on Oracle VM Server
2. Docker 1.12.6  (from Red Hat yum repository)
3. Oracle JDK SE 9 (from Oracle)
4. Apache HTTPD 2.4.6 (from Red Hat yum repository)
5. Tomcat 8.5.23 (from Apache)

{% img "https://docs.google.com/drawings/d/e/2PACX-1vThkThZmj6osmG63EaDdR5xFk9EIr7ANB_3guIcti8WuqS5P-_aGvJIdVbChIQkoiZYmU15evgAe60q/pub?w=910&amp;h=606" %}

<!-- more -->

### Install Docker on Red Hat Enterprise Linux 7
1. Join Red Hat Enterprise Linux developer subscription
2. Then, refer to the [GETTING STARTED WITH CONTAINERS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html-single/getting_started_with_containers/) to install Docker and corresponding configurations.

### Dockerfile -Apache HTTPD
1. mkdir -p /root/httpd-project
2. cd /root/httpd-project
3. vi Dockerfile
{% codeblock "Dockerfile for Apache HTTPD" line_number:false lang:bash https://docs.docker.com/engine/reference/builder/ Dockerfile %}

# My cool Docker image
# Version 1

# If you loaded redhat-rhel-server-7.0-x86_64 to your local registry, uncomment this FROM line instead:
# FROM registry.access.redhat.com/rhel
# Pull the rhel image from the local registry
FROM registry.access.redhat.com/rhel

MAINTAINER Calvin Yiu
USER root

# Update image
RUN yum repolist --disablerepo=* && \
    yum-config-manager --disable \* > /dev/null && \
    yum-config-manager --enable rhel-7-server-rpms > /dev/null
RUN yum update -y
# Add httpd package. procps and iproute are only added to investigate the image later.
RUN yum install httpd procps iproute -y
RUN echo hostname.abc.com > /etc/hostname
#RUN mkdir /run/httpd
COPY httpd-proxy.conf /etc/httpd/conf.modules.d/

# Create an index.html file
#RUN bash -c 'echo "Your Web server test is successful." >> /var/www/html/index.html'

EXPOSE 80

# Start the service
CMD /usr/sbin/httpd -D FOREGROUND
{% endcodeblock %} 
4. vi httpd-proxy.conf
{% codeblock "httpd-proxy.conf" line_number:false lang:bash  %}
<Proxy "balancer://cluster">
    BalancerMember "ajp:/tomcat:8009" loadfactor=1
    BalancerMember "ajp://tomcat2:8009" loadfactor=2
    ProxySet lbmethod=bytraffic
</Proxy>

ProxyPassMatch ^/(.*\.jsp)$ balancer://cluster/$1
{% endcodeblock %} 

### Dockerfile - Tomcat
1. mkdir -p /root/tomcat-project
2. Download binary files from [Apache](https://tomcat.apache.org/download-80.cgi)
3. Download Java 8 rpm file from [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
4. Copy above files into /root/tomcat-project
5. vi Dockerfile
{% codeblock "Dockerfile for Tomcat" line_number:false lang:bash https://docs.docker.com/engine/reference/builder/ Dockerfile %}
# My cool Docker image
# Version 1

# If you loaded redhat-rhel-server-7.0-x86_64 to your local registry, uncomment this FROM line instead:
# FROM registry.access.redhat.com/rhel
# Pull the rhel image from the local registry
#FROM registry.access.redhat.com/rhel
FROM rhel

MAINTAINER Calvin Yiu

# The USER instruction sets the user name (or UID) and optionally the user group (or GID) to use when running the image and for any RUN, CMD and ENTRYPOINT instructions that follow it in the Dockerfile.
USER root

ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH

RUN mkdir -p "$CATALINA_HOME"

# Update image
COPY jdk-9.0.1_linux-x64_bin.rpm .
COPY apache-tomcat-8.5.23.tar.gz .

RUN rpm -ivh jdk-9.0.1_linux-x64_bin.rpm
RUN tar xvf apache-tomcat-8.5.23.tar.gz -C ${CATALINA_HOME} --strip-components=1

EXPOSE 8009

WORKDIR ${CATALINA_HOME}

CMD ["catalina.sh", "run"]

{% endcodeblock %} 

## Build Apache HTTPD and Tomcat container images
1. cd /root/httpd-project
2. docker build -t rhel_httpd .
3. cd /root/tomcat-project
4. docker build -t rhel_tomcat .
5. docker images
[root@workflow tomcat-project]# docker images
{% codeblock line_number:false lang:bash  %}
REPOSITORY                                 TAG                 IMAGE ID            CREATED             SIZE
rhel_httpd                                 latest              101b0144da85        5 hours ago         523 MB
rhel_tomcat                                latest              8d73b26c2609        29 hours ago        1.135 GB
registry.access.redhat.com/rhel7/rhel      latest              db7a70a0414e        5 weeks ago         195.9 MB
registry.access.redhat.com/rhel            latest              db7a70a0414e        5 weeks ago         195.9 MB
{% endcodeblock %} 

## Create local network for the containers
It is recommended to use user-defined bridge networks to control which containers can communicate with each other, and also to enable automatic DNS resolution of container names to IP addresses. [User-defined networks](https://docs.docker.com/engine/userguide/networking/)
1. ``docker network create --driver bridge my_bridge_network``
2. docker network list
{% codeblock line_number:false lang:bash  %}
NETWORK ID          NAME                 DRIVER              SCOPE
f1f1b26c48dd        bridge               bridge              local
4f7f8e164833        host                 host                local
8aad24fc4399        my_bridge_network    bridge              local
f8361e507611        none                 null                local
{% endcodeblock %} 

## Start the Tomcat and HTTP containers
1. ``bash docker run -i -d -t -v /data/uat/webapps:/usr/local/tomcat/webapps --network=my_bridge_network -p 8080:8080 --name=tomcat --add-host=m-docker.abc.com:10.1.10.182 \--add-host=webdb12cuat:10.1.96.142  rhel_tomcat``
	
	{% codeblock line_number:false lang:bash  %}
	Definition of the options:	   
   --tty , -t		Allocate a pseudo-TTY
   --detach , -d		Run container in background and print container ID
   --interactive , -i		Keep STDIN open even if not attached
   --volume , -v		Bind mount a volume
   --add-host		Add a custom host-to-IP mapping (host:ip) (add to /etc/hosts)
   --network		Connect a container to a network
   --publish , -p		Publish a container’s port(s) to the host
   --name		Assign a name to the container
   {% endcodeblock %} 
2. ``docker run -i -d -t -v /data/uat/webapps:/usr/local/tomcat/webapps --network=my_bridge_network -p 8081:8080 --name=tomcat2 --add-host=m-docker.abc.com:10.1.10.182 --add-host=webdb12cuat:10.1.96.142  rhel_tomcat``
3. Check the running containers now:
{% codeblock line_number:false lang:bash  %}
[root@workflow tomcat-project]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                              NAMES
9a7fc047032c        rhel_tomcat         "catalina.sh run"        5 hours ago         Up 5 hours          8009/tcp, 0.0.0.0:8081->8080/tcp   tomcat2
188eefc95caa        rhel_httpd          "/bin/sh -c '/usr/sbi"   5 hours ago         Up 5 hours          0.0.0.0:80->80/tcp                 httpd
ef8553297fcb        rhel_tomcat         "catalina.sh run"        6 hours ago         Up 6 hours          8009/tcp, 0.0.0.0:8080->8080/tcp   tomcat
{% endcodeblock %} 