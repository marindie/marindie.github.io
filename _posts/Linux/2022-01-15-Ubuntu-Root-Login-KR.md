---
toc: true
title: "[Ubuntu] Ubuntu 20.04 에서 Root 로그인"
description: "Ubuntu Ubuntu 20.04 에서 Root 로그인"
categories: [Linux]
tags: [Ubuntu]
redirect_from:
  - /2022/01/15/
---

> Ubuntu 20.04 에서 Root 로그인

### Ubuntu 20.04 에서 Rroot 로그인 {#toc1}

```md
== 단순히 root 계정만 사용하고 싶으면
sudo su 로 root 로 진입
passwd 로 root 의 암호를 설정

== GUI 에서 root 로 로그인 하고 싶을때
vi /etc/gdm3/custom.conf 파일을 열여서 # TimedLoginDelay = 10 아랫줄에 AllowRoot=true라고 라인을 추가해 준다.

vi /etc/pam.d/gdm-password
line 3 (주석포함) 의 내용 주석 처리
Before
auth required pam_succeed_if.so_user != root quite_access

After
#auth required pam_succeed_if.so_user != root quite_access

재부팅 후 다른 사용자 선택해서 root 로 로그인 하면 됨
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
