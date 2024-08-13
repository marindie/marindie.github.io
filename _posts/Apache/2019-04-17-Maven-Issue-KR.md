---
toc: true
title: "Apache Maven 이슈 기록"
description: "Maven 세팅 관련 경험한 Issue 들을 기록해 놓은 공간입니다."
categories: [Maven]
tags: [Maven]
redirect_from:
  - /2019/04/17/
---

> Maven 세팅 관련 경험한 Issue 들을 기록해 놓은 공간입니다.

### Failure to transfer org.apache.maven.plugins:maven-surefire-plugin:pom:2.12.4 {#toc1}

```md

해당 에러는 사실 CA 관련 이슈로 보는게 맞는거 같지만, 일단 여기에도 기록해 놓는다.
기존 프로젝트를 다른 PC 에 옮기다 보면 종종 발생한다.
maven 세팅 방식은 
maven 설치 경로의 conf 폴더에 repository 를 잡던가
user 경로라고 하는 C:\Users\administrator\.m2 등등 사용자 PC 기본 경로의 .m2 폴더 안에 repository 를 사용하게 된다.

그럼 둘중 뭐가 우선순위이냐.. user 경로가 우선순위 라고 한다..

그래서 .m2 안의 repository 를 싹 비우고, sts 나 eclipse 에서 Maven - Update Project(Force Update of Snapshots/Releases 체크) 하고 진행하면
다시 내용을 다운받아서 만들어준다. 보통 이렇게 해결을 많이 하게 된다..

만약에 그래도 안된다면, sts 나 eclipse Window - Preferences - Java - Installed JREs 를 확인해 보면 JRE 가 잡혀있는 경우가 있다.
JDK 로 바꿔주면 안되는 경우는 거의 없다.

드물지만, 인증서 문제로 maven https 접속에러 관련 문제가 발생하는데, 이거는 아래의 명령어를 
자바의 security 폴더안에서 실행하면 된다.
Ex) cd C:\Program Files\Java\jdk1.8.0_72\jre\lib\security
keytool -import -file "C:\USER.cer" -keystore cacerts -storepass "changeit"

이렇게 하는 이유는, 아무 경로에서 아래의 명령어를 날리면,
keytool -import -file "C:\USER.cer" -keystore "C:\Program Files\Java\jdk1.8.0_72\jre\lib\security" -storepass "changeit"

인증서 등록하시겠습니까? 물음에 y 로 하고나면, 
인증서가 등록 되었습니다 가 뜨시만, 그 다음에 file access 접근 불가 에러가 뜨면서 
실제 반영이 안된다. 그래서 해당 폴더까지 가서 명령어를 날린다.

```
