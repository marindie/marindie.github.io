---
toc: true
title: " Install Apache and Apache Tomcat on CentOS 6.5"
description: "This post will demonstrate how to install apache2.4.25 & tomcat 7.0.73 manually on CentOS 6.5"
categories: [Linux]
tags: [Apache, Tomcat]
redirect_from:
  - /2018/10/13/
---

> This post will demonstrate how to install apache on CentOS 6.5

### Assumption & Background

1. Your OS is CentOS 6.5

### Preparation before Apache installation

```md

# remove default apache
yum remove httpd

# install packages
yum update
yum -y install gcc* cpp* compat-gcc* flex* openssl* apr* libjpeg* libpng* freetype* gd-* ncurses* libtermcap* libxml* curl-devel pcre-devel

# apache download url & download httpd 2.4.25
# http://archive.apache.org/dist/httpd/
wget -c http://archive.apache.org/dist/httpd/httpd-2.4.25.tar.gz

# apr download url & download apr and apr-util
# https://apr.apache.org/download.cgi
wget -c http://mirror.apache-kr.org//apr/apr-1.6.5.tar.gz
wget -c http://mirror.apache-kr.org//apr/apr-util-1.6.1.tar.gz

# pcre download
wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.40.tar.gz

# tomcat connector download url
# http://tomcat.apache.org/download-connectors.cgi
wget -c http://mirror.apache-kr.org/tomcat/tomcat-connectors/jk/tomcat-connectors-1.2.46-src.tar.gz

# tomcat native download url
# https://tomcat.apache.org/download-native.cgi
wget -c http://apache.mirror.cdnetworks.com/tomcat/tomcat-connectors/native/1.1.34/source/tomcat-native-1.1.34-src.tar.gz

# Java 8 Download
# https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
wget -c http://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-linux-x64.tar.gz

# Tomcat Download
# https://archive.apache.org/dist/tomcat/tomcat-7/v7.0.73/bin/
wget -c https://archive.apache.org/dist/tomcat/tomcat-7/v7.0.73/bin/apache-tomcat-7.0.73.tar.gz

# Necessary file list before installation  
# apr-1.6.5.tar.gz
# apr-util-1.6.1.tar.gz
# httpd-2.4.25.tar.gz
# jdk1.8.0_191-linux-x64.tar.gz
# tomcat-connectors-1.2.46-src.tar.gz
# tomcat-native-1.1.34-src.tar.gz

# unzip files
tar -zxvf apr-1.6.5.tar.gz
tar -zxvf apr-util-1.6.1.tar.gz
tar -zxvf httpd-2.4.25.tar.gz
tar -zxvf jdk-8u191-linux-x64.tar.gz
tar -zxvf tomcat-connectors-1.2.46-src.tar.gz
tar -zxvf tomcat-native-1.1.34-src.tar.gz
tar -zxvf apache-tomcat-7.0.73.tar.gz

mkdir /was
mv apache-tomcat-7.0.73 tomcat
mv tomcat /was

```

### Install Apache

