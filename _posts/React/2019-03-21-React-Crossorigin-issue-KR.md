---
toc: true
title: "React: 서버 API 호출시 발생하는 Crossorigin 해결 방법 정리"
description: "React 로 Ajax(axios or fetch 등등) 호출 시 chrome browser 에서 발생 가능한 Crossorigin 문제 해결 방법 정리 내용입니다."
categories: [React]
tags: [Crossorigin]
redirect_from:
  - /2019/03/21/
---

> React 로 Ajax(axios or fetch 등등) 호출 시 chrome browser 에서 발생 가능한 Crossorigin 문제 해결 방법 정리 내용입니다.

# Crossorigin Issue

서로 다른 Domain 사이의 보안 이슈를 막기위해 발생하는 에러로 이해하였습니다.

# 해결방법들

```md

1. package.json 에 아래 내용 추가
proxy: http://localhost:8080

2. 크롬 브라우저에 cors plug-in 설치 후 enable

3. 2번까지만 하고 사용해도 무방하나, Back End 단에도 설정이 가능하다.
@RestController 어노테이션이 있는 java 파일에 아래 내용 추가
@Crossorigin(origin="*")

```

도움이 되었으면 좋겠네요. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io