---
toc: true
title: "Oracle 18c JDBC 연동시 Hibernate dialect 에러 발생"
description: "Oracle 18c JDBC 연동시 Hibernate dialect 에러 발생"
categories: [Spring]
tags: [Spring]
redirect_from:
  - /2019/06/12/
---

> Oracle 18c JDBC 연동시 Hibernate dialect 에러 발생

### Access to DialectResolutionInfo cannot be null when 'hibernate.dialect' not set {#toc1}

```yaml
Oracle JDBC 를 Spring Boot 로 실행 하는데 11g, 12c 는 잘 붙는데 18c 만 
안붙으면서 에러가 발생해서 스트레스를 좀 받았다. 에러를 검색해도 마땅히 관련 자료가 없어서 기록해둠

결론은 Spring Boot 방식의 Configuration 에서 아래의 설정이 추가 되면 잘된다.

src/main/resources 에 설정 파일들을 두는데, application.properties 형태는 아래와 같고,
hibernate.dialect=org.hibernate.dialect.HSQLDialect

application.yml 파일 형태의 설정은 아래와 같다
dialect: org.hibernate.dialect.HSQLDialect

spring:
  application:
    name: test
    
  jpa:
    database: default
    properties:
      hibernate:
        dialect: org.hibernate.dialect.HSQLDialect
        show_sql: false
        meta_sql: false

```

### TIMEZONE 이슈 {#toc2}

```md
Spring Boot 기동시에 JAVA OPTION 으로 TIMEZONE 을 세팅해줘야 연결이 정상적으로 처리가 되는 경우가 있어서 적어 놓는다.

12c 에 붙는 케이스 였던것 같음.
java -Duser.timezone=GMT -jar app-0.0.1-SNAPSHOT.jar
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
