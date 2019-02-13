---
title: "CentOS6 Iptables Usage"
description: "In this page, I will show you some basic examples about how to use iptables to control IP and Port (Allow/Deny)"
categories: [Linux]
tags: [iptables]
redirect_from:
  - /2018/09/09/
---

> In this page, I will show you some basic examples about how to use iptables to control IP and Port (Allow/Deny)

* Kramdown table of contents
{:toc .toc}

# Assumption & Background

1. Your OS is CentOS 6.5
2. From CentOS7, iptables is not configured. So You have to manully configure it by yourself, if it is easier for you.
3. Please read from the top if you do not understand the syntax. Sometimes it's best to implement it without thinking and see if it works for you.

# Iptables Basic Command

```bash

# You May find it useful to use the following commands.
# The meaning of command is fairly strait-forward. So I will just continue
service iptables status
service iptables save
service iptables restart

```

# Allow Specific PORT Number using iptables

```bash

# The path for iptables configuration
vi /etc/sysconfig/iptables

# Using File
-A INPUT -p tcp –dport 80 -j ACCEPT
# Save file and restart iptables
service iptables restart

# Using Bash. (Shell command)
iptables -A INPUT -p tcp –dport 443 -j ACCEPT
service iptables save
service iptables restart

# Note. If you change the configuration file and more changes are made using iptables command,
# then chages made in the file will be lost when you run service iptables save

```

# Allow Specific IP Address using iptables

```bash

# Using File
-A INPUT -s 192.168.1.8 -j ACCEPT

# Using Bash. (Shell command)
iptables -A INPUT -s 192.168.1.8 -j ACCEPT

```

# Deny Specific IP Address and PORT Number using iptables

```bash

# Using File
-A INPUT -s 192.168.1.8 -p tcp --dport 443 -j DROP

# Using Bash. (Shell command)
iptables -A INPUT -s 192.168.1.8 -p tcp --dport 443 -j DROP

```

# Allow Range of IP Address using iptables

```bash

# Using File
iptables -A INPUT -s 192.168.0.1/24 -j ACCEPT

```

# Allow Range of PORT using iptables

```bash

# Using File
# Allow Port Number 80,22,53
iptables -A INPUT -p tcp --match multiport --dports 80,22,53 -j ACCEPT

# Allow Port Number from 1024 to 3000
iptables -A INPUT -p tcp --match multiport --dports 1024:3000 -j ACCEPT

```

# Iptables filter table Description

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

# Some Other Useful Commands

```bash

# Check if iptables are installed
rpm -qa | grep iptables

# List iptables
iptables -nL

# Remove all the rules in iptables
iptables -F

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io