---
toc: true
title: " JAVA Certificate 추가 방법. CACERTS, KEYTOOL"
description: "JAVA Certificate 추가 방법. CACERTS, KEYTOOL"
categories: [Java]
tags: [Cacerts]
redirect_from:
  - /2019/05/27/
---

> JAVA Certificate 추가 방법. CACERTS, KEYTOOL

### 발생하는 상황들 {#toc1}
```md

회사에서 환경 구축을 하다 보면, Eclipse, Jenkins, git, npm든 각종 IDE 나 application tool 에서 ssl 이슈가 발생이 종종난다.
이유를 찾다보니.. 인증서를 회사 전용으로 가지고 있는경우에는 이거를 직접 추가해 주지 않으면.. 
인터넷 접속시 https 를 통한 다운로드시에 해당 인증서를 신뢰할 수 없는 상황으로 인해 집에서 하던것들이
전부다 안되는 상황이 발생한다. 대부분 JAVA 를 사용하는 IDE, TOOL 들은 아래의 방법으로 해결이 가능하다.
그외에 내가 겪었던 것들을 공유 한다.

```

### JAVA CACERT (인증저장소) Certificate 추가 {#toc2}
```text

eclipse, android, jenkins 등등 JAVA 를 기반으로 돌아가는 녀석들은
download 시에 cacerts 에 있는 인증 정보를 기반으로 https 요청 신뢰 여부를 판단하게 되는것 같다.
그래서 뭔가를 다운받거나, 접속을 할때, https 라면 JAVA cacerts 에 회사 인증서를 추가해야 잘 돌아간다.
이거 때문에 많은 의미없는 시간을 보내서 정리해 놓는다..

Java 의 경우 keytool 이라는 명령어를 사용해서 인증서를 추가 할수 있게 되어 있다.

keytool -import -file "C:\MYCertificate.cer" -keystore "C:\Program Files\Android\Android Studio\jre\jre\lib\security\cacerts" -storepass "changeit"
keytool -import -file "C:\MYCertificate.cer" -keystore "C:\Program Files\Java\jdk1.7.0_80\jre\lib\security\cacerts" -storepass "changeit"
keytool -import -file "C:\MYCertificate.cer" -keystore "C:\Program Files\Java\jre1.8.0_72\lib\security\cacerts" -storepass "changeit"

storepass 옵션에 caceerts 의 암호인 changeit 을 입력해 놓으면 된다. 기본 암호가 changeit 으로 알고 있다.
file 옵션에 .cer 확장자를 입력했는데 다른 형태의 확장자도 아마 가능할 것으로 생각 된다.
keystore 옵션에는 CA 인증서 위치를 적어야 한다. 

가장중요한 것은 해당 명령어 실행시, 등록하겠냐고 물어보는 질문에 y 입력후 엔터치면
밑에 로그가 짧게 나온다. 거기에 혹시 해당 위치를 접근할수 없다고 나오는 경우가 있다면,
cacerts 위치에 직접가서 keytool 명령어를 실행해 보기 바란다.

keytool -import -file "C:\MYCertificate.cer" -keystore "cacerts" -storepass "changeit"

내가 추천하는 바는, 설치된 JDK,JRE 가 있으면 각 cacerts 에 전부 keytool 로 추가 하고 
PC 재부팅 후에 테스트 하기를 권장한다.

```

### 그 외에 SSL 관련 발생한 경험들 {#toc3}
```md

git 의 경우에는 ssl disable 옵션으로 처리했다.
git config --global http.sslVerify false

insomnia 에서 https 연동 테스트시에도 ssl disable 을 설정 화면에서 disable 을 해놓고 테스트했더니
잘 되던것으로 기억한다.

```


[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io