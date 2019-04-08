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
  TAB VARCHAR2(3000) := 'TB_M00_CODES030'; 
  TAB_CAMEL_UPPER VARCHAR2(3000) := '';
  TAB_CAMEL_LOWER VARCHAR2(3000) := '';
  CNT NUMBER := 0;
  PK_DONE VARCHAR2(200) := 'N';
  MATCH_FOUND VARCHAR2(10) := '';
BEGIN

--CHECK PK
    SELECT COUNT(*) INTO CNT FROM ALL_CONS_COLUMNS
    WHERE CONSTRAINT_NAME = (SELECT CONSTRAINT_NAME FROM ALL_CONSTRAINTS WHERE CONSTRAINT_TYPE = 'P'
    AND TABLE_NAME = TAB);

  DBMS_OUTPUT.PUT_LINE('@Entity');
  DBMS_OUTPUT.PUT_LINE('@Table(name = "'||TAB||'")');
  SELECT UPPER(SUBSTR(REPLACE(INITCAP(TAB),'_'),1,1))||SUBSTR(REPLACE(INITCAP(TAB),'_'),2) INTO TAB_CAMEL_UPPER FROM DUAL;
  SELECT LOWER(SUBSTR(REPLACE(INITCAP(TAB),'_'),1,1))||SUBSTR(REPLACE(INITCAP(TAB),'_'),2) INTO TAB_CAMEL_LOWER FROM DUAL;
  DBMS_OUTPUT.PUT_LINE('public class '||TAB_CAMEL_UPPER||'Jpo {'); 
  DBMS_OUTPUT.PUT_LINE('');
  FOR C IN (SELECT LOWER(SUBSTR(REPLACE(INITCAP(COLUMN_NAME),'_'),1,1))
       ||
       SUBSTR(REPLACE(INITCAP(COLUMN_NAME),'_'),2) AS COLUMN_NAME,
       DECODE(DATA_TYPE,'VARCHAR2','String','NUMBER','Long','DATE','Date','TIMESTAMP(6)','Date',DATA_TYPE) AS DATA_TYPE,
       COLUMN_NAME AS OLD_COL
  FROM ALL_TAB_COLS WHERE OWNER = 'POSMAST' AND TABLE_NAME = TAB
  ORDER BY COLUMN_ID) LOOP
    MATCH_FOUND := 'N';
    --PK COLUMNS ARE MORE THAN 1. CREATE COMPOSITE CLASS Variable
    IF CNT > 1 THEN
        IF PK_DONE = 'N' THEN
            DBMS_OUTPUT.PUT_LINE(chr(9)||'@EmbeddedId');
            DBMS_OUTPUT.PUT_LINE(chr(9)||'private '||TAB_CAMEL_UPPER||'Pk '||TAB_CAMEL_LOWER||'Pk;');
            DBMS_OUTPUT.PUT_LINE('');
            PK_DONE := 'Y';
        END IF;
        FOR D IN (SELECT COLUMN_NAME FROM ALL_CONS_COLUMNS
            WHERE CONSTRAINT_NAME = (SELECT CONSTRAINT_NAME FROM ALL_CONSTRAINTS WHERE CONSTRAINT_TYPE = 'P'
            AND TABLE_NAME = TAB)) LOOP
            IF D.COLUMN_NAME = C.OLD_COL THEN
                MATCH_FOUND := 'Y';
            END IF;
        END LOOP;
        IF MATCH_FOUND = 'N' THEN
            DBMS_OUTPUT.PUT_LINE(chr(9)||'@Column(name = "'||C.OLD_COL||'")');
            DBMS_OUTPUT.PUT_LINE(chr(9)||'private '||C.DATA_TYPE||' '||C.COLUMN_NAME||';');
            DBMS_OUTPUT.PUT_LINE('');                
        END IF;
    ELSE 
        FOR D IN (SELECT COLUMN_NAME FROM ALL_CONS_COLUMNS
            WHERE CONSTRAINT_NAME = (SELECT CONSTRAINT_NAME FROM ALL_CONSTRAINTS WHERE CONSTRAINT_TYPE = 'P'
            AND TABLE_NAME = TAB)) LOOP
            IF D.COLUMN_NAME = C.OLD_COL THEN
                DBMS_OUTPUT.PUT_LINE(chr(9)||'@Id');
            END IF;
        END LOOP;        
        DBMS_OUTPUT.PUT_LINE(chr(9)||'@Column(name = "'||C.OLD_COL||'")');
        DBMS_OUTPUT.PUT_LINE(chr(9)||'private '||C.DATA_TYPE||' '||C.COLUMN_NAME||';');
        DBMS_OUTPUT.PUT_LINE('');    
    END IF;
  
  END LOOP;
  DBMS_OUTPUT.PUT_LINE('}');
  
  --CREATE COMPOSITE CLASS
  IF CNT > 1 THEN
      DBMS_OUTPUT.PUT_LINE('@Embeddable');
      DBMS_OUTPUT.PUT_LINE('public class '||TAB_CAMEL_UPPER||'Pk {'); 
      
      FOR C IN (SELECT LOWER(SUBSTR(REPLACE(INITCAP(B.COLUMN_NAME),'_'),1,1))
           ||
           SUBSTR(REPLACE(INITCAP(B.COLUMN_NAME),'_'),2) AS COLUMN_NAME,
           DECODE(DATA_TYPE,'VARCHAR2','String','NUMBER','Long','DATE','Date','TIMESTAMP(6)','Date',B.DATA_TYPE) AS DATA_TYPE
        FROM ALL_CONS_COLUMNS A, ALL_TAB_COLS B
        WHERE CONSTRAINT_NAME = (SELECT CONSTRAINT_NAME FROM ALL_CONSTRAINTS A WHERE CONSTRAINT_TYPE = 'P'
                                 AND TABLE_NAME = TAB)
        AND A.COLUMN_NAME = B.COLUMN_NAME
        AND A.TABLE_NAME = B.TABLE_NAME
        ORDER BY B.COLUMN_ID
        ) LOOP
        DBMS_OUTPUT.PUT_LINE(chr(9)||'private '||C.DATA_TYPE||' '||C.COLUMN_NAME||';');
      END LOOP;
      DBMS_OUTPUT.PUT_LINE('}');
  END IF;
