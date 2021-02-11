---
toc: true
title: " npm install 등등 명령시 npm ERR! code SELF_SIGNED_CERT_IN_CHAIN 에러 문제 관련"
description: "npm 또는 nodejs 를 사용하면서 발생했던 에러 관련 모음으로 사용할 포스팅 입니다." 
categories: [Nodejs]
tags: [Npm]
redirect_from:
  - /2018/05/09/
---

> npm 또는 nodejs 를 사용하면서 발생했던 에러 관련 모음으로 사용할 포스팅 입니다.

### npm ERR! code SELF_SIGNED_CERT_IN_CHAIN {#toc1}

```md
회사에서 Cordova 시작하려는데 인증서 때문에 시작부터 에러 발생
SELF_SIGNED 인증서를 등록하는 방법을 찾으려다가 일단 패스하고
npm set strict-ssl false
```

### 해결방법들 {#toc2}

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
