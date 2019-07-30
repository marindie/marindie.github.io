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

### Unable to detect adb version, adb output {#toc6}
```md
https://androidsdkoffline.blogspot.com/p/android-sdk-platform-tools.html 에서 OS 에 맞는 platform 다운로드
나는 platform-tools_r26.0.0-windows.zip 로 받아서 Android Sdk 설치 경로의 platform-tools 폴더 삭제하고
다운 받은 platform-tools 폴더의 내용으로 복사해 넣고 Studio 재부팅하니까 잘 되었다.
```

### AAPT2 Demon Start Failed {#toc7}
```md
Gradle 버전을 다운그레이드 해서 해당 에러를 우회 하였다.

Go to File->Project-Structure->Project
Change Android Gradle Plugin Version to 3.3.2 or the last stable version for you
Change the Gradle Version to 4.10.1
Click Ok and sync
Build Apk

```

### Android RESTFul API Lib RETROFIT or VOLLEY {#toc8}
```md
REST API 구현에 좋다고 하는데, 안써봐서 모르겠음. 하지만 그냥 REST API 가 되는건 아니고 
이런 라이브러리를 사용해야 가능한듯.
```

### Android AVD 인식 안될때  {#toc9}
```md
https://androidmtk.com/download-universal-adb-driver 에서 monitoring tool 다운 후 확인
Device Setting Debugging 모드 활성화, Mobile Device Driver 설치 가 되었음에도 안되는 경우가 있다.
장치관리자에서 상태를 보라고 monitoring tool 이 알려줘서 봤더니 사용 불가 상태. 사용으로 바꿔지지가 않았다.

해결은 잘 되지 않았지만, 장치 사용 안함으로 하고 다시 사용으로 하면 잡히는 경우가 있다.
이유는 내가 찾지 못한 어떤 프로세스가 핸드폰 단말 AVD Interface 를 먼저 선점하고 있는데 내가 사용 안함으로 
끊어버리면 Android Studio 가 먼저 찾아서 리스트에 나오는듯 하다.

결론적으로 해당 프로세스가 뭔지를 파악해서 해결이 가능한지를 확인해 봐야 하지만.. 이런경우는 드물다.

대부분 Mobile Device Driver 와 AVD Interface Driver 그리고 Gradle Version Downgrade 정도면
최소한의 구동 가능한 Android Studio 개발은 되는듯 하다는게 나의 생각이다.

아래는 잡아줘야 한다는 권고사항의 CLASS_PATH 리스트
%ANDROID_HOME%\platform-tools
%GRADLE_HOME%\bin
%ANDROID_HOME%\tools
```



[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io