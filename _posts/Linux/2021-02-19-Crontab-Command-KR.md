---
toc: true
title: "Linux crontab 명령어"
description: "Linux crontab 명령어"
categories: [Linux]
tags: [crontab]
redirect_from:
  - /2021/02/19/
---

> crontab 명령어에 대한 간단한 정보를 메모하는 곳입니다.

### crontab 명령어 모음 {#toc1}

```md
# crontab 수정
crontab -e

# 매일 1 am 실행
0 1 * * * /home/runScript.sh

# 매일 0 am 1 min 실행
1 0 * * * /home/runScript.sh
```