```md

# install JAVA
tar -zxvf jdk-8u191-linux-x64.tar.gz
mkdir -p /usr/java
mv jdk1.8.0_191 /usr/java
alternatives --install /usr/bin/jar jar /usr/java/jdk1.8.0_191/bin/jar 1
alternatives --install /usr/bin/java java /usr/java/jdk1.8.0_191/bin/java 1
alternatives --install /usr/bin/javac javac /usr/java/jdk1.8.0_191/bin/javac 1
chmod 777 /usr/java/jdk1.8.0_191/bin/*

vi /etc/profile
##########
# JAVA
##########
#JAVA_HOME=/usr/java/jdk1.8.0_191
#JRE_HOME=/usr/java/jdk1.8.0_191/jre
#PATH=$PATH:JAVA_HOME/bin:JRE_HOME/bin
#export JAVA_HOME JRE_HOME PATH
sed -i'' -e "/# will prevent the need for merging in future updates./a ##########\n# JAVA\n##########\nJAVA_HOME=/usr/java/jdk1.8.0_191\nJRE_HOME=/usr/java/jdk1.8.0_191/jre\nPATH=\$PATH:JAVA_HOME/bin:JRE_HOME/bin\nexport JAVA_HOME JRE_HOME PATH" /etc/profile
source /etc/profile

# install apr
cd apr-1.6.5
./configure --prefix=/usr/local/src/apr-1.6.5
make 
make install
cd ..

# install pcre
cd pcre-8.40
./configure --prefix=/usr/local/src/pcre-8.40 --with-apr=/usr/local/src/apr-1.6.5
make
make install
cd ..

# install apr-util
cd apr-util-1.6.1
./configure --prefix=/usr/local/src/apr-util-1.6.1 --with-apr=/usr/local/src/apr-1.6.5
make 
make install
cd ..

# install apache
cd httpd-2.4.25
./configure --prefix=/web/apache2.4.25 --with-apr=/usr/local/src/apr-1.6.5 --with-apr-util=/usr/local/src/apr-util-1.6.1 
make
make install
cd ..

# prefix is the location where you want to install
# In case of you want to remove make configuration, type "make distclean".
# This will discard your current configuration. Then run configure command again.

# When you run ./apachectl start command, it may show a warning message as below. Then do the following
# httpd: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1 for ServerName
# vi apache_install_path/conf/httpd.conf 
# search ServerName uncomment that line and change value to localhost or the ip address of the host machine.
# run ./apachectl start again

```

### Open firewall

```md

vi /etc/sysconfig/iptables

# Open 80 Port
iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
# Allow remote (Your_IP) access
iptables -A INPUT -s Your_IP -j ACCEPT

service iptables save
service iptables restart

```

### Apache and Tomcat Integration (mod_jk.so module)

```md

cd tomcat-connectors-1.2.44-src/native
./configure --with-apxs=/web/apache2.4.25/bin/apxs
make
make install
cd ../..

cd tomcat-native-1.1.34-src/jni/native
./configure --prefix=/was/tomcat --with-apr=/usr/local/src/apr-1.6.5 --with-java-home=$JAVA_HOME
make
make install
cd ../../..

########################
# mod_jk configuration
########################
vi /web/apache2.4.25/conf/workers.properties

# paste the following 
workers.tomcat_home="/was/tomcat/"
workers.java_home="/usr/java/jdk1.8.0.181"
ps=/
worker.list=tomcat
worker.tomcat.port=8009
worker.tomcat.host=localhost
worker.tomcat.type=ajp13

vi /web/apache2.4.25/conf/mod_jk.conf

# paste the following 
<IfModule mod_jk.c>
    JkWorkersFile "/svc/cds/web/apache2.4.25/conf/workers.properties"
    JkLogFile "/was/tomcat/logs/mod_jk.log"
    JkLogLevel debug
    JkAutoAlias "/was/tomcat/webapps"
    JkMount /*.jsp tomcat
    JkMount /*.json tomcat
    JkMount /*.xml tomcat
    JkMount /*.do tomcat
    JkLogStampFormat "[%y-%m-%d %H:%M:%S]"
    #JkOptions +ForwardKeySize +ForwardURICompat -ForwardDirectories
    JKRequestLogFormat "%w %V %T"
</IfModule>

vi /web/apache2.4.25/conf/httpd.conf
# go to the end and paste the following commands
LoadModule jk_module modules/mod_jk.so
include conf/mod_jk.conf

```

### Start Apache Web Server and Tomcat Server

```md

/web/apache2.4.25/bin/apachectl start
/was/tomcat/bin/startup.sh

ps -ef | egrep "httpd|tomcat" | grep -v "grep"
netstat -lnpt

```

### Visit localhost and localhost/index.jsp

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
