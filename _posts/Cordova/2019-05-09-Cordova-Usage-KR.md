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

### Android Studio 프로젝트 Cordova 로 Mig {#toc5}
```md
일단 hello world 가 나오는 hello 프로젝트를 하나 만들고 디바이스에서 기동 되는거 까지는 진행.
그리고 새로운 프로젝트를 하나더 만듬

cordova create targetApp com.target.app targetAppName
cd targetApp
cordova platform add android 까지는 진행.

app 폴더 아래에 일단 기존의 Android Studio 폴더 내용으로 교체

MainActivity.java 파일이 없으면 hello 프로젝트에서 가져오거나, 내용을 봐서
동작하게끔 수정. (난 그냥 복붙했음.) 이걸 하는 이유는 public class MainActivity extends CordovaActivity 
CordovaActivity 를 extends 하는 무언가를 찾기 때문..

기존 Android Studio 의 AndroidManifest.xml 을 백업해두고,
hello 프로젝트의 AndroidManifest.xml 의 내용을 복사해서 app 하위의 AndroidManifest.xml 로 붙여넣고
기존 Android Studio 의 AndroidManifest.xml 내용에서 필요한 부분을 추가해줌.

AndroidManifest.xml 의 package 명과, build.gradle 의 applicationid 값이 동일한지 확인.
추가로 Cordova 에서는 해당 package 명의 디렉토리 구조가 존재하는지도 확인하기에 폴더 구조도
필요시 Cordova 생성시 잡은 package 명과 일치시켜줌. 여기 예시에서는 app/src/main/java/com/target/app 폴더구조.

특이사항으로 Android Studio 에서는 httpcore-4.4.11.jar 가 없어도 import 사용이 가능했지만,
Cordova 에서는 안되는듯.. Android Studio 에서 httpcore-4.4.11.jar 없이도 잘 되는 이유도 사실 모름. 
그래서 그냥 Apache 사이트에서 httpcore-4.4.11.jar 가져와서 libs 폴더 아래에 떨굼

Cordova 는 기본적으로 app/libs 아래의 jar 파일들을 읽게끔 기본적으로 설정 정보를 생성한다
이게 그 구문
dependencies {
    implementation fileTree(dir: 'libs', include: '*.jar')
    // SUB-PROJECT DEPENDENCIES START
    implementation(project(path: ":CordovaLib"))
    // SUB-PROJECT DEPENDENCIES END
}

More than one file was found with OS independent path 'META-INF/LICENSE' 에러가 나서

build.gradle 에 packagingOptions 내용을 android 안에 추가해줬다
android{
      packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/LICENSE.txt'
        exclude 'META-INF/license.txt'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/NOTICE.txt'
        exclude 'META-INF/notice.txt'
        exclude 'META-INF/ASL2.0'
      }
}

CordovaLib 의 AndroidManifest.xml 파일이 있는데 기본적으로 아래의 설정이 들어가는듯 하다.
문제는 Android Studio 플젝에서 Gradle Sync 시에 이 녀석으로 인해 에러를 발생시키고
build.gradle 파일의 설정을 사용하도록 안내가 나와서 그냥 지워봤더니 성공하고 디바이스 run 활성화 되었다.
    <uses-sdk android:minSdkVersion="19" />

build.gradle
    minSdkVersion 19
    compileSdkVersion 19
    buildToolsVersion "28.0.3"   

applicationid 와 package 명이 서로 달라서 applicationid 명으로 일괄 변경하였다.
include ~ 
package ~
AndroidManifest.xml 의 package 명을 application id 로 변경   

AndroidManifest.xml 의 android:versionName="1.0.0" 으로 인해 이슈가 발생해서 수정해서 성공
```

### More than one file was found with OS independent path 'META-INF/LICENSE' {#toc6}
```md
build.gradle 에 packagingOptions 내용을 android 안에 추가해줬다
android{
      packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/LICENSE.txt'
        exclude 'META-INF/license.txt'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/NOTICE.txt'
        exclude 'META-INF/notice.txt'
        exclude 'META-INF/ASL2.0'
      }
}
```

### Android or Cordova package not found (does not exist 등등) {#toc6}
```md
Cordova 에서 하면 뭔가 다른 설정을 잡아줘야 하는듯 했으나..
Android 의 app 아래의 build.gradle dependencies 설정이 
Cordova 프로젝트 설정시 자동생성이 된다. 그리고 jar 파일들이 잘 import 가 된다.
고로 에러가 난다면 7zip 으로 각 jar 파일을 열어서 진짜로 해당 package 가 존재하지
않는지 부터 보는게 정신건강에 좋다.

dependencies {
    implementation fileTree(dir: 'libs', include: '*.jar')
    // SUB-PROJECT DEPENDENCIES START
    implementation(project(path: ":CordovaLib"))
    // SUB-PROJECT DEPENDENCIES END
}
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io