---
toc: true
title: " CentOS Nodejs install v10 설치"
description: "CentOS Nodejs install v10 설치" 
categories: [Nodejs]
tags: [Npm]
redirect_from:
  - /2019/05/09/
---

> CentOS Nodejs install v10 설치

### Yum Repository 세팅 {#toc1}

```md
curl -sL https://rpm.nodesource.com/setup_10.x | bash -
sudo yum clean all && sudo yum makecache fast
sudo yum install -y gcc-c++ make
sudo yum install -y nodejs
```

### 기존에 nodejs 를 설치한 경우 {#toc2}

Yum Repository 를 10 버전으로 잡아줘도 계속 설치가 6으로 되서 검색  
/etc/yum.repos.d/nodesource-el.repo 삭제하면 됨.

```md
rm /etc/yum.repos.d/nodesource-el.repo

curl -sL https://rpm.nodesource.com/setup_10.x | bash -
sudo yum clean all && sudo yum makecache fast
sudo yum install -y gcc-c++ make
sudo yum install -y nodejs

node -v
npm -v
```
