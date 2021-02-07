---
toc: true
title: " Rpm Header V4 RSA/SHA1 Signature, key ID ... NOKEY"
description: "Rpm Header V4 RSA/SHA1 Signature, key ID ... NOKEY"
categories: [Linux]
tags: [Rpm]
redirect_from:
  - /2019/05/27/
---

> Rpm Header V4 RSA/SHA1 Signature, key ID ... NOKEY

### RPM file install 시 경고 {#toc1}
```md

# GPG Key가 만료되어 뜨는 문제이므로 아래 명령어를 통해 해결할 수 있다고 한다.
# 경고여서 설치가 안되는건 아니였다.
rpm --import /etc/pki/rpm-gpg/RPM*

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io

