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

# Crossorigin Issue {#toc1}

서로 다른 Domain 사이의 보안 이슈를 막기위해 발생하는 에러로 이해하였습니다.

# 해결방법들 {#toc2}

Front 단에 설정을 하든 Back 단에 설정을 하든 둘중 하나만 하면 되는것으로 알고 있습니다.
하지만 크롬 브라우저 사용자라면, cors 플러그인을 설치하고 활성화를 매번 시켜주어야 정상 동작이 되는 것을 확인하였습니다.
React 를 기동 후 크롬을 열었을대 cors 플러그인이 활성화 되어 있어도, 정상 동작하지 않는 경우가 있었습니다.
이때 저는 다시 cors 를 비활성화 후에 활성화 하고 테스트해보았더니 통과가 되어 정상 적으로 데이터를 받았습니다.
참고 하시면 좋을것 같습니다.

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