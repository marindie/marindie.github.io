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

### ls 파일 이름만 {#toc3}
```bash
find . -name '*.jar' -exec basename {} \;
```

### VIM 에서 String Replace 관련 {#toc4}
```bash
:%s/.*System.out.println.*//gc # c 는 변환대상마다 확인 옵션
# 그룹핑 하려면 VI 에서는 \( \) 형태로 escape 해줘야 그 안의 내용이 그룹화 된다. 일반 regex 랑 다름.
# .*? 첫번째 매칭을 시도해 봤는데 잘 안됨.. 어떻게 해야 하는지 모르겠음.
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
