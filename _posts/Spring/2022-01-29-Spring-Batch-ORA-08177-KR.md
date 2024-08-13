---
toc: true
title: "Spring Boot Batch ORA-08177 이 트랜잭션에 대한 직렬화 액세스를 할 수 없습니다 처리"
description: "Spring Boot Batch ORA-08177 이 트랜잭션에 대한 직렬화 액세스를 할 수 없습니다 처리"
categories: [Spring]
tags: [Spring]
redirect_from:
  - /2022/01/29/
---

> Spring Boot Batch ORA-08177 이 트랜잭션에 대한 직렬화 액세스를 할 수 없습니다 처리

### ORA-08177 이 트랜잭션에 대한 직렬화 액세스를 할 수 없습니다 처리 {#toc1}

```xml
SPRING BOOT 2.6.3 사용
SPRING BATCH 샘플 작성후 테스트시 발생

ORACLE 19c 사용시 발생
application.properties 파일에 아래 옵션 추가
batch.repository.isolationlevelforcreate=ISOLATION_READ_COMMITTED

ISOLATION_DEFAULT 로 해봤는데 여전히 에러났었음.
구글 다른 검색 결과는 19c에 해당 없는 듯
```