END;
/


```

# 내용 설명 {#toc3}

1. 이 프로그램이 처리해 주는 유용한 기능은 컬럼명을 Camelcase 로 변환하여 변수 이름으로 만들어 주는 기능입니다.
2. Class 명의 맨 앞글자는 UPPER CASE 로 해줍니다.
3. PK 가 한개 이상일 경우, Embeddable, EmbeddedId 어노테이션 추가 및 관련 PK 전용 Composite Class 를 밑에 따로 만들어줍니다.
4. 일반적으로 저는 이 프로그램 결과를 복사 붙여넣기 후에 STS 나 Eclipse IDE 에서 제공하는 getter, setter 를 생성하여 사용합니다.
5. DATA_TYPE 은 필요에 따라 DECODE 함수에 추가해서 사용하시면 됩니다.

# Sample Result {#toc4}

```md
@Entity
@Table(name="TEST")
public class Test {

	@Id (name = "TEST_ID")
	private Long testId;

	@Column(name = "TEST_NM")
	private String testNm;
}
```
PK 가 한개 이상인 경우 
```md
@Entity
@Table(name="TEST_TWO_PK_COLS")
public class Test {

	@EmbeddedId
	private TestTwoPkColsPk testTwoPkColsPk;

	@Column(name = "TEST_COL_Three")
	private String testColThree;
}

@Embeddable
public class TestTwoPkColsPk {
  private Long testColOne;
  private Long testColTwo;
}


도움이 되었으면 좋겠네요. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io