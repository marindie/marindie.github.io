---
toc: true
title: " 서버 API 호출시 발생하는 Crossorigin 해결 방법 정리"
description: "React 로 Ajax(axios or fetch 등등) 호출 시 chrome browser 에서 발생 가능한 Crossorigin 문제 해결 방법 정리 내용입니다."
categories: [React]
tags: [Crossorigin]
redirect_from:
  - /2019/03/21/
---

> React 로 Ajax(axios or fetch 등등) 호출 시 chrome browser 에서 발생 가능한 Crossorigin 문제 해결 방법 정리 내용입니다.

### Crossorigin Issue {#toc1}

서로 다른 Domain 사이의 보안 이슈를 막기위해 발생하는 에러로 이해하였습니다.

### 해결 가능한 방법들 {#toc2}

Front 단에 설정을 하든 Back 단에 설정을 하든 둘중 하나만 하면 되는것으로 알고 있습니다.

하지만 크롬 브라우저 사용자라면, cors 플러그인을 설치하고 활성화를 매번 시켜주어야 정상 동작이 되는 것을 확인하였습니다.
이와 같은 증상이 모든 사용자에 발생하는 것은 아니며, 저의 경우에 발생하여 언급합니다.

React 를 기동 후 크롬을 열었을대 cors 플러그인이 활성화 되어 있어도, 정상 동작하지 않는 경우가 있었습니다.
이때 저는 다시 cors 를 비활성화 후에 활성화 하고 테스트해보았더니 통과가 되어 정상 적으로 데이터를 받았습니다.
참고 하시면 좋을것 같습니다.

```md

1. package.json 에 아래 내용 추가
아래의 내용과 관련하여 알아 두셔야 할 내용은, react proxy 는 developer, 개발자 모드에서만 사용이 가능합니다.
따라서 production 으로 넘어갔을 경우에 CORS 이슈가 생긴다면, 이부분은 Back-End 서버 에서 CORS 를 처리해 주어야 합니다.

proxy: http://localhost:8080

2. 크롬 브라우저에 cors plug-in 설치 후 enable

3. 2번까지만 하고 사용해도 무방하나, Back End 단에도 설정이 가능하다.
@RestController 어노테이션이 있는 java 파일에 아래 내용 추가
@CrossOrigin(origins="*")

혹은 @Configure 로 설정하는 방법이 있습니다.
```

### CORS 관련 preflight 403 Error 이슈 {#toc2}

Spring Boot Security 설정시 CORS Preflight 403 이슈가 발생하였습니다.
검색해 보니 실제 전송전에 전송가능한지 확인하는 방식이 preflight 방식이라고 합니다.
문제는 REQUEST METHOD 가 OPTION 인데 응답을 Spring이 정상으로 던지지 않는것이 문제인듯 합니다.
위의 CrossOrigin 이나 Configure 를 해보아도 되지 않아 검색중에 있습니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
