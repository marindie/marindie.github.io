---
toc: true
title: " Oracle SQL LOADER (sqlldr) Control 파일 자동 생성 프로그램"
description: "Oracle SQL LOADER 에서 필요한 Control File 정보를 자동으로 생성해 주는 프로그램입니다."
categories: [Oracle]
tags: [SQL LOADER,Sqlldr]
redirect_from:
  - /2019/02/20/
---

> Oracle SQL LOADER 에서 필요한 Control File 정보를 자동으로 생성해 주는 프로그램입니다.

### 배경지식 {#toc1}

1. SQL LOADER 에 대한 기본 지식이 있다고 가정 합니다. 간략한 설명은 나옵니다.
2. SQL LOADER 는 ORACLE 에서 제공하는 INSERT TOOL 이라고 보시면 됩니다.
3. 단일 INSERT 문으로 처리하는 것보다는 빠르며, Bulk Collect FORALL 를 사용한 INSERT 나 APPEND hint 를 사용한 DIRECT LOAD 보다는
   느린것으로 생각됩니다.
4. 이게 필요한 계기는 A 라는 DB에서 B 라는 DB에 동일한 Table 정보를 동기화 하고자 하는데 DB LINK 를 사용하지 않고 처리 하고자 개발하게 되었습니다.
5. DB 동기화를 보장해주는 Tool 이나 3rd Party Software 를 사용하지 않고 처리하려다 보니 그렇게 되었네요.
6. 더 나은 방법이 있을것으로 생각이 됩니다만.. 일단은 만들어 보았습니다.

### PL/SQL Block in bash {#toc2}

```md

#/bin/bash

# Generate SQL Loader Controller Files
SOURCE_DB="ORCL"
S_USER="scott"
S_PWD="tiger"

FILE_PATH="./insert"

cd $FILE_PATH

TAB_LIST="TABLE_TEST1
TABLE_TEST2
TABLE_TEST3"

for table in $TAB_LIST
do
    TAB_NM=${table/TB_M00/MIG}
    echo ${TAB_NM} "START"
    echo "LOAD DATA" > "${TAB_NM}.ctl"
    echo "INFILE '$HOME/select/${TAB_NM}.DAT'" >> ${TAB_NM}.ctl
    echo "BADFILE ${TAB_NM}.bad" >> ${TAB_NM}.ctl
    echo "DISCARDFILE ${TAB_NM}.dis" >> ${TAB_NM}.ctl
    echo "DISCARDMAX 99999" >> ${TAB_NM}.ctl
    echo "APPEND" >> ${TAB_NM}.ctl
    echo "INTO TABLE ${TAB_NM}" >> ${TAB_NM}.ctl
    echo "FIELDS TERMINATED BY \"&\" " >> ${TAB_NM}.ctl
    echo "TRAILING NULLCOLS" >> ${TAB_NM}.ctl
    echo "(" >> ${TAB_NM}.ctl
OUTPUT=$(sqlplus -S $S_USER/$S_PWD@$SOURCE_DB << EOF
SET HEADING OFF
column RES Format a20000
SET SERVEROUTPUT ON
SET LINESIZE 20000
SET FEEDBACK OFF
SET TRIMS ON 
SPOOL TEMP.TXT
BEGIN
FOR C IN (SELECT TABLE_NAME, 
    CASE 
        WHEN INSTR(DATA_TYPE,'DATE') > 0 THEN 
            COLUMN_NAME||' DATE "YYYY-MM-DD HH24:MI:SS",'
        WHEN INSTR(DATA_TYPE,'TIMESTAMP') > 0 THEN 
            COLUMN_NAME||' TIMESTAMP "YYYY-MM-DD HH24:MI:SS.FF",' 
        ELSE 
            COLUMN_NAME||' NULLIF '||COLUMN_NAME||'=BLANKS,' 
    END AS RES FROM ALL_TAB_COLUMNS
WHERE OWNER = 'POSMAST'
AND TABLE_NAME = '$table'
ORDER BY TABLE_NAME, COLUMN_ID) LOOP
DBMS_OUTPUT.PUT_LINE(C.RES);
END LOOP;
END;
/
SPOOL OFF
EOF
)
    sed -i'' -e "s/.*PL\/SQL.*//" TEMP.TXT
    sed -i '/^\s*$/d' TEMP.TXT
    cat TEMP.TXT >> "${TAB_NM}.ctl" 
    sed -i "$ s/.$//" "${TAB_NM}.ctl"
    echo ")" >> "${TAB_NM}.ctl"
done
rm TEMP.TXT


```

