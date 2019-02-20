---
toc: true
title: "Databases: Oracle SQLPLUS 에서 CSV 포맷 형태의 쿼리 조회 결과 출력"
description: "Oracle SQLPLUS 에서 쿼리 조회 결과를 CSV 포맷 형식으로 만드는 방법을 공유합니다."
categories: [Databases]
tags: [SQLPLUS,CSV]
redirect_from:
  - /2019/01/31/
---

> Oracle SQLPLUS 에서 쿼리 조회 결과를 CSV 포맷 형식으로 만드는 방법을 공유합니다.

# 배경지식

1. PL/SQL 에 대한 지식이 있다고 합니다.
2. 기본적은 SQL 지식이 있다고 가정합니다.
3. 일반적으로 CSV 형태의 Table Export 는 SQL DEVELOPER 에서 제공하는 기능을 사용해서 엑셀 이든 csv 파일이든 생성하곤 합니다.
   하지만 여러개의 테이블에 대하여 동적으로 동작하게 하기위해서는 SQL DEVELOPER 의 기능만으로는 한계가 있지요.
   해서 TABLE 명과 WHERE 조건절을 동적으로 받아서 해당 조회 결과를 CSV 형태로 (DELIMITER 는 변경 가능) 출력하는 방식을 고민하게 되었고, 
   제가 만들 결과물을 공유합니다.

# PL/SQL Block

```sql

ALTER SESSION SET NLS_DATE_FORMAT = 'YYYY-MM-DD HH24:MI:SS';
ALTER SESSION SET NLS_TIMESTAMP_FORMAT = 'YYYY-MM-DD HH24:MI:SS.FF';
SET SERVEROUTPUT ON
SET FEEDBACK OFF
DECLARE
    CUR NUMBER;
    RES NUMBER;
    CNT INTEGER;
    DESC_TAB    DBMS_SQL.DESC_TAB;
    V_VARCHAR VARCHAR2(32767);
    V_DATE DATE;
    V_TIMESTAMP TIMESTAMP;
    V_NUMBER NUMBER;
    COL_TYPE_NM VARCHAR2(300);
BEGIN
    DBMS_OUTPUT.ENABLE (BUFFER_SIZE => NULL);
    CUR := DBMS_SQL.OPEN_CURSOR;
    DBMS_SQL.PARSE(CUR, 'SELECT * FROM TEST_TABLE WHERE ROWNUM < 10', DBMS_SQL.NATIVE);
    DBMS_SQL.DESCRIBE_COLUMNS (CUR, CNT, DESC_TAB);
    FOR I IN 1 .. CNT LOOP
        DBMS_SQL.DEFINE_COLUMN(CUR, I, V_VARCHAR, 32767);
    END LOOP;        
    RES := DBMS_SQL.EXECUTE(CUR);
    LOOP
        IF DBMS_SQL.FETCH_ROWS(CUR)>0 THEN 
            FOR I IN 1 .. CNT LOOP
                DBMS_SQL.COLUMN_VALUE(CUR, I, V_VARCHAR);                    
                IF (I > 1) THEN
                    DBMS_OUTPUT.PUT('&'||V_VARCHAR);
                ELSIF (I = CNT OR I = 1) THEN
                    DBMS_OUTPUT.PUT(V_VARCHAR);
                END IF;
            END LOOP;  
                    DBMS_OUTPUT.NEW_LINE;
        ELSE
            EXIT;
        END IF;
    END LOOP;
END;
/

```

# 내용 설명

1. 먼저 동적으로 테이블 조회의 결과를 처리하기 위해서는, 컬럼이 몇개가 있던, 각 컬럼의 값을 불러올 수 있어야 했습니다.
2. 해서 이것저것 검색해보고 고민하던 중, 이녀석이 가장 적은 코딩라인으로 처리가 가능 할 것 같다고 생각이 들었습니다.
3. DBMS_SQL 은 OPEN_CURSOR 를 하고, 조회할 SQL문을 PARSE 를 하고, DESCRIBE_COLUMNS 로 컬럼 정보를 획득하고, 
   DEFINE_COLUMN 으로 해당 COLUMN 의 값을 저장할 변수명을 세팅을 하고, 
   COLUMN_VALUE 로 각 컬럼의 값을 해당 변수명에 세팅을 하는 방식으로 처리를 구현 할 수 있습니다.
