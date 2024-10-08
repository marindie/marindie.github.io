---
toc: true
title: " Oracle PL/SQL 을 이용한 DB TABLE 관련 JPA Entity, Repository 객체 생성 프로그램"
description: "Oracle PL/SQL 을 이용한 DB TABLE 관련 JPA 에서 사용하는 JPO MODEL Entity, Repository 객체 생성 프로그램 입니다."
categories: [Spring]
tags: [JPA,PL/SQL]
redirect_from:
  - /2019/03/21/
---

> Oracle PL/SQL 을 이용한 DB TABLE 관련 JPA 에서 사용하는 JPO MODEL Entity, Repository 객체 생성 프로그램을 공유합니다.

### 전제조건 {#toc1}

1. ORACLE DB 에서만 사용 가능 합니다.
2. 기본적인 PL/SQL 개념을 필요로 합니다.

### PL/SQL Block {#toc2}

```sql

--===============================
-- JPO Table 정보 생성기
--===============================
SET SERVEROUTPUT ON 
SET FEEDBACK OFF               
DECLARE
  TAB VARCHAR2(3000) := 'TEST'; 
  TAB_CAMEL_UPPER VARCHAR2(3000) := '';
  TAB_CAMEL_LOWER VARCHAR2(3000) := '';
  CNT NUMBER := 0;
  PK_DONE VARCHAR2(200) := 'N';
  MATCH_FOUND VARCHAR2(10) := '';
  OWNER_NM VARCHAR2(20) := 'ADMIN';
  PK_TYPE VARCHAR2(20) :='';
  CONST_NM VARCHAR2(200) := 'NO';
  TEMP_STR VARCHAR2(9999) := '';
  TYPE V_ARR IS TABLE OF VARCHAR2(1000);
  PK_COLS V_ARR := V_ARR();
  IND NUMBER := 1;
BEGIN
  DBMS_OUTPUT.ENABLE(1000000);
--CHECK PK
    SELECT COUNT(*) INTO CNT
    FROM ALL_CONS_COLUMNS A, ALL_CONSTRAINTS B
    WHERE A.CONSTRAINT_NAME = B.CONSTRAINT_NAME
    AND B.CONSTRAINT_TYPE = 'P'
    AND A.OWNER = OWNER_NM
    AND A.TABLE_NAME = TAB;

  DBMS_OUTPUT.PUT_LINE('@Entity');
  DBMS_OUTPUT.PUT_LINE('@Table(name = "'||TAB||'")');
  SELECT UPPER(SUBSTR(REPLACE(INITCAP(TAB),'_'),1,1))||SUBSTR(REPLACE(INITCAP(TAB),'_'),2) INTO TAB_CAMEL_UPPER FROM DUAL;
  SELECT LOWER(SUBSTR(REPLACE(INITCAP(TAB),'_'),1,1))||SUBSTR(REPLACE(INITCAP(TAB),'_'),2) INTO TAB_CAMEL_LOWER FROM DUAL;
  DBMS_OUTPUT.PUT_LINE('public class '||TAB_CAMEL_UPPER||'Jpo {'); 
  DBMS_OUTPUT.PUT_LINE('');
  IF CNT = 0 THEN
    DBMS_OUTPUT.PUT_LINE(chr(9)||'@Id');
    DBMS_OUTPUT.PUT_LINE(chr(9)||'@Column(name="ROWID")');
    DBMS_OUTPUT.PUT_LINE(chr(9)||'String rowid;');
    DBMS_OUTPUT.PUT_LINE('');
  END IF;
  
  FOR C IN (SELECT LOWER(SUBSTR(REPLACE(INITCAP(COLUMN_NAME),'_'),1,1))
       ||
       SUBSTR(REPLACE(INITCAP(COLUMN_NAME),'_'),2) AS COLUMN_NAME,
       DECODE(DATA_TYPE,'VARCHAR2','String','NUMBER','Long','DATE','Date','TIMESTAMP(6)','Date','CHAR','String',DATA_TYPE) AS DATA_TYPE,
       COLUMN_NAME AS OLD_COL
  FROM ALL_TAB_COLUMNS WHERE OWNER = OWNER_NM AND TABLE_NAME = TAB
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
        FOR D IN (SELECT COLUMN_NAME FROM ALL_CONS_COLUMNS A, ALL_CONSTRAINTS B
                    WHERE A.CONSTRAINT_NAME = B.CONSTRAINT_NAME
                    AND B.CONSTRAINT_TYPE = 'P'
                    AND A.OWNER = OWNER_NM
                    AND A.TABLE_NAME = TAB) LOOP
            IF D.COLUMN_NAME = C.OLD_COL THEN
                PK_COLS.EXTEND;
                PK_COLS(IND) := D.COLUMN_NAME;
                IND := IND + 1;            
                MATCH_FOUND := 'Y';
            END IF;
        END LOOP;
        IF MATCH_FOUND = 'N' THEN
            DBMS_OUTPUT.PUT_LINE(chr(9)||'@Column(name = "'||C.OLD_COL||'")');
            DBMS_OUTPUT.PUT_LINE(chr(9)||'private '||C.DATA_TYPE||' '||C.COLUMN_NAME||';');
            DBMS_OUTPUT.PUT_LINE('');                
        END IF;
    ELSE 
        FOR D IN (SELECT COLUMN_NAME FROM ALL_CONS_COLUMNS A, ALL_CONSTRAINTS B
                    WHERE A.CONSTRAINT_NAME = B.CONSTRAINT_NAME
                    AND B.CONSTRAINT_TYPE = 'P'
                    AND A.OWNER = OWNER_NM
                    AND A.TABLE_NAME = TAB) LOOP
            IF D.COLUMN_NAME = C.OLD_COL THEN
                DBMS_OUTPUT.PUT_LINE(chr(9)||'@Id');
                PK_TYPE := C.DATA_TYPE;
            END IF;
        END LOOP;        
        DBMS_OUTPUT.PUT_LINE(chr(9)||'@Column(name = "'||C.OLD_COL||'")');
        DBMS_OUTPUT.PUT_LINE(chr(9)||'private '||C.DATA_TYPE||' '||C.COLUMN_NAME||';');
        DBMS_OUTPUT.PUT_LINE('');    
    END IF;
  
  END LOOP;
  DBMS_OUTPUT.PUT_LINE('}');
  DBMS_OUTPUT.PUT_LINE('');
  
  --CREATE COMPOSITE CLASS
  IF CNT > 1 THEN
      DBMS_OUTPUT.PUT_LINE('@Embeddable');
      DBMS_OUTPUT.PUT_LINE('public class '||TAB_CAMEL_UPPER||'Pk  implements Serializable {'); 
      
      FOR C IN (SELECT LOWER(SUBSTR(REPLACE(INITCAP(B.COLUMN_NAME),'_'),1,1))
           ||
           SUBSTR(REPLACE(INITCAP(B.COLUMN_NAME),'_'),2) AS COLUMN_NAME,
           DECODE(DATA_TYPE,'VARCHAR2','String','NUMBER','Long','DATE','Date','TIMESTAMP(6)','Date','CHAR','String',B.DATA_TYPE) AS DATA_TYPE,
           A.COLUMN_NAME AS OLD_COL
        FROM ALL_CONS_COLUMNS A, ALL_TAB_COLS B
        WHERE CONSTRAINT_NAME = (SELECT CONSTRAINT_NAME FROM ALL_CONSTRAINTS A WHERE CONSTRAINT_TYPE = 'P'
                                 AND TABLE_NAME = TAB AND OWNER = OWNER_NM)
        AND A.OWNER = OWNER_NM
        AND A.COLUMN_NAME = B.COLUMN_NAME
        AND A.TABLE_NAME = B.TABLE_NAME
        ORDER BY B.COLUMN_ID
        ) LOOP
        DBMS_OUTPUT.PUT_LINE(chr(9)||'@Column(name = "'||C.OLD_COL||'")');
        DBMS_OUTPUT.PUT_LINE(chr(9)||'private '||C.DATA_TYPE||' '||C.COLUMN_NAME||';');
        DBMS_OUTPUT.PUT_LINE('');
      END LOOP;
      DBMS_OUTPUT.PUT_LINE('}');
  END IF;
  
  --interface
  IF CNT > 1 THEN
    DBMS_OUTPUT.PUT_LINE('public interface '||REPLACE(TAB_CAMEL_UPPER,'TbM00','')||'Repository extends JpaRepository<'||TAB_CAMEL_UPPER||'Jpo,'||TAB_CAMEL_UPPER||'Pk> {');
  ELSIF CNT = 0 THEN    
    DBMS_OUTPUT.PUT_LINE('public interface '||REPLACE(TAB_CAMEL_UPPER,'TbM00','')||'Repository extends JpaRepository<'||TAB_CAMEL_UPPER||'Jpo,String> {');
  ELSE
    DBMS_OUTPUT.PUT_LINE('public interface '||REPLACE(TAB_CAMEL_UPPER,'TbM00','')||'Repository extends JpaRepository<'||TAB_CAMEL_UPPER||'Jpo,'||PK_TYPE||'> {');
  END IF;
  FOR C IN (SELECT  C.CONSTRAINT_NAME, C.CONSTRAINT_TYPE, A.POSITION,
        UPPER(SUBSTR(REPLACE(INITCAP(B.COLUMN_NAME),'_'),1,1))
           ||
           SUBSTR(REPLACE(INITCAP(B.COLUMN_NAME),'_'),2) AS COLUMN_NAME_UP,
        LOWER(SUBSTR(REPLACE(INITCAP(B.COLUMN_NAME),'_'),1,1))
           ||
           SUBSTR(REPLACE(INITCAP(B.COLUMN_NAME),'_'),2) AS COLUMN_NAME_LOW,           
           DECODE(DATA_TYPE,'VARCHAR2','String','NUMBER','Long','DATE','Date','TIMESTAMP(6)','Date','CHAR','String',B.DATA_TYPE) AS DATA_TYPE,
           A.COLUMN_NAME AS OLD_COL
        FROM ALL_CONS_COLUMNS A, ALL_TAB_COLS B, ALL_CONSTRAINTS C
        WHERE A.OWNER = OWNER_NM
        AND A.CONSTRAINT_NAME = C.CONSTRAINT_NAME
        AND C.TABLE_NAME = TAB
        AND C.CONSTRAINT_NAME NOT LIKE '%SYS%'
        AND A.COLUMN_NAME = B.COLUMN_NAME
        AND A.TABLE_NAME = B.TABLE_NAME
        ORDER BY A.CONSTRAINT_NAME,POSITION) LOOP
    -- JPA Query
    IF CONST_NM = 'NO' THEN
        CONST_NM := C.CONSTRAINT_NAME;
        -- Unique Check
        IF C.CONSTRAINT_TYPE = 'U' OR C.CONSTRAINT_TYPE = 'P' THEN
            IF CNT > 1 THEN
                TEMP_STR := chr(9)||'public Optional<'||TAB_CAMEL_UPPER||'Jpo> findBy';
                FOR D IN PK_COLS.FIRST .. PK_COLS.LAST LOOP
                    IF PK_COLS(D) = C.OLD_COL THEN
                        TEMP_STR := TEMP_STR||TAB_CAMEL_UPPER||'Pk'||C.COLUMN_NAME_UP;
                    END IF;
                END LOOP;
                TEMP_STR := TEMP_STR||'('||C.DATA_TYPE||' '||C.COLUMN_NAME_LOW||');';
            ELSE
                TEMP_STR := chr(9)||'public Optional<'||TAB_CAMEL_UPPER||'Jpo> findBy'||C.COLUMN_NAME_UP||'('||C.DATA_TYPE||' '||C.COLUMN_NAME_LOW||');';    
            END IF;
        ELSE
            TEMP_STR := chr(9)||'public Optional<List<'||TAB_CAMEL_UPPER||'Jpo>> findBy'||C.COLUMN_NAME_UP||'('||C.DATA_TYPE||' '||C.COLUMN_NAME_LOW||');';
        END IF;        
    ELSIF CONST_NM = C.CONSTRAINT_NAME THEN
        IF C.CONSTRAINT_TYPE = 'U' OR C.CONSTRAINT_TYPE = 'P' THEN
            IF CNT > 1 THEN
                FOR D IN PK_COLS.FIRST .. PK_COLS.LAST LOOP
                    IF PK_COLS(D) = C.OLD_COL THEN
                        TEMP_STR := REPLACE(TEMP_STR,'(','And'||TAB_CAMEL_UPPER||'Pk'||C.COLUMN_NAME_UP||'(');
                        TEMP_STR := REPLACE(TEMP_STR,')',','||C.DATA_TYPE||' '||C.COLUMN_NAME_LOW||')');               
                    END IF;
                END LOOP;
            ELSE
                TEMP_STR := REPLACE(TEMP_STR,'(','And'||C.COLUMN_NAME_UP||'(');
                TEMP_STR := REPLACE(TEMP_STR,')',','||C.DATA_TYPE||' '||C.COLUMN_NAME_LOW||')');               
            END IF;        
        ELSE
            TEMP_STR := REPLACE(TEMP_STR,'(','And'||C.COLUMN_NAME_UP||'(');
            TEMP_STR := REPLACE(TEMP_STR,')',','||C.DATA_TYPE||' '||C.COLUMN_NAME_LOW||')');
        END IF;
    ELSE 
        CONST_NM := C.CONSTRAINT_NAME;
        DBMS_OUTPUT.PUT_LINE(TEMP_STR);
        -- Unique Check
        IF C.CONSTRAINT_TYPE = 'U' OR C.CONSTRAINT_TYPE = 'P' THEN
            IF CNT > 1 THEN
                TEMP_STR := chr(9)||'public Optional<'||TAB_CAMEL_UPPER||'Jpo> findBy';
                FOR D IN PK_COLS.FIRST .. PK_COLS.LAST LOOP
                    IF PK_COLS(D) = C.OLD_COL THEN
                        TEMP_STR := TEMP_STR||TAB_CAMEL_UPPER||'Pk'||C.COLUMN_NAME_UP;
                    END IF;
                END LOOP;
                TEMP_STR := TEMP_STR||'('||C.DATA_TYPE||' '||C.COLUMN_NAME_LOW||');';
            ELSE
                TEMP_STR := chr(9)||'public Optional<'||TAB_CAMEL_UPPER||'Jpo> findBy'||C.COLUMN_NAME_UP||'('||C.DATA_TYPE||' '||C.COLUMN_NAME_LOW||');';    
            END IF;
        ELSE
            TEMP_STR := chr(9)||'public Optional<List<'||TAB_CAMEL_UPPER||'Jpo>> findBy'||C.COLUMN_NAME_UP||'('||C.DATA_TYPE||' '||C.COLUMN_NAME_LOW||');';
        END IF;        
    END IF;
  END LOOP;
  DBMS_OUTPUT.PUT_LINE(TEMP_STR);
  DBMS_OUTPUT.PUT_LINE('}');
END;
/


```

