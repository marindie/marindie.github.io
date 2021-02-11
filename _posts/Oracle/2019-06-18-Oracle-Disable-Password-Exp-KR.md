---
toc: true
title: " Oracle Disable Passoword Expiry Policy"
description: "Oracle Oracle Disable Passoword Expiry Policy"
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/06/18/
---

> Oracle Disable Passoword Expiry Policy

### 처리 방법 {#toc1}

```sql

-- profile name 조회
SELECT PROFILE FROM DBA_USERS WHERE USERNAME = 'SCOTT';
-- 해당 profile name 의 passowrd life time unlimited 로 변경
ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
-- 내용 확인
SELECT RESOURCE_NAME,LIMIT FROM DBA_PROFILES WHERE PROFILE='DEFAULT';

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