4. DEFINE_COLUMN 과 COLUMN_VALUE 덕분에 일일이 해당 컬럼명을 하드코딩 하지 않고, 동적으로 각 조회된 RECORD 의 값들을 TEXT 형태로 뽑을 수 있게 되었습니다.
5. 첫번째 COLUMN과 마지막 COLUMN은 해당 값만을 출력하고, 나머지 COLUMN 에는 DELIMETER 값을 세팅하여 CSV 포멧 형태로 생성합니다.
6. 이 예제에서는 SQL 쿼리문과 WHERE 조건문이 하드코딩 되어 있지만, 이 부분은 동적으로 변경하도록 세팅이 가능하다는 것을 알고 계시면 됩니다.
7. 이 예제에서 DELIMETER 값은 '&' 입니다.

# 하지만, 출력 결과를 보게 되면, 너무 많은 빈 라인들이 만들어 져서 나오게 됩니다. 적어도 제가 테스트 한 결과는 그렇습니다.
# 그렇다면.. 방법이 없는걸가요? 여러가지를 시도해 본 결과, 아래의 방법으로 처리하게 되면 정상적으로 원하는 결과를 얻는것을 확인하였습니다.

# PL/SQL Block in bash

1. 동작 방식은, 일단 두개의 파라미터를 전달 받는 형태입니다. [ $1 $2 accordingly.. Ex) test.sh "test" "select * from test"]
2. 첫번째는 테이블 명이고, 두번째는 쿼리 정보입니다.
3. 전달 받은 내용을 바탕으로 쿼리가 돌아서 csv 형태의 결과물을 출력하고 해당 결과를 테이블명.csv 파일로 현재 폴더에 생성해 줍니다. 

```bash

#/bin/bash
SOURCE_DB="DPBAS_NEW"
S_USER="POSMGMAST"
S_PWD="dpbas1"

query=$2

for table in $1
do
OUTPUT=$(sqlplus -S $S_USER/$S_PWD@$SOURCE_DB << EOF > "./${table}.csv"
SET SERVEROUTPUT ON
SET FEEDBACK OFF
SET LINESIZE 20000
ALTER SESSION SET NLS_DATE_FORMAT = 'YYYY-MM-DD HH24:MI:SS';
ALTER SESSION SET NLS_TIMESTAMP_FORMAT = 'YYYY-MM-DD HH24:MI:SS.FF';
DECLARE
    CUR NUMBER;
    RES NUMBER;
    CNT INTEGER;
    DESC_TAB    DBMS_SQL.DESC_TAB;
    V_VARCHAR VARCHAR2(32767);
    V_DATE DATE;
    V_TIMESTAMP TIMESTAMP;
    V_NUMBER NUMBER;
    COL_TYPE_NM VARCHAR2(300);
BEGIN
    DBMS_OUTPUT.ENABLE (BUFFER_SIZE => NULL);
    CUR := DBMS_SQL.OPEN_CURSOR;
    DBMS_SQL.PARSE(CUR, '${query}', DBMS_SQL.NATIVE);
    DBMS_SQL.DESCRIBE_COLUMNS (CUR, CNT, DESC_TAB);
    FOR I IN 1 .. CNT LOOP
        DBMS_SQL.DEFINE_COLUMN(CUR, I, V_VARCHAR, 32767);
    END LOOP;        
    RES := DBMS_SQL.EXECUTE(CUR);
    LOOP
        IF DBMS_SQL.FETCH_ROWS(CUR)>0 THEN 
            FOR I IN 1 .. CNT LOOP
                DBMS_SQL.COLUMN_VALUE(CUR, I, V_VARCHAR);                    
                IF (I > 1) THEN
                    DBMS_OUTPUT.PUT('&'||V_VARCHAR);
                ELSIF (I = CNT OR I = 1) THEN
                    DBMS_OUTPUT.PUT(V_VARCHAR);
                END IF;
            END LOOP;  
                    DBMS_OUTPUT.NEW_LINE;
        ELSE
            EXIT;
        END IF;
    END LOOP;
END;
/
EOF
)

echo "SELECT ${table} DATA FINISHED"
done

```

도움이 되었으면 좋겠네요. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io