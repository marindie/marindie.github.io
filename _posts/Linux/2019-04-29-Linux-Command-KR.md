---
toc: true
title: "Linux: Linux 명령어 모음(CentOS 위주)"
description: "Linux 명령어 모음"
categories: [Linux]
tags: [Linux]
redirect_from:
  - /2019/04/29/
---

> CentOS 에서 사용하는 명령어를 주로 모아놨습니다.

### process kill {#toc1}

```bash

for pid in $(ps -ef | grep "concourse" | grep -v "grep" | awk '{print $2}'); do kill -9 $pid; done

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
