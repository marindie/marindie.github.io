---
toc: true
title: " CentOS 6 JAVA 설치"
description: "정규식 CentOS 6 JAVA 설치"
categories: [Linux]
tags: [JAVA]
redirect_from:
  - /2019/11/26/
---

> CentOS 6 JAVA 설치

### CentOS 6 JAVA 설치 {#toc1}

```md
# 자바 설치 여부 확인
rpm -qa | grep java
# 존재하면 나오는 java 이름으로 삭제
yum remove "java-name"

# FTP 든 SCP 든 Java 압축 파일 복사 
tar -zxvf jdk-8u131-linux-x64.tar.gz

# /usr/java 로 압축 해제된 폴더 이동
mkdir /usr/java
mv jdk1.8.0_131 /usr/java

# 권한 부여
chmod 777 /usr/java/jdk1.8.0_131/bin/*

# java, jar, javac 명령어 등록
alternatives --install /usr/bin/jar jar /usr/java/jdk1.8.0_131/bin/jar 1
alternatives --install /usr/bin/java java /usr/java/jdk1.8.0_131/bin/java 1
alternatives --install /usr/bin/javac javac /usr/java/jdk1.8.0_131/bin/javac 1

# root 계정의 /etc/profile 에 추가하여 모든 사용자에게 JAVA 사용 등록
vi /etc/profile

##########
# JAVA
##########
JAVA_HOME=/usr/java/jdk1.8.0_131
JRE_HOME=/usr/java/jdk1.8.0_131/jre
PATH=$PATH:JAVA_HOME/bin:JRE_HOME/bin
export JAVA_HOME JRE_HOME PATH
```