### 내용 설명 {#toc3}

1. 먼저 insert 폴더가 있다고 가정하고 해당 폴더로 이동 후에, TAB_LIST 에 있는 TABLE 명에 대한 META 정보를 조회 하여 각 컬럼명의 DATA TYPE 별로
   SQL LOADER 가 필요로하는 정보를 생성합니다.
2. LOAD DATA 는 어떤 ctl (Control 파일) 을 읽어야 하는지 정의 합니다. (이 예제에서는 TABLE_TEST1.ctl TABLE_TEST2.ctl 파일이 해당 되겠습니다.)
3. INFILE 은 실제로 데이터가 있는 파일을 정의 합니다. (이 예제에서는 TABLE_TEST1.DAT TABLE_TEST2.DAT 파일이 해당 되겠습니다.)
4. BADFILE, DISCARDFILE, DISCARDMAX 는 에러 처리시 파일로 떨굴 녀석들을 정의 하고 최대 건수를 세팅하였습니다. 
5. APPEND 라고 명시를 하게 되면 실행시 해당 테이블의 내용을 삭제하지 않고 진행합니다. INSERT 라고 명시를 하게 되면, SQL LOADER 는 해당 테이블이
   EMPTY 라고 생각하고 처리를 시도합니다. 만약 해당 테이블이 EMPTY가 아니면, ERROR 를 뱉습니다.
6. INSERT INTO TABLE_TEST1 이라고 세팅하게 되고 FIELDS TERMINATED BY & 라고 명시하여, 각 컬럼별 DELIMETER 를 정의 합니다. 
7. TRAILING NULLCOLS 은 SQL LOADER 실행시, 해당 컬럼 갯수 만큼의 DELIMETER SYMBOL 갯수가 일치하지 않으면 에러를 뱉는데 그 에러를 무시하도록 세팅하는 
   OPTION 입니다. 데이터가 잘 못 들어게가 되는 부분에 대한 책임이 USER 에게 넘어오는 것으로 보시면 될 듯 합니다. 
8. 예를 들어, 어떤 컬럼에 '&' 값이 있었다면, SQL LOADER 파싱을 비정상적으로 하게 됩니다. 보통은 이런 경우에 에러가 나겠지만, 간혹 그 이후의 컬럼값들이
   정상적으로 처리가 가능한 CASE 라면 컬럼 값들이 한칸씩 밀려서 들어가 있을 것 같습니다. 테스트는 해보지 않았습니다.
9. PL/SQL BLOCK 은 각 컬럼별로 어떤 DATA TYPE 인지에 대한 정의를 동적으로 생성해 줍니다.

### TABLE_TEST1.ctl {#toc4}

1. DATE 타입과 TIMESTAMP 타입만 처리를 하고 나머지는 STRING 형태로 가정하고 처리하였습니다. 
2. LOB 와 같이 4000 이상의 길이를 처리 하기 위해서는 단순히 해당 컬럼에 max 값을 정의 하면 됩니다.
   Ex) COLUMN_NAME char(10000) 
   개행 처리를 위해서는 INFILE 에 "str '$\r\n'" 를 추가해 주거나 LOBFILE 을 사용하면 된다고 합니다.
   의미는 각 record string 의 맨 끝에 '$' 표시에 이어서 line break 가 매칭 되었을때, 
   ctl 에서의 record 하나로 인식해 달라는 뜻입니다.