### 내용 설명 {#toc3}

1. 이 프로그램이 처리해 주는 유용한 기능은 컬럼명을 Camelcase 로 변환하여 변수 이름으로 만들어 주는 기능입니다.
2. Class 명의 맨 앞글자는 UPPER CASE 로 해줍니다.
3. PK 가 한개 이상일 경우, Embeddable, EmbeddedId 어노테이션 추가 및 관련 PK 전용 Composite Class 를 밑에 따로 만들어줍니다.
4. 일반적으로 저는 이 프로그램 결과를 복사 붙여넣기 후에 STS 나 Eclipse IDE 에서 제공하는 getter, setter 를 생성하여 사용합니다.
5. DATA_TYPE 은 필요에 따라 DECODE 함수에 추가해서 사용하시면 됩니다.
6. Embeddable Composite Class 자동생성도 추가 하였습니다.
7. JpaRepository Interface 자동생성도 추가하였습니다. INDEX 정보를 파싱해서 Composite Class 가 있는경우도 자동으로 생성해줍니다.
   findBy 문을 자동으로 생성해 준다고 이해하시면 되겠습니다.

### Sample Result {#toc4}

```java
@Entity
@Table(name="TEST")
public class Test {

	@Id (name = "TEST_ID")
	private Long testId;

	@Column(name = "TEST_NM")
	private String testNm;
}

public interface TestRepository extends JpaRepository<TestJpo,Long> {
	public Optional<TestJpo> findByTestId(Long testId);
}
```
PK 가 한개 이상인 경우

```java
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

public interface TestRepository extends JpaRepository<TestJpo,TestTwoPkColsPk> {
	public Optional<TestJpo> findByTestTwoPkTestColOneAndTestTwoPktestColTwo(Long testColOne,Long testColTwo);
}

```

도움이 되었으면 좋겠네요. 감사합니다.


