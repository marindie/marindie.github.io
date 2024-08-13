---
toc: true
title: " Jenkins CentOS 7 설치"
description: "Jenkins CentOS 7 설치"
categories: [Jenkins]
tags: [Jenkins]
redirect_from:
  - /2019/05/27/
---

> Jenkins CentOS 7 설치

### Jenkins 레파지토리 추가 후 설치 {#toc1}

```md
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins
```

### Jenkin 포트 변경 {#toc2}

```md
sudo vi /etc/sysconfig/jenkins
아래의 JENKINS_PORT 값을 원하는 값으로 변경
JENKINS_PORT="6000"

service jenkins start #(or systemctl for CentOS7)
service jenkins stop #(or systemctl for CentOS7)
service jenkins restart #(or systemctl for CentOS7)
service jenkins status #(or systemctl for CentOS7)
systemctl enable jenkins 
```

### 방화벽 설정 {#toc3}

```md
# CentOS 6
# 방법1.
iptables -I INPUT 1 -p tcp --dport 443 -j ACCEPT 
service iptables save
service iptables restart

# 방법2
vi /etc/sysconfig/iptables
-A INPUT -p tcp -m tcp --dport 9090 -j ACCEPT
:wq
service iptables restart

# CentOS 7
firewall-cmd –permanent –zone=public –add-port=80/tcp
firewall-cmd –reload

# CentOS 7 에서 iptables 사용
systemctl stop firewalld
systemctl mask firewalld
systemctl disable firewalld

yum -y install iptables-services
systemctl enable iptables
systemctl start iptables
iptables --flush
iptables-save > /etc/sysconfig/iptables
```

### 최초 로그인 암호 확인 {#toc4}

```md
cat /var/lib/jenkins/secrets/initialAdminPassword
# 이후에는 계정 생성하게 되고, 본인 선택에 따라 plugin 을 설치할 수도 아니면 일단 그냥 시작할 수도 있음.
```
