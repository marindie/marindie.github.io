---
toc: true
title: " Dynamic Bulk Collect Forall Delete Auto generator (Table 단위 Bulk Collect Forall Delete 자동생성기) "
description: "Dynamic Bulk Collect Forall Delete Auto generator (Table 단위 Bulk Collect Forall Delete 자동생성기)"
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/09/05/
---

> Dynamic Bulk Collect Forall Delete Auto generator (Table 단위 Bulk Collect Forall Delete 자동생성기)

### Auto Generator PL/SQL Block {#toc1}

```md
원하는 Schema 의 테이블들을 대상으로 하여
Truncate 가 아닌 Delete 를 사용하여 처리 해야만 하는 제약이 있어
Bulk Collect Forall Delete 를 만들어 사용하게 되었고,
전체 Table에 대하여 자동생성해주는 PL/SQL 을 만들었다.
에러 테스트는 안해서, Exception 관련 부분은 정상 동작 보증 못함
```

```sql
DECLARE
    L_SQL VARCHAR2(4000);
    V_USER VARCHAR2(20) := 'TEST';
BEGIN
    FOR C IN (SELECT * FROM ALL_TABLES WHERE OWNER = V_USER AND TABLE_NAME LIKE '%TB_M00%' AND TABLE_NAME NOT LIKE '%LOG%') LOOP
        L_SQL := 'DECLARE '||
                 '   CURSOR C1 IS '||
                 '   SELECT * '||
                 '   FROM   '||C.TABLE_NAME||'; '||
                 '   TYPE TABLE_TYPE IS TABLE OF C1%ROWTYPE INDEX BY PLS_INTEGER; '||
                 '   REC_C1 TABLE_TYPE; '||
                 '   V_LIMIT NUMBER := 1000; '||
                 'BEGIN '||
                 '   OPEN C1; '||
                 '   LOOP '||
                 '   FETCH C1 BULK COLLECT INTO REC_C1 LIMIT V_LIMIT; '||
                 '   FORALL I IN REC_C1.FIRST..REC_C1.LAST '||
                 '      DELETE FROM '||C.TABLE_NAME||' WHERE ';
        FOR D IN (SELECT * FROM ALL_CONS_COLUMNS WHERE OWNER = V_USER 
                    AND CONSTRAINT_NAME = (SELECT CONSTRAINT_NAME FROM ALL_CONSTRAINTS WHERE OWNER = V_USER
                                            AND TABLE_NAME = C.TABLE_NAME
                                            AND CONSTRAINT_TYPE = 'P') ORDER BY POSITION) LOOP
                L_SQL := L_SQL||D.COLUMN_NAME||' = REC_C1(I).'||D.COLUMN_NAME||' AND ';
        END LOOP;
            L_SQL := SUBSTR(L_SQL,1,LENGTH(L_SQL)-4)||';';
            L_SQL := L_SQL||'   EXIT WHEN REC_C1.COUNT < V_LIMIT; '||
                 '   END LOOP; '||
                 '   CLOSE C1; '||
                 '   EXCEPTION '||
                 '      WHEN OTHERS THEN '||
                 '          DBMS_OUTPUT.put_line (SQLERRM); '||
                 '          DBMS_OUTPUT.put_line (''DELETED ''||SQL%ROWCOUNT||'' ROWS.''); '||
                 '          FOR INDX IN 1 .. SQL%BULK_EXCEPTIONS.COUNT LOOP '||
                 '              DBMS_OUTPUT.PUT_LINE (''ERROR ''||INDX||'' OCCURRED ON INDEX ''||SQL%BULK_EXCEPTIONS (INDX).ERROR_INDEX||'' ATTEMPTING TO UPDATE NAME TO "''||SQL%BULK_EXCEPTIONS (INDX).ERROR_INDEX||''"''); '||
                 '              DBMS_OUTPUT.PUT_LINE (''ORACLE ERROR IS ''||SQLERRM(-1 * SQL%BULK_EXCEPTIONS (INDX).ERROR_CODE)); '||
                 '          END LOOP; '||
                 'END; ';
        DBMS_OUTPUT.PUT_LINE(L_SQL);          
        --EXECUTE IMMEDIATE L_SQL;
    END LOOP;
END;
/
```

### Sample Bulk Collect Forall Delete {#toc2}
```sql
-- 자동생성기에는 맨 마지막 / slash 가 없습니다. 개별로 사용하게 되면 / 는 붙여줘야 합니다.
-- 이거 STRING 으로 하면 에러가 나서.. 일단 없앰..
-- EXECUTE 로 전체 돌리는데에는 문제는 없이 돌아갈테지만.. DBMS_OUTPUT 으로 뽑아서 사용시에는 / 를 각가 별도로 붙여줘야하는 단점이 있음..
DECLARE    
    CURSOR C1 IS    
    SELECT *    
    FROM   TEST;    
    TYPE TABLE_TYPE IS TABLE OF C1%ROWTYPE INDEX BY PLS_INTEGER;    
    REC_C1 TABLE_TYPE;    
    V_LIMIT NUMBER := 1000; 
BEGIN    
    OPEN C1;    
    LOOP    
        FETCH C1 BULK COLLECT INTO REC_C1 LIMIT V_LIMIT; 
        FORALL I IN REC_C1.FIRST..REC_C1.LAST       
            DELETE FROM TEST WHERE COL1 = REC_C1(I).COL1 AND COL2 = REC_C1(I).COL2;
            EXIT WHEN REC_C1.COUNT < V_LIMIT;    
        END LOOP;    
    CLOSE C1;    
    EXCEPTION       
        WHEN OTHERS THEN           
            DBMS_OUTPUT.put_line (SQLERRM);           
            DBMS_OUTPUT.put_line ('DELETED '||SQL%ROWCOUNT||' ROWS.');           
            FOR INDX IN 1 .. SQL%BULK_EXCEPTIONS.COUNT LOOP               
                DBMS_OUTPUT.PUT_LINE ('ERROR '||INDX||' OCCURRED ON INDEX '||SQL%BULK_EXCEPTIONS (INDX).ERROR_INDEX||' ATTEMPTING TO UPDATE NAME TO "'||SQL%BULK_EXCEPTIONS (INDX).ERROR_INDEX||'"');               
                DBMS_OUTPUT.PUT_LINE ('ORACLE ERROR IS '||SQLERRM(-1 * SQL%BULK_EXCEPTIONS (INDX).ERROR_CODE));           
            END LOOP; 
END; 
/
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io