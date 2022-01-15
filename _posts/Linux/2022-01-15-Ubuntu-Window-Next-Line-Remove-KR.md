---
toc: true
title: "[Ubuntu] Ubuntu 20.04 에서 파일 끝에 ^M 삭제 방법
description: "Ubuntu Ubuntu 20.04 에서 파일 끝에 ^M 삭제 방법"
categories: [Ubuntu]
tags: [File]
redirect_from:
  - /2022/01/15/
---

> Ubuntu 20.04 에서 파일 끝에 ^M 삭제 방법

### Linux Ubuntu 로 파일 전송 후 vi로 열면 끝에 ^M 이 붙어 있는 경우 삭제 방법 {#toc1}

```md
== ^M 없에는 명령어
== 파일을 윈도우에서 가져다오면 ^M 이 붙는 경우가 있어서 기록해둠...
vi 에서 
:e ++ff=dos
:set ff=unix
:wq

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