```md

LOAD DATA
INFILE '/home/oracle/select/TABLE_TEST1.DAT'
BADFILE TABLE_TEST1.bad
DISCARDFILE TABLE_TEST1.dis
DISCARDMAX 99999
APPEND
INTO TABLE TABLE_TEST1
FIELDS TERMINATED BY "&"
TRAILING NULLCOLS
(
COLUMN_ID NULLIF DT_NM_ID=BLANKS,
CREATE_TIMESTAMP TIMESTAMP "YYYY-MM-DD HH24:MI:SS.FF",
CREATE_DT DATE "YYYY-MM-DD HH24:MI:SS"
)

```

### TABLE_TEST1.DAT {#toc5}

```md

1&2003-07-14 15:52:43.000000&2019-02-11 13:11:57.536000
2&2003-07-14 15:52:43.000000&2019-02-11 13:11:57.536000
3&2003-07-14 15:52:43.000000&2019-02-11 13:11:57.536000
...

```

### READSIZE BINDSIZE ROWS 설정 {#toc6}

```md
실제로 사용을 하다 보면 한꺼번에 넣는 ROWS 개수를 조절하고 싶어집니다.

그때 알게 되는 옵션이 READSIZE BINDSIZE 인데, Oracle 은 BINDSIZE만 정의하는것을 추천한다고 합니다.
이유는 READSIZE 값이 BINDSIZE 값 보다 작으면 자동으로 BINDSIZE 값으로 증가시켜서 사용하기 때문이라고 하네요.
20971520 가 최대 값입니다. (20MB)
Ex)
sqlldr test/test22@TEST control="./insert/TEST.ctl" log="./log/TEST.log" ROWS=1000 BINDSIZE =20971520 READSIZE =20971520 
```

### sqlldr 사용시 SQL*Loader-704: Internal error: ulconnect: OCIServerAttach 에러 처리 {#toc7}

```sql
-- 아래 쿼리로 조회해 보면, 두개 이상의 Characterset 이 보이게 된다.  Ex)KO16KSC5601, AL16UTF16
-- 내가 DB 를 설치하고 구성할때, 일부 Characterset 을 변경하지 않고 만들어서 그런거 같은데, 어디를 놓친건지 모르는 상황
SELECT DISTINCT (NLS_CHARSET_NAME (CHARSETID)) CHARACTERSET,
       DECODE (TYPE#,
	           1, DECODE(CHARSETFORM, 1, 'VARCHAR2', 2, 'NVARCHAR2', 'UNKOWN'),
			   9, DECODE(CHARSETFORM, 1, 'VARCHAR2', 2, 'NCHAR VARYING', 'UNKOWN'),
			   96, DECODE(CHARSETFORM, 1, 'CHAR', 2, 'NCHAR', 'UNKOWN'),
			   112, DECODE(CHARSETFORM, 1, 'CLOB', 2, 'NCLOB', 'UNKOWN'))
	   TYPES_USED_IN
FROM SYS.COL$
WHERE CHARSETFORM IN (1,2) AND TYPE# IN (1,9,96,112);

-- 일단 조취 방법은 아래와 같다
-- sqlplus / as sysdba
SHUTDOWN IMMEDIATE ;
STARTUP MOUNT ;
ALTER SYSTEM ENABLE RESTRICTED SESSION ;
ALTER SYSTEM SET JOB_QUEUE_PROCESSES=0 ;
ALTER SYSTEM SET AQ_TM_PROCESSES=0 ;
ALTER DATABASE OPEN ;
ALTER DATABASE CHARACTER SET KO16KSC5601 ; --변경하고자 하는 케릭터셋
SHUTDOWN IMMEDIATE;

```

### TABLE_TEST1.DAT 와 같은 CSV 형태의 파일 생성 관련 정보 {#toc7}

CSV 파일 형태로 쿼리 조회 결과를 출력해주는 프로그램 정보를 원하신다면

[클릭](https://marindie.github.io/databases/Oracle-CSV-SQLPLUS-KR/)


