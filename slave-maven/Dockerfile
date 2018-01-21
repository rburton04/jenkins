FROM registry.access.redhat.com/openshift3/jenkins-slave-maven-rhel7:v3.7.9-21

MAINTAINER Ben Parees <bparees@redhat.com>

# Labels consumed by Red Hat build service
LABEL com.redhat.component="jenkins-slave-maven-rhel7-docker" \
      name="openshift3/jenkins-slave-maven-rhel7" \
      version="3.6" \
      architecture="x86_64" \
      release="4" \
      io.k8s.display-name="Jenkins Slave Maven" \
      io.k8s.description="The jenkins slave maven image has the maven tools on top of the jenkins slave base image." \
      io.openshift.tags="openshift,jenkins,slave,maven"

ENV MAVEN_VERSION=3.3 \
    BASH_ENV=/usr/local/bin/scl_enable \
    ENV=/usr/local/bin/scl_enable \
    PROMPT_COMMAND=". /usr/local/bin/scl_enable"

# Install Maven
RUN yum-config-manager --enable rhel-server-rhscl-7-rpms && \
    yum-config-manager --enable jboss-rhel-rhscl && \
    scl enable rh-maven33 && \
    yum-config-manager --disable epel >/dev/null || : && \
    INSTALL_PKGS="java-1.8.0-openjdk-devel.x86_64 java-1.8.0-openjdk-devel.i686 rh-maven33*" && \
    yum install -y $INSTALL_PKGS && \
    rpm -V ${INSTALL_PKGS//\*/} && \
    yum clean all -y && \
  #  wget http://mirror.olnevhost.net/pub/apache/maven/binaries/apache-maven-3.2.1-bin.tar.gz && \
  #  tar xvf apache-maven-3.2.1-bin.tar.gz && \
  #  export M2_HOME=/usr/local/bin/scl_enable/apache-maven-3.2.1 && \
  #  export M2=$M2_HOME/bin && \
  #  export PATH=$M2:$PATH && \
    mkdir -p $HOME/.m2 && \
    chmod -R 777 /usr/local/bin && \
    curl -SsL https://downloads.getgauge.io/stable | sh && \
    gauge install java

# When bash is started non-interactively, to run a shell script, for example it
# looks for this variable and source the content of this file. This will enable
# the SCL for all scripts without need to do 'scl enable'.
ADD contrib/bin/scl_enable /usr/local/bin/scl_enable
ADD contrib/bin/configure-slave /usr/local/bin/configure-slave
ADD ./contrib/settings.xml $HOME/.m2/

RUN chown -R 1001:0 $HOME && \
    chmod -R g+rw $HOME

USER 1001
