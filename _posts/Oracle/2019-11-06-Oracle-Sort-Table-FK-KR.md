---
toc: true
title: " Oracle Create Table 시에 FK Constraint 감안한 Table 생성 순서 List 추출 프로그램 "
description: "Oracle Oracle Create Table 시에 FK Constraint 감안한 Table 생성 순서 List 추출 프로그램 "
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/11/06/
---

> Oracle Create Table 시에 FK Constraint 감안한 Table 생성 순서 List 추출 프로그램 

### Oracle Create Table 시에 FK Constraint 감안한 Table 생성 순서 List 추출 프로그램  {#toc1}

```sql
DECLARE
    TYPE ARRAY_1 IS VARRAY(1000) OF VARCHAR2(100);
    V_LEVEL0 ARRAY_1 := ARRAY_1();
    V_INDEX0 NUMBER := 1;
    V_FIND VARCHAR2(200) := 'N';
    V_CNT NUMBER;
    V_USER VARCHAR2(100) := 'USER';
BEGIN
    FOR C IN (SELECT * FROM ALL_TABLES WHERE OWNER = V_USER AND TABLE_NAME LIKE '%TEST%') LOOP
        SELECT COUNT(*) INTO V_CNT FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = C.TABLE_NAME AND CONSTRAINT_TYPE = 'R';
        IF V_CNT = 0 THEN
            DBMS_OUTPUT.PUT_LINE(C.TABLE_NAME);
            V_LEVEL0.EXTEND;
            V_LEVEL0(V_INDEX0) := C.TABLE_NAME;
            V_INDEX0 := V_INDEX0 + 1;
        END IF;
    END LOOP;
    FOR C IN (SELECT * FROM ALL_TABLES WHERE OWNER = V_USER AND TABLE_NAME LIKE '%TEST%') LOOP
        SELECT COUNT(*) INTO V_CNT FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = C.TABLE_NAME AND CONSTRAINT_TYPE = 'R';
        IF V_CNT = 1 THEN
            IF V_LEVEL0.COUNT > 0 THEN
                FOR G IN 1..V_LEVEL0.COUNT LOOP
                    V_FIND := 'N';
                    IF V_LEVEL0(G) = C.TABLE_NAME THEN
                        V_FIND := 'Y';
                    END IF;
                END LOOP;
                IF V_FIND = 'Y' THEN
                    CONTINUE;
                END IF;
            END IF;
            FOR D IN (SELECT TABLE_NAME FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND CONSTRAINT_NAME IN (SELECT R_CONSTRAINT_NAME FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = C.TABLE_NAME AND CONSTRAINT_TYPE = 'R')) LOOP           
                V_FIND := 'N';
                FOR E IN 1..V_LEVEL0.COUNT LOOP
                    IF D.TABLE_NAME = V_LEVEL0(E) THEN
                        V_FIND := 'Y';
                    END IF;
                END LOOP;             
                IF V_FIND = 'N' THEN
                    FOR H IN (SELECT TABLE_NAME FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND CONSTRAINT_NAME IN (SELECT R_CONSTRAINT_NAME FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = D.TABLE_NAME AND CONSTRAINT_TYPE = 'R')) LOOP
                        V_FIND := 'N';
                        FOR E IN 1..V_LEVEL0.COUNT LOOP
                            IF H.TABLE_NAME = V_LEVEL0(E) THEN
                                V_FIND := 'Y';
                            END IF;
                        END LOOP;             
                        IF V_FIND = 'N' THEN
                            DBMS_OUTPUT.PUT_LINE(H.TABLE_NAME);
                            V_LEVEL0.EXTEND;
                            V_LEVEL0(V_INDEX0) := H.TABLE_NAME;
                            V_INDEX0 := V_INDEX0 + 1;
                        END IF;
                    END LOOP;
                    V_FIND := 'N';
                    FOR E IN 1..V_LEVEL0.COUNT LOOP
                        IF D.TABLE_NAME = V_LEVEL0(E) THEN
                            V_FIND := 'Y';
                        END IF;
                    END LOOP;             
                    IF V_FIND = 'N' THEN
                        DBMS_OUTPUT.PUT_LINE(D.TABLE_NAME);
                        V_LEVEL0.EXTEND;
                        V_LEVEL0(V_INDEX0) := D.TABLE_NAME;
                        V_INDEX0 := V_INDEX0 + 1;
                    END IF;
                END IF;
            END LOOP;
            V_FIND := 'N';
            FOR E IN 1..V_LEVEL0.COUNT LOOP
                IF C.TABLE_NAME = V_LEVEL0(E) THEN
                    V_FIND := 'Y';
                END IF;
            END LOOP;             
            IF V_FIND = 'N' THEN
                DBMS_OUTPUT.PUT_LINE(C.TABLE_NAME);
                V_LEVEL0.EXTEND;
                V_LEVEL0(V_INDEX0) := C.TABLE_NAME;
                V_INDEX0 := V_INDEX0 + 1;
            END IF;
        END IF;
    END LOOP; 
    FOR C IN (SELECT * FROM ALL_TABLES WHERE OWNER = V_USER AND TABLE_NAME LIKE '%TEST%') LOOP
        SELECT COUNT(*) INTO V_CNT FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = C.TABLE_NAME AND CONSTRAINT_TYPE = 'R';
        IF V_CNT = 2 THEN
            IF V_LEVEL0.COUNT > 0 THEN
                FOR G IN 1..V_LEVEL0.COUNT LOOP
                    V_FIND := 'N';
                    IF V_LEVEL0(G) = C.TABLE_NAME THEN
                        V_FIND := 'Y';
                    END IF;
                END LOOP;
                IF V_FIND = 'Y' THEN
                    CONTINUE;
                END IF;
            END IF;
            FOR D IN (SELECT TABLE_NAME FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND CONSTRAINT_NAME IN (SELECT R_CONSTRAINT_NAME FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = C.TABLE_NAME AND CONSTRAINT_TYPE = 'R')) LOOP
                V_FIND := 'N';
                FOR E IN 1..V_LEVEL0.COUNT LOOP
                    IF D.TABLE_NAME = V_LEVEL0(E) THEN
                        V_FIND := 'Y';
                    END IF;
                END LOOP;             
                IF V_FIND = 'N' THEN
                    SELECT COUNT(*) INTO V_CNT FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = D.TABLE_NAME AND CONSTRAINT_TYPE = 'R';
                    IF V_CNT <= 2 THEN
                        DBMS_OUTPUT.PUT_LINE(D.TABLE_NAME);
                        V_LEVEL0.EXTEND;
                        V_LEVEL0(V_INDEX0) := D.TABLE_NAME;
                        V_INDEX0 := V_INDEX0 + 1;
                    END IF;                
                END IF;
            END LOOP;
            V_FIND := 'N';
            FOR E IN 1..V_LEVEL0.COUNT LOOP
                IF C.TABLE_NAME = V_LEVEL0(E) THEN
                    V_FIND := 'Y';
                END IF;
            END LOOP;             
            IF V_FIND = 'N' THEN
                DBMS_OUTPUT.PUT_LINE(C.TABLE_NAME);
                V_LEVEL0.EXTEND;
                V_LEVEL0(V_INDEX0) := C.TABLE_NAME;
                V_INDEX0 := V_INDEX0 + 1;
            END IF;
        END IF;
    END LOOP;     
    FOR C IN (SELECT * FROM ALL_TABLES WHERE OWNER = V_USER AND TABLE_NAME LIKE '%TEST%') LOOP
        SELECT COUNT(*) INTO V_CNT FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = C.TABLE_NAME AND CONSTRAINT_TYPE = 'R';
        IF V_CNT = 3 THEN
            IF V_LEVEL0.COUNT > 0 THEN
                FOR G IN 1..V_LEVEL0.COUNT LOOP
                    V_FIND := 'N';
                    IF V_LEVEL0(G) = C.TABLE_NAME THEN
                        V_FIND := 'Y';
                    END IF;
                END LOOP;
                IF V_FIND = 'Y' THEN
                    CONTINUE;
                END IF;
            END IF;
            FOR D IN (SELECT TABLE_NAME FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND CONSTRAINT_NAME IN (SELECT R_CONSTRAINT_NAME FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = C.TABLE_NAME AND CONSTRAINT_TYPE = 'R')) LOOP
                V_FIND := 'N';
                FOR E IN 1..V_LEVEL0.COUNT LOOP
                    IF D.TABLE_NAME = V_LEVEL0(E) THEN
                        V_FIND := 'Y';
                    END IF;
                END LOOP;             
                IF V_FIND = 'N' THEN
                    SELECT COUNT(*) INTO V_CNT FROM ALL_CONSTRAINTS WHERE OWNER = V_USER AND TABLE_NAME = D.TABLE_NAME AND CONSTRAINT_TYPE = 'R';
                    IF V_CNT <= 3 THEN
                        DBMS_OUTPUT.PUT_LINE(D.TABLE_NAME);
                        V_LEVEL0.EXTEND;
                        V_LEVEL0(V_INDEX0) := D.TABLE_NAME;
                        V_INDEX0 := V_INDEX0 + 1;
                    END IF; 
                END IF;
            END LOOP;
            V_FIND := 'N';
            FOR E IN 1..V_LEVEL0.COUNT LOOP
                IF C.TABLE_NAME = V_LEVEL0(E) THEN
                    V_FIND := 'Y';
                END IF;
            END LOOP;             
            IF V_FIND = 'N' THEN
                DBMS_OUTPUT.PUT_LINE(C.TABLE_NAME);
                V_LEVEL0.EXTEND;
                V_LEVEL0(V_INDEX0) := C.TABLE_NAME;
                V_INDEX0 := V_INDEX0 + 1;
            END IF;
        END IF;
    END LOOP;        
END;
/
```

