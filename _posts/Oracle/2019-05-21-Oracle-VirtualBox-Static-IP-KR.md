---
toc: true
title: " Oracle VirtualBox Static Ip CentOS 6"
description: "Oracle VirtualBox Static Ip CentOS 6 구성 방법"
categories: [VirtualBox]
tags: [VirtualBox]
redirect_from:
  - /2019/05/21/
---

> Oracle VirtualBox Static Ip CentOS 6 구성 방법

이렇게 세팅하고 나서 안되는 부분이 여전이 존재했습니다.
VM Static IP 로 브라우저 접속이 안되는게 아쉬웠는데요..
해당 부분을 어댑터1 에 포트포워딩 설정을 추가해서 localhost:2020 -> 192.168.56.142:8080 으로 포워딩했더니
화면이 나오긴했습니다. 하지만 여전히 맘에 안드네요...

### VirtualBox 환경설정 {#toc1}

1. 파일 -> 환경설정 -> 네트워크 -> 호스트 전용 네트워크 -> VirtualBox Host-Only Ethernet Adapter 더블클릭
2. DHCP 서버 탭 아래 '서버 사용' 체크 박스 체크 해제
3. 어댑터 탭에 IPv4 주소가 GATEWAY 192.168.56.1 이고, 255.255.255.0 는 서브넷

### CentOS 6 설치 {#toc2}

기본적인 설정으로 설치 진행하면 됩니다. minimal 버전으로 하는걸 추천.
설치된 CentOS 아이콘(추가된 Record) 클릭 후 설정 버튼 클릭하여 해당 VM 환경설정으로 진입.

어댑터1 에 'NAT' 선택
어댑터2 에 '호스트 전용 어댑터' 선택 -> 이름에 'VirtualBox Host-Only Ethernet Adapter' 선택
무작위 모드 '모두 허용' 으로 선택

### CentOS 6 네트워크 설정 {#toc2}

root 계정으로 접속
/etc/sysconfig/network-scripts/ifcfg-eth0 은 NAT 로 인터넷 연결을 가능하게 해준다. (어댑터1)

/etc/sysconfig/network-scripts/ifcfg-eth1 은 Static IP 설정으로 사용한다 (어댑터2)

```md
vi /etc/sysconfig/network-scripts/ifcfg-eth1
```

BOOTPROTO=dhcp => BOOTPROTO=none 변경
아래 내용 추가 후 저장
IPADDR=192.168.56.141
PREFIX=24
GATEWAY=192.168.56.1

특이사항으로 MAC ADDRESS 도 불일치 할 수 있으니 VirtualBox VM 아이콘클릭하여 설정 -> 네트워크 -> 어댑터 탭에 나오는 MAC 주소가 일치하는지도 확인해
주는것이 좋습니다.

Network 재시작

```md
service network restart
ifconfig -a # IP 부여 여부 확인
```

일단 Ping 확인
ping 192.168.56.141

Putty 등등의 IDE 로 접속 하기 전에 방화벽 및 openssl 설치 확인하셔야 합니다.

방화벽 파일을 수정하는 방법

```md
service iptables status # 방화벽 정보 확인

vi /etc/sysconfig/iptables
-A INPUT -p tcp –dport 22 -j ACCEPT # 22 포트 OPEN 추가
# 파일 저장 후 재시작
service iptables restart
```

명령어로 하는 방법

```md
service iptables status # 방화벽 정보 확인

iptables -I INPUT 1 -p tcp --dport 22 -j ACCEPT 
service iptables save 
service iptables restart
```

ssh 툴로 원격 접속 시도


