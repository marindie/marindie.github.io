---
toc: true
title: " CentOS6 Iptables 사용법"
description: "기본적인 iptables 명령어에 대하여 다루어 보겠습니다. IP and Port (Allow/Deny)"
categories: [Linux]
tags: [Iptables]
redirect_from:
  - /2019/02/15/
---

> 기본적인 iptables 명령어에 대하여 다루어 보겠습니다. IP and Port (Allow/Deny)

### 배경지식 {#toc1}

1. CentOS 6.x 에서 사용 가능한 내용입니다.
2. CentOS 7.x 부터는 iptables is not configured. 하지만 수동으로 iptable 을 사용하게끔 설정은 가능합니다.

### Iptables Basic Command {#toc2}

```md

# 자주 사용하게 될 명령어들 입니다.
# 상태 확인, 시작, 재시작
service iptables status
service iptables save
service iptables restart

```

### 특정 PORT 허용 {#toc3}

```md

# iptables configuration 파일 경로
vi /etc/sysconfig/iptables

# 80 포트 허용
-A INPUT -p tcp –dport 80 -j ACCEPT

# 파일 저장 후 아래 명령어 실행으로 반영
service iptables restart

# 파일 작업 없이 명령어 만으로 추가 하기. (Shell command)
# 주의사항, 파일에서 작업 후, 명령어로 추가하고 나서 save 명령어를 날리면, 파일로 작업한 내용은 사라집니다.
iptables -A INPUT -p tcp –dport 443 -j ACCEPT
service iptables save
service iptables restart

```

### 특정 IP 주소 허용 {#toc4}

```md

# 파일 편집시..
-A INPUT -s 192.168.1.8 -j ACCEPT

# 명령어 사용시.. (Shell command)
iptables -A INPUT -s 192.168.1.8 -j ACCEPT

```

### 특정 IP 와 PORT 차단 {#toc5}

```md

# 파일 편집시..
-A INPUT -s 192.168.1.8 -p tcp --dport 443 -j DROP

# 명령어 사용시.. (Shell command)
iptables -A INPUT -s 192.168.1.8 -p tcp --dport 443 -j DROP

```

### 특정 IP 주소 대역을 허용 {#toc6}

```md

# 파일 편집시.. (192.168.0.1 ~ 192.168.0.255 까지)
-A INPUT -s 192.168.0.1/24 -j ACCEPT

# 명령어 사용시.. (Shell command) (192.168.0.1 ~ 192.168.0.255 까지)
iptables -A INPUT -s 192.168.0.1/24 -j ACCEPT

```

### 특정 PORT 대역 허용 {#toc7}

```md

# 파일 편집시.. Port Number 80,22,53 허용
-A INPUT -p tcp --match multiport --dports 80,22,53 -j ACCEPT

# 명령어 사용시.. (Shell command) Port Number 80,22,53 허용
iptables -A INPUT -p tcp --match multiport --dports 80,22,53 -j ACCEPT

# 파일 편집시.. Port 1024 부터 3000 까지 허용
-A INPUT -p tcp --match multiport --dports 1024:3000 -j ACCEPT

# 명령어 사용시.. (Shell command) Port 1024 부터 3000 까지 허용
iptables -A INPUT -p tcp --match multiport --dports 1024:3000 -j ACCEPT

```

### Iptables filter table 도움말 정보 {#toc8}

Chain - There are three different types of chains in iptables
- INPUT : All the packets to host machine
- OUTPUT : All the packets caused by host machine
- FORWARD : All the packets where host machine is not their destination

Match - Conditions that need to be satisfied to handle the given packets
- -s : source ip address
- -d : destination ip address
- -p : protocol Ex) tcp, udp
- -j : jump how those packets which satisfied the rule will be handled. Look At Target for possible options

Target - Actions where packets are meet certain Match conditions
- ACCEPT : Allow Packets
- DROP : Drop Packets (As if the packet has not been delivered)
- REJECT : Drop Packets and send response to source ip address
- LOG : Make log into syslog

### Some Other Useful Commands {#toc9}

```md

# iptables 설치 확인 명령어
rpm -qa | grep iptables

# iptables 정보 display
iptables -nL

# iptables 에 있는 모든 rule 삭제
iptables -F

```

도움이 되었으면 좋겠네요. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io