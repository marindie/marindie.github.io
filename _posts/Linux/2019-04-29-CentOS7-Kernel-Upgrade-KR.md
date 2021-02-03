---
toc: true
title: " CentOS7 Kernel Update 정리"
description: "CentOS7 Kernel Update 정리"
categories: [Linux]
tags: [Kernel]
redirect_from:
  - /2019/04/29/
---

> CentOS7 Kernel Update 정리

###  실행 명령어 {#toc1}

```md
# Default
yum -y update
yum -y install yum-plugin-fastestmirror
uname -msr

# Repo Setting
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
yum repolist

# Install Kernel
yum --enablerepo=elrepo-kernel install kernel-ml
yum repolist all

# Set New Kernel
sudo awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
sudo grub2-set-default 0
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# Remove Old Kernel
yum install yum-utils
package-cleanup --oldkernels

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io