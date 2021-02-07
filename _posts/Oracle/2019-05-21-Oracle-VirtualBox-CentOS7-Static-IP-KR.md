---
toc: true
title: " Oracle VirtualBox Static Ip CentOS 7"
description: "Oracle VirtualBox Static Ip CentOS 7 구성 방법"
categories: [VirtualBox]
tags: [VirtualBox]
redirect_from:
  - /2019/05/21/
---

> Oracle VirtualBox Static Ip CentOS 7 구성 방법

이렇게 세팅하고 나서 안되는 부분이 여전이 존재했습니다.
VM Static IP 로 브라우저 접속이 안되는게 아쉬웠는데요..
해당 부분을 어댑터1 에 포트포워딩 설정을 추가해서 localhost:2020 -> 192.168.56.142:8080 으로 포워딩했더니
화면이 나오긴했습니다. 하지만 여전히 맘에 안드네요...

### VirtualBox 환경설정 {#toc1}

1. 파일 -> 환경설정 -> 네트워크 -> 호스트 전용 네트워크 -> VirtualBox Host-Only Ethernet Adapter 더블클릭
2. DHCP 서버 탭 아래 '서버 사용' 체크 박스 체크 해제
3. 어댑터 탭에 IPv4 주소가 GATEWAY 192.168.56.1 이고, 255.255.255.0 는 서브넷

### CentOS 7 설치 {#toc2}

기본적인 설정으로 설치 진행하면 됩니다. minimal 버전으로 하는걸 추천.
설치된 CentOS 아이콘(추가된 Record) 클릭 후 설정 버튼 클릭하여 해당 VM 환경설정으로 진입.

어댑터1 에 'NAT' 선택
어댑터2 에 '호스트 전용 어댑터' 선택 -> 이름에 'VirtualBox Host-Only Ethernet Adapter' 선택
무작위 모드 '모두 허용' 으로 선택

### CentOS 7 네트워크 설정 {#toc2}

root 계정으로 접속

/etc/sysconfig/network-scripts/ifcfg-enp0s3 은 NAT 로 인터넷 연결을 가능하게 해준다. (어댑터1)

```md
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
```
ONBOOT=yes 인지 확인

/etc/sysconfig/network-scripts/ifcfg-enp0s8 은 Static IP 설정으로 사용한다 (어댑터2)

```md
vi /etc/sysconfig/network-scripts/ifcfg-enp0s8
```
BOOTPROTO=dhcp => BOOTPROTO=none 변경
아래 내용 추가 후 저장
IPADDR=192.168.56.142
PREFIX=24
GATEWAY=192.168.56.1

특이사항으로 MAC ADDRESS 도 불일치 할 수 있으니 VirtualBox VM 아이콘클릭하여 설정 -> 네트워크 -> 어댑터 탭에 나오는 MAC 주소가 일치하는지도 확인해
주는것이 좋습니다.

DNS 설정

```md
vi /etc/resolv.conf
nameserver 8.8.8.8
```

Network 재시작

```md
systemctl restart network
ifconfig -a # IP 부여 여부 확인
```

일단 Ping 확인
ping 192.168.56.142

Putty 등등의 IDE 로 접속 하기 전에 방화벽 및 openssl 설치 확인하셔야 합니다.

방화벽 설정
```md
firewall-cmd --list-all # 로 리스트 확인
firewall-cmd --permanent --zone=public --add-port=22/tcp # 로 22 포트 OPEN
firewall-cmd --reload # 로 재시작 
firewall-cmd --list-all # 로 리스트 확인
```

방화벽 사용안하고 싶으면 아래처럼
```md
systemctl stop firewalld.service && systemctl disable firewalld.service
```

ssh 툴로 원격 접속 시도

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io