### 추출한 테이블 순서 대로 Table 생성문 생성해주는 프로그램 {#toc2}
```sql
column res Format a20000;
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM, 'PRETTY', TRUE);
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM, 'SQLTERMINATOR', TRUE); 
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM,'STORAGE', FALSE); 

DECLARE
    TYPE STR_LIST_TYPE IS TABLE OF VARCHAR2(1000);
    V_STR_VALUES STR_LIST_TYPE;    
    CNT NUMBER := 0;
    V_TC_ID VARCHAR(200) := '';
BEGIN
    V_STR_VALUES := STR_LIST_TYPE('T_TEST');
    FOR C IN V_STR_VALUES.FIRST..V_STR_VALUES.LAST LOOP
        FOR D IN (SELECT DBMS_METADATA.GET_DDL('TABLE',TABLE_NAME,'USER') as res from USER_TABLES WHERE TABLE_NAME = V_STR_VALUES(C)) LOOP
            DBMS_OUTPUT.PUT_LINE(D.RES);
        END LOOP;
    END LOOP;
END;
/
```

### Table 및 Index 삭제 {#toc3}
```sql
-- 내가 테이블 생성 스크립트를 뽑는 방식은 주로 DBMS_METADATA.GET_DDL 이라서 Constraints 정보가
-- Table 생성 문에 같이 들어가 있다.
-- Index 생성문 뽑을시에, PK, FK, UK 등등이 중복으로 에러 나지만 무시해도 된다. 이미 만든 오류 메세지니까..
select * from USER_TABLES;
select * from USER_INDEXES;


BEGIN
    FOR C IN (SELECT * FROM USER_TABLES) LOOP
        EXECUTE IMMEDIATE 'DROP TABLE '||C.TABLE_NAME||' CASCADE CONSTRAINTS';
    END LOOP;
    FOR C IN (SELECT * FROM USER_INDEXES) LOOP
        EXECUTE IMMEDIATE 'DROP INDEX '||C.INDEX_NAME;
    END LOOP;    
END;
/
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io