---
toc: true
title: "Spring: Oracle PL/SQL 을 이용한 DB TABLE 관련 JPA Entity 객체 생성 프로그램"
description: "Oracle PL/SQL 을 이용한 DB TABLE 관련 JPA 에서 사용하는 JPO MODEL Entity 객체 생성 프로그램 입니다."
categories: [Spring]
tags: [JPA,PL/SQL]
redirect_from:
  - /2019/03/21/
---

> Oracle PL/SQL 을 이용한 DB TABLE 관련 JPA 에서 사용하는 JPO MODEL Entity 객체 생성 프로그램을 공유합니다.

# 전제조건 {#toc1}

1. ORACLE DB 에서만 사용 가능 합니다.
2. 기본적인 PL/SQL 개념을 필요로 합니다.

# PL/SQL Block {#toc2}

```sql

--===============================
-- JPO Table 정보 생성기
--===============================
SET SERVEROUTPUT ON
SET FEEDBACK OFF
DECLARE
  TAB VARCHAR2(3000) := 'TEST';
  TAB_CAMEL VARCHAR2(3000) := '';
BEGIN
  DBMS_OUTPUT.PUT_LINE('@Entity');
  DBMS_OUTPUT.PUT_LINE('@Table(name = "'||TAB||'")');
  SELECT UPPER(SUBSTR(REPLACE(INITCAP(TAB),'_'),1,1))||SUBSTR(REPLACE(INITCAP(TAB),'_'),2) INTO TAB_CAMEL FROM DUAL;
  DBMS_OUTPUT.PUT_LINE('public class '||TAB_CAMEL||' {'); 
  DBMS_OUTPUT.PUT_LINE('');
  FOR C IN (SELECT LOWER(SUBSTR(REPLACE(INITCAP(COLUMN_NAME),'_'),1,1))
       ||
       SUBSTR(REPLACE(INITCAP(COLUMN_NAME),'_'),2) AS COLUMN_NAME,
       DECODE(DATA_TYPE,'VARCHAR2','String','NUMBER','int','DATE','Date','TIMESTAMP(6)','Date',DATA_TYPE) AS DATA_TYPE,
       COLUMN_NAME AS OLD_COL
  FROM ALL_TAB_COLS WHERE OWNER = 'POSMAST' AND TABLE_NAME = TAB
  ORDER BY COLUMN_ID) LOOP
    DBMS_OUTPUT.PUT_LINE(chr(9)||'@Column(name = "'||C.OLD_COL||'")');
    DBMS_OUTPUT.PUT_LINE(chr(9)||'private '||C.DATA_TYPE||' '||C.COLUMN_NAME||';');
    DBMS_OUTPUT.PUT_LINE('');
  END LOOP;
  DBMS_OUTPUT.PUT_LINE('}');
END;
/

```

# 내용 설명 {#toc3}

1. 이 프로그램이 처리해 주는 유용한 기능은 컬럼명을 Camelcase 로 변환하여 변수 이름으로 만들어 주는 기능입니다.
2. Class 명의 맨 앞글자는 UPPER CASE 로 해줍니다.
3. ID 나 기타 어노테이션은 없습니다.
4. 일반적으로 저는 이 내용에서 STS 나 Eclipse IDE 에서 제공하는 getter, setter 를 생성하여 사용합니다.
5. 처리하고자 하는 DATA_TYPE 에 따라 DECODE 함수에 추가해서 사용하시면 됩니다.

# Sample Result {#toc4}

```md
@Entity
@Table(name="TEST")
public class Test {

	@Id
	@Column(name = "TEST_ID")
	private Long testId;

	@Column(name = "TEST_NM")
	private String testNm;
}
```

도움이 되었으면 좋겠네요. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io