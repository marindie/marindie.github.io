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
// SELF_SIGN 에러 나면 npm set strict-ssl false 명령어 날리고 다시 명령어 실행
```

### Cordova add platform {#toc2}

```js
cordova platform add android
```

### ANDROID_HOME Environment Set {#toc3}
```md
변수 이름: ANDROID_HOME 
변수 값: C:\Users\username\AppData\Local\Android\Sdk
```

### JAVA_OPTION Environment Set {#toc4}
```md
변수 이름: _JAVA_OPTIONS 
변수 값: -Djava.net.preferIPv4Stack=true
```

### ANDROID STUDIO AVD 생성 {#toc5}
```md 
ANDROID STUDIO 실행 - 새로운 프로젝트 생성 - Gradle Sync 완료 대기 - Tool 에 AVD Manager 나오는지 확인
나오면 클릭해서 AVD 하나 생성. 
이렇게 만들어 놓으면 나중에 cordova run android 하면 만들어져 있는 녀석을 알아서 찾아서 띄워줌.
만약에 PC에 USB 로 핸드폰 연결 되어 있고 DEBUG 모드 활성화 되어 있으면 자동으로 핸드폰에 앱 기동됨.

잘 안되면 그냥 cordova build android 하고, Android Studio 열어서 프로젝트 열기로 해서
platform 폴더 안의 android 폴더로 잡고 gradle sync 하고 나서 run 해서 사용하면 됨.

그게 그거임. 똑같음. cli 냐 ide 냐 차이
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io