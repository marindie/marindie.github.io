---
toc: true
title: " Weblogic 12c 설치 (CentOS6) 가이드 GUI 모드"
description: "Weblogic 12c 설치 (CentOS6) 가이드 GUI 모드"
categories: [Server]
tags: [Weblogic]
redirect_from:
  - /2019/11/16/
---

> Weblogic 12c 설치 (CentOS6) 가이드 GUI 모드

### 배경 및 전제 조건 {#toc0}
```md
# 일단, Weblogic 12c 는 Java 1.8 에서 동작하니, 
# Java 1.8 설치.
# 방법은 여러가지가 있으니 검색해서 설치.

# CentOS6 Gnome 설치
yum update -y
yum groupinstall -y "X Window System" "Desktop"
yum install -y gnome-core xfce4 xorg-x11-fonts
yum install nano
nano /etc/inittab
id:3:initdefault:
# Change the 3 to 5. The line should be the same as the line below:
id:5:initdefault:

# CentOS7 Gnome 설치
yum update -y
yum groupinstall -y "GNOME Desktop" "Graphical Administration Tools"
ln -sf /lib/systemd/system/runlevel5.target /etc/systemd/system/default.target
```

### 계정, 환경설정, 설치 시작 {#toc1}
```md
# 계정
groupadd -g 1001 oinstall
useradd -u 1001 -g -install oracle
passwd oracle #(암호입력)

# 폴더 및 환경변수
mkdir -p /was/weblogic/12c/oracle/product
mkdir -p /was/weblogic/12c/oracle/config
chown -R oinstall:oracle /was/weblogic
chmod -R 755 /was/weblogic

vi ~/.bash_profile
export ORACLE_BASE=/was/weblogic/12c/oracle
export ORACLE_HOME=$ORACLE_BASE/product
export MW_HOME=$ORACLE_HOME
export WLS_HOME=$MW_HOME/wlserver
export DOMAIN_BASE=$ORACLE_HOME/config/domains
export DOMAIN_HOME=$DOMAIN_BASE/test

# fmw~.jar 파일 있는 곳에서 아래 명령어 실행
# GUI 로 실행 해야 하기 때문에 ssh 접속이 아닌 서버에서 직점 실행
# 원격으로 붙을때 GUI 로 붙는거 까지 설정된 거라면 원격 접속으로도 가능
java -jar fmw~.jar 

```

### can't connect to x11 window server using ':0.0' as the value of display variable {#toc2}
```md
java -jar fmr~.jar 를 실행해서 설치하려고 하니, 위의 에러가 발생했는데,
해당 에러는 나의 경우에는 oracle 이라는 계정으로 설치를 해서 발생하는 것으로 보인다.

xhost 명령어로 local 에 모든 사용자 권한을 추가해주니, GUI 가 잘 실행 되었다.
물론 CentOS 에서 부팅시에 이미 gnome은 잘 동작되고 있는 상황이였다.
xhost +local:all

그외에 
echo $DISPLAY 값이 ":0.0" 이 아니면 
export DISPLAY=":0.0" 으로 주고 시도해보는것도 방법이고,

xhost +inet:192.168.3.1 처럼 IP 정보를 세팅 해주고 하는것도 방법이다.
```

### Weblogic VM 에서 기동시 시간이 너무 오래 걸림 {#toc3}
```md
설치 완료 후 서버를 기동시키자, 너무 오랜 시간 STARTING 상태에서 머물러 있어서
결국 검색.
vi /usr/java/jdk1.8/jre/lib/security/java.security

아래의 내용으로 수정.
securerandom.source=file:/tmp/big.random.file

해당 oracle 세션 끊고 다시 su - oracle
재기동 하니까 그나마 좀 빨리 기동됨
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io

