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

### zip {#toc2}
```bash
yum install unzip
unzip test.zip -d ./mydir
zip test.zip ./*
zip -r test.zip ./*  

스크립트로 사용시 zip의 r 옵션은 절대경로로 잡은경우에, 압축을 풀면 해당 경로로 압축해제를 시도함
결국 해당 폴더 생성해 놓고 적절한 폴더 위치에서 unzip을 추천

```

### ls 파일 이름만 {#toc2}
```bash
find . -name '*.jar' -exec basename {} \;
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
