---
toc: true
title: "VirtualBox: Oracle VirtualBox Static Ip CentOS 7"
description: "Oracle VirtualBox Static Ip CentOS 7 구성 방법"
categories: [VirtualBox]
tags: [VirtualBox]
redirect_from:
  - /2019/05/21/
---

> Oracle VirtualBox Static Ip CentOS 7 구성 방법

### VirtualBox 환경설정 {#toc1}

1. 파일 -> 환경설정 -> 네트워크 -> 호스트 전용 네트워크 -> VirtualBox Host-Only Ethernet Adapter 더블클릭
2. DHCP 서버 탭 아래 '서버 사용' 체크 박스 체크 상태 확인
3. 최저 주소 한계 192.168.56.101, 최고 주소 한계 192.168.56.254 IP 영역 확인

### CentOS 7 설치 {#toc2}

기본적인 설정으로 설치 진행하면 됩니다. minimal 버전으로 하는걸 추천.
설치된 CentOS 아이콘(추가된 Record) 클릭 후 설정 버튼 클릭하여 해당 VM 환경설정으로 진입.
네트워크 -> 어댑터 탭 클릭 -> 다음에 연결됨에 '호스트 전용 어댑터' 선택 -> 이름에 'VirtualBox Host-Only Ethernet Adapter' 선택
나머지는 그대로 두고 확인

### CentOS 7 네트워크 설정 {#toc2}

root 계정으로 접속
발생 가능한 변수로는, 신규 설치가 아닌, 기존에 설치한 CentOS 6 에서 NAT 를 사용하시다가,
Static으로 바꾸는 경우에는 기존거를 남겨두려고 어댑터 1이 아닌 어댑터 2탭을 사용하는 경우가 있을 수 있습니다.
이렇게 하셔도 되는데 단지, eth1 을 찾는데 설정파일이 만들어 져있지 않는 경우가 발생하는 것 뿐이죠.

가장 확실하게 하기 위해서 ifconfig -a 로 현재 사용이 가능한 네크워크 어댑터 이름 Ex. eth0 or eth1 등등을 확인하고
해당파일이 없으면 만들고 (cp /etc/sysconfig/network-scripts/ifcfg-enp0s3 /etc/sysconfig/network-scripts/ifcfg-enp0s8)
해당 파일을 작업하면 됩니다. 이 예제에서는 ifcfg-enp0s8 이라고 가정.

```bash
vi /etc/sysconfig/network-scripts/ifcfg-enp0s8
```
BOOTPROTO=dhcp => BOOTPROTO=none 변경
아래 내용 추가 후 저장
IPADDR=192.168.56.142
PREFIX=24

특이사항으로 MAC ADDRESS 도 불일치 할 수 있으니 VirtualBox VM 아이콘클릭하여 설정 -> 네트워크 -> 어댑터 탭에 나오는 MAC 주소가 일치하는지도 확인해
주는것이 좋습니다.

Network 재시작

```bash
systemctl restart network
ifconfig -a # IP 부여 여부 확인
```

일단 Ping 확인
ping 192.168.56.142

Putty 등등의 IDE 로 접속 하기 전에 방화벽 및 openssl 설치 확인하셔야 합니다.

방화벽 설정
```bash
firewall-cmd --list-all # 로 리스트 확인
firewall-cmd --permanent --zone=public --add-port=22/tcp # 로 22 포트 OPEN
firewall-cmd --reload # 로 재시작 
firewall-cmd --list-all # 로 리스트 확인
```

방화벽 사용안하고 싶으면 아래처럼
```bash
systemctl stop firewalld.service && systemctl disable firewalld.service
```

ssh 툴로 원격 접속 시도

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io