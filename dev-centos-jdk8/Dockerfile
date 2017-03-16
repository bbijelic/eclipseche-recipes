FROM centos:latest

# Exposing files
# 4403 - Eclipse Che - Agent
# 8000 - Debug Port
# 8443 - API
# 9443 - Admin
# 22   - SSH access
EXPOSE 4403 8000 8443 9443 22

MAINTAINER Bojan Bijelic <bojan.bijelic.os@gmail.com> - Developer

RUN yum -y install epel-release && \
    yum -y install sudo openssh-server procps wget unzip mc git vim-enhanced gitflow curl nmap yum-plugin-ovl && \
    mkdir /var/run/sshd && \
    sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd && \
    echo "%wheel ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    useradd -u 1000 -G users,wheel -d /home/user --shell /bin/bash -m user && \
    echo -e "user\nuser" | passwd user && \
    sed -i 's/requiretty/!requiretty/g' /etc/sudoers
	
USER user

# Labeling ports (Observable in Eclipse Che)
LABEL che:server:8443:ref=api che:server:8443:protocol=https che:server:8000:ref=debug che:server:8000:protocol=http che:server:9443:ref=admin che:server:9443:protocol=https

ENV MAVEN_VERSION 3.3.9
ENV JAVA_VERSION 8u121
ENV JAVA_VERSION_PREFIX 1.8.0_121
ENV M2_HOME /opt/apache-maven-$MAVEN_VERSION
ENV JAVA_HOME /opt/jdk$JAVA_VERSION_PREFIX
ENV PATH $M2_HOME/bin:$JAVA_HOME/bin:$PATH

RUN sudo mkdir /opt/apache-maven-$MAVEN_VERSION && \
    wget -qO- "http://ftp.carnet.hr/misc/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz" | sudo tar -zx --strip-components=1 -C /opt/apache-maven-$MAVEN_VERSION/ && \
    wget --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" -qO- "http://download.oracle.com/otn-pub/java/jdk/8u121-b13/e9e7ea248e2c4826b92b3f075a80e441/jdk-8u121-linux-x64.tar.gz" | sudo tar -zx -C /opt/

ENV TERM xterm
	
USER root
RUN  echo -e "M2_HOME=/opt/apache-maven-$MAVEN_VERSION\nJAVA_HOME=/opt/jdk$JAVA_VERSION_PREFIX\nPATH=$M2_HOME/bin:$JAVA_HOME/bin:$PATH" >> /etc/environment

USER user
ENV LANG croatian
CMD sudo /usr/bin/ssh-keygen -A && \
    sudo /usr/sbin/sshd -D && \
    tail -f /dev/null