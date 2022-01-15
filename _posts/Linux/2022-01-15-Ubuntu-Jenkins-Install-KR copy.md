---
toc: true
title: "[Ubuntu] Jenkins 설치 방법 온라인 오프라인 둘다"
description: "Ubuntu 20.04 에서 Jenkins 설치 방법 (오프라인 방법 팁도 설명)"
categories: [Linux]
tags: [Jenkins]
redirect_from:
  - /2022/01/15/
---

> Ubuntu 20.04 에서 Jenkins 설치 방법 (오프라인 방법 팁도 설명)

### Jenkins 설치 명령어 온라인 {#toc1}

```md
== 파일 다운로드 및 서비스 시작
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins
sudo systemctl start jenkins
sudo systemctl stop jenkins
sudo systemctl status jenkins

sudo vi /etc/default/jenkins   HTTP_PORT 를 원하는 포트로 변경

== 최초 암호 저장된 위치
cat /var/lib/jenkins/secrets/initialAdminPassword

== jenkins 기본 설치 경로
/var/lib/jenkins

== maven repository 경로
== jenkins 로 job 만들어서 maven install 시키면 기본적으로 잡히는 maven repository 경로
/var/lib/jenkins/.m2/repository/

== maven settings 경로
== 내부 넥서스나 로컬 리파지토리를 세팅하기 위해..
/etc/maven/settings.xml

== ^M 없에는 명령어
== 파일을 윈도우에서 가져다오면 ^M 이 붙는 경우가 있어서 기록해둠...
vi 에서 
:e ++ff=dos
:set ff=unix
:wq

Ctrl+V Ctrl+M to insert that ^M

== 방화벽 관련 이거는 그냥 참고만.. 거진 사용할일 없음. 네트워크 담당자가 하니까..
== VirtualBox 같은 가상황에서 설치된 Ubuntu는 VirtualBox 에서 고정 IP 설정만 잡으면 호출시 포트 이슈는 없는듯 함.
sudo ufw allow 8080
sudo ufw enable
sudo ufw status

```

### Jenkins 설치 명령어 오프라인 {#toc2}

```md
오프라인으로 설치를 할 수 밖에 없는 경우 팁
1. wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key 으로 다운을 받으려고 하는
mirror 사이트 url을 온라인 컴퓨터에서 찾아들어가서 package 파일을 받아온다.

sudo dpkg -i 패키지파일명 으로 package 를 manual install 을 진행하고
위의 온라인 내용을 참고해서 서버를 기동 시킨다.

그러면 plugin 이슈가 발생을 할텐데, 가장 머리 안아픈 방식은
로컬 어딘가에 jenkins 를 설치해서 온라인으로 plugin 들을 필요한 만큼 다 다운 받고
해당 plugin 들의 파일들을 가져오는 방식이다.


```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
