---
toc: true
title: "Android: Android Studio 사용 관련 이슈 기록. 설정, 에러 등등"
description: "Android Studio 사용 관련 이슈 기록. 설정, 에러 등등" 
categories: [Android]
tags: [Android]
redirect_from:
  - /2019/05/29/
---

> Android Studio 사용 관련 이슈 기록. 설정, 에러 등등

### ANDROID_HOME Environment Set {#toc1}
```md
변수 이름: ANDROID_HOME 
변수 값: C:\Users\username\AppData\Local\Android\Sdk
```

### JAVA_OPTION Environment Set {#toc2}
```md
andoid: sync failed. Protocol family unavailable. 에러가 나면 환경변수 때문임.
변수 이름: _JAVA_OPTIONS 
변수 값: -Djava.net.preferIPv4Stack=true
```

### ANDROID STUDIO AVD 생성 {#toc3}
```md 
ANDROID STUDIO 실행 - 새로운 프로젝트 생성 - Gradle Sync 완료 대기 - Tool 에 AVD Manager 나오는지 확인
나오면 클릭해서 AVD 하나 생성. 
이렇게 만들어 놓으면 나중에 cordova run android 하면 만들어져 있는 녀석을 알아서 찾아서 띄워줌.
만약에 PC에 USB 로 핸드폰 연결 되어 있고 DEBUG 모드 활성화 되어 있으면 자동으로 핸드폰에 앱 기동됨.

잘 안되면 그냥 cordova build android 하고, Android Studio 열어서 프로젝트 열기로 해서
platform 폴더 안의 android 폴더로 잡고 gradle sync 하고 나서 run 해서 사용하면 됨.

그게 그거임
```

### Insecure Connection {#toc3}
```md
File -> Settings -> System Settings -> Updates -> Use secure connection 체크 해제 -> OK 
```

### SDK Update Sites http 주소 사용 {#toc4}
```md
File -> Settings -> System Settings -> Android SDK -> SDK Update Sites 탭 -> 
Force https://.. sources to be fetched using http://... 체크 -> OK
```

### CA Certificate 추가 {#toc5}
```md
File -> Settings -> Tools -> Server Certificates -> + 버튼 클릭 -> yourCA.cer 등등 (확장자 다를수있음) 선택 -> OK 클릭
Update 등등이 잘 되면 잘 되는 걸로 생각하고 있습니다.
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io