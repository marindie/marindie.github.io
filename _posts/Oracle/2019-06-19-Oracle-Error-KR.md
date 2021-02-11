---
toc: true
title: " Oracle 에러 이슈 모음. "
description: "Oracle Oracle 에러 이슈 모음. "
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/06/19/
---

> Oracle 에러 이슈 모음

### DBMS_METADATA.GET_DDL ORA-31603 이슈 원인 및 처리 방법 {#toc1}

```md
해당 에러는 보통 A Schema 유저가 B Schema Table Object 등의 정보를 DBMS_METADATA.GET_DDL
로 사용하고자 할때 발생하는 이슈 이다. 
Dictionary 조회 권한이 없는듯 하다.

```

```sql
DBA 권한이 있는 사용자가 아래의 select_catalog_role 을 부여해 주면 해결됨.
grant select_catalog_role to scott

-- 권한 요청하기 귀찮아서 그냥 Orange 에 F4 누르면 나오는 Script 정보를 건건이 사용중.. -_-+

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
