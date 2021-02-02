---
toc: true
title: " Spring Boot Build 없이 Run"
description: "Spring Spring Boot Build 없이 Run"
categories: [Spring]
tags: [Spring]
redirect_from:
  - /2019/07/19/
---

> Spring Boot Build 없이 Run

### Spring Boot Build 없이 Run {#toc1}

```java
1. boot 서비스 pom.xml 파일에 아래 추가

<dependency>
 <groupId>org.springframework</groupId>
 <artifactId>springloaded</artifactId>
 <version>1.2.8.RELEASE</version>
</dependency>

2. run Configurations 설정
boot 서비스 우클릭 → Run As → Run Configurations → Spring Boot App  → Arguments → VM arguments 에디터 창에서 아래 설정 작성
-javaagent:C:\Users\Administrator\.m2\repository\org\springframework\springloaded\1.2.8.RELEASE\springloaded-1.2.8.RELEASE.jar -noverify
※ 단, 경로는 본인 PC 경로 설정 할것

3. Mvn update 실시

4.  jar 파일 확인
C:\Users\Administrator\.m2\repository\org\springframework\springloaded\1.2.8.RELEASE\ 내에 springloaded-1.2.8.RELEASE.jar 확인

5. Run As → Spring Boot App
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io