FROM registry.access.redhat.com/openshift3/jenkins-slave-base-rhel7

MAINTAINER Ben Parees <bparees@redhat.com>

# Labels consumed by Red Hat build service
LABEL com.redhat.component="jenkins-slave-base-rhel7-docker" \
      name="openshift3/jenkins-slave-base-rhel7" \
      version="v3.6.173.0.96" \
      architecture="x86_64" \
      release="10" \
      io.k8s.display-name="Jenkins Slave Base" \
      io.k8s.description="The jenkins slave maven image has the maven tools on top of the jenkins slave base image." \
      io.openshift.tags="openshift,jenkins,slave,maven"

ENV MAVEN_VERSION=3.3 \
    BASH_ENV=/usr/local/bin/scl_enable \
    ENV=/usr/local/bin/scl_enable \
    PROMPT_COMMAND=". /usr/local/bin/scl_enable"

COPY contrib/bin/scl_enable /usr/local/bin/scl_enable

# Install Maven

RUN yum-config-manager --enable rhel-7-server-rhscl-rpms && \
    yum-config-manager --enable rhel-7-server-optional-rpms && \
    yum-config-manager --enable rhel-7-server-extras-rpms && \
    yum-config-manager --enable rhel-7-server-rpms && \
    yum-config-manager --enable rhel-7-server-ose-3.2-rpms && \
    
    yum-config-manager --disable epel >/dev/null || : && \
    INSTALL_PKGS="java-1.8.0-openjdk-devel.x86_64 java-1.8.0-openjdk-devel.i686 rh-maven33*" && \
    yum install -y $INSTALL_PKGS && \
    rpm -V ${INSTALL_PKGS//\*/} && \
    yum clean all -y && \
    mkdir -p $HOME/.m2 && \
    chmod -R 777 /usr/local/bin && \
    curl -SsL https://downloads.getgauge.io/stable | sh && \
    gauge install java
   # wget https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.3.9/apache-maven-3.3.9-bin.tar.gz
   # tar xvf apache-maven-3.3.9-bin.tar.gz

# When bash is started non-interactively, to run a shell script, for example it
# looks for this variable and source the content of this file. This will enable
# the SCL for all scripts without need to do 'scl enable'.
ADD contrib/bin/scl_enable /usr/local/bin/scl_enable
ADD contrib/bin/configure-slave /usr/local/bin/configure-slave
ADD ./contrib/settings.xml $HOME/.m2/

RUN chown -R 1001:0 $HOME && \
    chmod -R g+rw $HOME

USER 1001
