---
toc: true
title: "Cordova: Cordova 기초 사용법 정리"
description: "Cordova 기초 사용법 정리" 
categories: [Cordova]
tags: [Cordova]
redirect_from:
  - /2019/05/09/
---

> Cordova 기초 사용법 정리

### Install Cordova {#toc1}

```js
npm install cordova -g //Global 옵션. 사용자 컴퓨터 어디에서든 사용가능

cordova create MyApp ca.griffith.steve.myapp AppName //MyApp 폴터 이름. ca.xxx 패키지명. AppName 앱 이름
// SELF_SIGN 에러 나면 npm set strict-ssl false 명령어 날리고 다시 install
```

### Cordova add platform {#toc1}

```js
cordova platform add android
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io