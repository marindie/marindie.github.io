---
toc: true
title: " Maven 설치 Install On CentOS"
description: "Maven 설치 Install On CentOS"
categories: [Maven]
tags: [Maven]
redirect_from:
  - /2019/05/27/
---

> Maven 설치 Install On CentOS

### Maven Download {#toc1}
```bash
mkdir /toos
cd /tool

# wget 으로 다운받는게 빠른데, link 는 maven download 사이트에서 tar.gz 파일 마우스 오른쪽버튼 클릭 후 "링크 복사" 하시고
# 아래처럼 붙여넣기
wget http://apache.mirror.cdnetworks.com/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz
tar -zxvf apache-maven-3.6.1-bin.tar.gz

vi /etc/profile
#############
# MAVEN
#############
MAVEN_HOME=/tool/apache-maven-3.6.1
PATH=$PATH:$MAVEN_HOME/bin
export MAVEN_HOME PATH

source /etc/profile
mvn -version
```



[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io