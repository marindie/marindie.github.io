---
toc: true
title: " Eclipse 관련 모음"
description: "Eclipse 관련 모음"
categories: [Eclipse]
tags: [Eclipse]
redirect_from:
  - /2019/05/31/
---

> Eclipse 관련 모음

### Eclipse JVM 경로 설정 -vm 옵션 in eclipse.ini file {#toc1}

```md
반드시 실행파일명을 포함한 전체경로를 적어야 한다.
-product, --launcher.* 가 있는 라인 이후, -vmargs 라인의 이전에 기입

--launcher.defaultAction
openFile
-vm
D:\JAVA\bin\javaw.exe
-vmargs
-Dosgi.requiredJavaVersion=1.7

```
