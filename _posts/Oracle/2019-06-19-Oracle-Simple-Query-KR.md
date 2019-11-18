---
toc: true
title: "Oracle: Oracle 쿼리 모음"
description: "Oracle Oracle 쿼리 모음"
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/06/19/
---

> Oracle 쿼리 모음

매번 검색하기 귀찮아서 검색한 녀석들 이곳에 모으기로 함.

### Tablespace 관련 {#toc1}

```sql
CREATE TABLESPACE oraTest DATAFILE '/ORACLE/product/11.2/orcl/tb_test.dbf' SIZE 100M AUTOEXTEND ON NEXT 10M;

SELECT TABLESPACE_NAME, STATUS, CONTENTS FROM DBA_TABLESPACES;
SELECT FILE_NAME, BYTES, STATUS FROM DBA_DATA_FILES;
SELECT TABLESPACE_NAME, BYTES, BLOCKS FROM DBA_FREE_SPACE;

SELECT   SUBSTR(A.TABLESPACE_NAME,1,30) TABLESPACE,
         ROUND(SUM(A.TOTAL1)/1024/1024,1) "TOTALMB",
         ROUND(SUM(A.TOTAL1)/1024/1024,1)-ROUND(SUM(A.SUM1)/1024/1024,1) "USEDMB",
         ROUND(SUM(A.SUM1)/1024/1024,1) "FREEMB",
         ROUND((ROUND(SUM(A.TOTAL1)/1024/1024,1)-ROUND(SUM(A.SUM1)/1024/1024,1))/ROUND(SUM(A.TOTAL1)/1024/1024,1)*100,2) "USED%"
FROM
         (SELECT   TABLESPACE_NAME,0 TOTAL1,SUM(BYTES) SUM1,MAX(BYTES) MAXB,COUNT(BYTES) CNT
          FROM     DBA_FREE_SPACE
          GROUP BY TABLESPACE_NAME
          UNION
          SELECT   TABLESPACE_NAME,SUM(BYTES) TOTAL1,0,0,0
          FROM     DBA_DATA_FILES
          GROUP BY TABLESPACE_NAME) A
GROUP BY A.TABLESPACE_NAME
ORDER BY TABLESPACE;
```

### 테이블 리스트 카운트 및 TOTAL {#toc2}

```sql
ALTER SESSION FORCE PARALLEL DML;
DECLARE
    CNT NUMBER;
    TOTAL NUMBER := 0;
BEGIN
    FOR C IN (SELECT * FROM ALL_TABLES WHERE OWNER = 'POSMAST' AND TABLE_NAME LIKE '%TB_M00%' AND TABLE_NAME NOT LIKE '%LOG%') LOOP
        EXECUTE IMMEDIATE 'SELECT COUNT(*) FROM '||C.TABLE_NAME INTO CNT;
            IF( CNT > 10000 ) THEN
                DBMS_OUTPUT.PUT_LINE(C.TABLE_NAME||' = '||CNT);
            END IF;
        TOTAL := TOTAL + CNT;
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('TOTAL = '||TOTAL);
END;
/
```

### Database Character Set {#toc3}
```sql
SELECT VALUE FROM NLS_DATABASE_PARAMETERS WHERE PARAMETER='NLS_CHARACTERSET';
ALTER DATABASE CHARACTER SET INTERNAL_USE WE8ISO8859P1;

-- CHARACTER SET 변경
-- ORA-12712: new character set must be a superset of old character set
UPDATE SYS.PROPS$ SET VALUE$='KO16KSC5601' WHERE NAME = 'NLS_CHARACTERSET';
SHUTDOWN IMMEDIATE;
STARTUP;
ALTER SYSTEM ENABLE RESTRICTED SESSION ;
ALTER SYSTEM SET JOB_QUEUE_PROCESSES=0 ;
ALTER SYSTEM SET AQ_TM_PROCESSES=0 ;
ALTER DATABASE CHARACTER SET KO16KSC5601;
SHUTDOWN IMMEDIATE;
STARTUP;

SELECT * FROM NLS_SESSION_PARAMETERS;
SELECT * FROM NLS_DATABASE_PARAMETERS;

```

### SPOOL 시 사용하는 Option 들 {#toc4}
```sql
SET ECHO OFF
SET TERM OFF
SET FEEDBACK OFF
SET SERVEROUTPUT ON
SET HEADING OFF
SET LINESIZE 30000
SET TRIMOUT ON
SET TRIMSPOOL ON
SET PAGESIZE 0
set long 2000000000
column res Format a20000
ALTER SESSION SET NLS_DATE_FORMAT = 'YY-MM-DD';

EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM, 'SQLTERMINATOR', TRUE);
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM, 'PRETTY', TRUE);
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM, 'TABLESPACE', TRUE);
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM, 'SEGMENT_ATTRIBUTES', TRUE);
EXECUTE DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM,'STORAGE',false);
```

### Oracle 삭제 방법 {#toc4}
```sql
su - oracle
$ORACLE_HOME/deinstall/deinstall

root 계정으로 /etc 폴더의 oraInst.loc / oratab 파일 삭제
root 계정으로 /usr/local/bin 폴더의 dbhome, oraenv, coraenv 파일 모두 삭제.
/tmp 폴더에 Ora로 시작하는 모든 디렉토리 삭제.
$ORACLE_HOME에 있는 폴더 모두 삭제.
환경 프로파일에 넣어준 부분 삭제. ( /etc/profile 또는 /home/oracle/.bash_profile )
```

### 사용자 추가 및 DBA 권한 부여 {#toc5}
```sql
CREATE USER NEW_USER IDENTIFIED BY newuser DEFAULT TABLESPACE USERS ;
GRANT CONNECT, RESOURCE, DBA TO NEW_USER;
```

### Oracle Partitioning 활성화 {#toc6}
```sql
select * from v$option where parameter = 'Partitioning';

PARAMETER VALUE
-------------------------------- ------------------- 
Partitioning TRUE

-- If partitioning is not enabled in the database then do the following step: 
-- 1. Shutdown all databases that use this ORACLE_HOME
-- 2. Run the following 

cd $ORACLE_HOME/rdbms/lib
make -f ins_rdbms.mk part_on
make -f ins_rdbms.mk ioracle
```

### Oracle Password Limit Off {#toc7}
```sql
select RESOURCE_NAME,RESOURCE_TYPE,LIMIT from dba_profiles where PROFILE='DEFAULT' and RESOURCE_NAME='PASSWORD_VERIFY_FUNCTION';
alter profile default limit PASSWORD_VERIFY_FUNCTION NULL;
```

### Add Partition SQL {#toc8}
```sql
ALTER TABLE TB_TEST
ADD PARTITION "PT_201807"  VALUES LESS THAN (TO_DATE(' 2018-08-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) 
PCTFREE 10 PCTUSED 40 INITRANS 1 MAXTRANS 255 
TABLESPACE "DATA02" NOCOMPRESS;
```

### Session Number Control {#toc9}
```sql

SHOW PARAMETER SESSIONS;
SHOW PARAMETER PROCESSES;
SHOW PARAMETER TRANSACTIONS;

ALTER SYSTEM SET PROCESSES=500 SCOPE=BOTH SID='*';
ALTER SYSTEM SET SESSIONS=555 SCOPE=BOTH SID='*';
ALTER SYSTEM SET TRANSACTIONS=610 SCOPE=BOTH SID='*';
```

### Lock 관련 {#toc10}
```sql
SELECT * FROM V$SESSION WHERE USERNAME = 'WONY';
-- 실제로 테이블 락 걸고 안해봐서 잘되는건지는 모르고 그냥 검색한거 기록함.

-- 락걸린 테이블 확인 
SELECT  DO.OBJECT_NAME, DO.OWNER, DO.OBJECT_TYPE, DO.OWNER,
        VO.XIDUSN, VO.SESSION_ID, VO.LOCKED_MODE
FROM    V$LOCKED_OBJECT VO, DBA_OBJECTS DO
WHERE   VO.OBJECT_ID = DO.OBJECT_ID;

-- 해당 테이블에 LOCK 이 걸렸는지.
SELECT A.SID, A.SERIAL#, B.TYPE, C.OBJECT_NAME
FROM V$SESSION A, V$LOCK B, DBA_OBJECTS C
WHERE A.SID = B.SID AND B.ID1 = C.OBJECT_ID
AND B.TYPE='TM' AND C.OBJECT_NAME IN ('WONY');

-- 락발생 사용자와 SQL, OBJECT 조회
SELECT DISTINCT X.SESSION_ID, A.SERIAL#, D.OBJECT_NAME, A.MACHINE, A.TERMINAL,
                A.PROGRAM, B.ADDRESS, B.PIECE, B.SQL_TEXT
FROM V$LOCKED_OBJECT X, V$SESSION A, V$SQLTEXT B, DBA_OBJECTS D
WHERE X.SESSION_ID = A.SID AND 
X.OBJECT_ID = D.OBJECT_ID AND A.SQL_ADDRESS = B.ADDRESS 
ORDER BY B.ADDRESS, B.PIECE;

-- 현재 접속자의 SQL 분석
SELECT DISTINCT A.SID, A.SERIAL#,
       A.MACHINE, A.TERMINAL, A.PROGRAM,
       B.ADDRESS, B.PIECE, B.SQL_TEXT
FROM  V$SESSION A, V$SQLTEXT B
WHERE A.SQL_ADDRESS = B.ADDRESS
ORDER BY A.SID, A.SERIAL#, B.ADDRESS, B.PIECE;

-- 락 세션 죽이기
  SELECT A.SID,   A.SERIAL#
  FROM V$SESSION A,  V$LOCK B,
       DBA_OBJECTS C
 WHERE A.SID = B.SID
   AND B.ID1 = C.OBJECT_ID
   AND B.TYPE = 'TM'
   AND C.OBJECT_NAME = 'WONY';
   
ALTER SYSTEM KILL SESSION 'SESSION_ID, SERIAL#';
```

### Date and Time Format 변경 {#toc11}
```sql
ALTER SESSION SET NLS_DATE_FORMAT = 'YYYY-MM-DD HH24:MI:SS';
ALTER SESSION SET NLS_TIMESTAMP_FORMAT = 'YYYY-MM-DD HH24:MI:SS.FF';
ALTER SESSION SET NLS_TIMESTAMP_TZ_FORMAT = 'YYYY-MM-DD HH:MI:SS.FF TZH:TZM'
```

### Hex Code 표시 방법 {#toc12}
```sql
select utl_raw.cast_to_raw('|'||chr(10)) from dual;              -- 유닉스에서 줄의종결표시는 chr(10), 특수문자표시는 | 라고 가정했을때
utl_raw.cast_to_row('|'||chr(10))
----------------------------------
7C0A                                                             -- STR = 7C0A

select utl_raw.cast_to_raw('|'||chr(13)||chr(10)) from dual;     -- 윈도우에서 줄의종결표시는 chr(10), 특수문자표시는 | 라고 가정했을때
UTL_RAW.CAST_TO_RAW('|'||CHR(13)||CHR(10))
-----------------------------------------------------------
7C0D0A                                                           -- STR = 7C0D0A
```

### PGA_AGGREGATE_LIMIT 설정 {#toc13}
```sql
alter system set pga_aggregate_limit = 0 -- 제한을 두지 않음
```

### Oracle Paging Query {#toc14}
```sql
SELECT * FROM 
(SELECT /*+ INDEX_DESC(Z TEST_PK) */ ROWNUM AS RNUM, A.* 
 FROM 
 (
    SELECT * FROM TEST
 ) A 
 WHERE ROWNUM <= 200
) 
WHERE RNUM >= 100;
```

### Oracle NLS_CHARACTERSET 확인 및 변경{#toc15}
```sql
--확인
SELECT * FROM NLS_DATABASE_PARAMETERS;
SELECT * FROM V$NLS_PARAMETERS;

--변경
sqlplus / as sysdba
SHUTDOWN IMMEDIATE ;
STARTUP MOUNT ;
-- 제한 모드 활성화 및 DBMS_JOB, Oracle Scheduler Job 실행 방지, Queue Monitor Coordinator 비활성화
ALTER SYSTEM ENABLE RESTRICTED SESSION ;
ALTER SYSTEM SET JOB_QUEUE_PROCESSES=0 ; -- show parameter job_queue_process
ALTER SYSTEM SET AQ_TM_PROCESSES=0 ;
ALTER DATABASE OPEN ;
ALTER DATABASE CHARACTER SET KO16KSC5601 ; -- 변경하고자 하는 케릭터셋 Ex. UTF8 KO16KSC5601
ALTER DATABASE NATIONAL CHARACTER SET KO16KSC5601;
-- 혹시 superset 이 틀리다는 오류가 나면 아래처럼 강제로 수정할 수 있다.
--ALTER DATABASE CHARACTER SET INTERNAL_USE KO16KSC5601;
SHUTDOWN IMMEDIATE;
STARTUP;

```

### Oracle DB LINK 생성 삭제 {#toc16}
```sql
-- DROP 하기 전에 조회했던 쿼리 결과들 닫아야 함. Ex) Jdeveloper 는 query result 같은거
CREATE DATABASE LINK TESDB CONNECT TO SCOTT TIGER BY TESDB USING 'TES';
DROP DATABASE LINK TESDB;

아래의 방법으로도 삭제 가능
-- sqlplus / as sysdba
-- SHUTDOWN IMMEDIATE
-- STARTUP RESTRICT
-- DROP DATABASE LINK ...;
-- ALTER SYSTEM DISABLE RESTRICTED SESSION
```

### Oracle Tablespace Datafile 사용량 조회 및 사이즈 재조정, Undotable space 삭제 방법 {#toc16}
```sql
SELECT  SUBSTR(DF.TABLESPACE_NAME,1,20) "Tablespace Name",
        SUBSTR(DF.FILE_NAME,1,80) "File Name",
        ROUND(DF.BYTES/1024/1024,0) "Size (M)",
        DECODE(E.USED_BYTES,NULL,0,ROUND(E.USED_BYTES/1024/1024,0)) "Used (M)",
        DECODE(F.FREE_BYTES,NULL,0,ROUND(F.FREE_BYTES/1024/1024,0)) "Free (M)",
        DECODE(E.USED_BYTES,NULL,0,ROUND((E.USED_BYTES/DF.BYTES)*100,0)) "% Used"
FROM    DBA_DATA_FILES DF,
       (SELECT FILE_ID,
               SUM(BYTES) USED_BYTES
        FROM DBA_EXTENTS
        GROUP BY FILE_ID) E,
       (SELECT SUM(BYTES) FREE_BYTES,
               FILE_ID
        FROM DBA_FREE_SPACE
        GROUP BY FILE_ID) F
WHERE    E.FILE_ID (+) = DF.FILE_ID
AND      DF.FILE_ID  = F.FILE_ID (+)
ORDER BY DF.TABLESPACE_NAME,
         DF.FILE_NAME;

alter database datafile '/ORACLE/oradata/TEST/ts_dat.dbf' resize 20M;
alter database datafile '/ORACLE/oradata/TEST/ts_idx.dbf' resize 20M;

         
--임시 undo tablespace 생성
create  undo tablespace undotbs2 datafile '/ORACLE/oradata/TEST/undotbs02.dbf' size 10m;
--임시로 만든 undotbs2 를 시스템 undo tablespace로 지정
alter system set undo_tablespace=undotbs2;
--기존에 비정상 적으로 확장된 undotbs1을 drop
drop tablespace undotbs1 including contents and datafiles;
--새로 사용할 undo tablespace 를 생성
create  undo tablespace undotbs1  datafile '/ORACLE/oradata/TEST/undotbs01.dbf' size 500m;
--새로 만든 undotbs1를 시스템 undo tablespace로 지정
alter system set undo_tablespace=undotbs1;
--임시로 만든 undotbs2를 drop
drop tablespace undotbs2 including contents and datafiles;
```

### Oracle INACTIVE Session 삭제 DBMS_SCHEDULER Job 등록 {#toc17}
```sql
-- 일단 이슈는 같은 row lock 현상으로 인해서 이렇게 처리하게 되었음.
-- 락 발생시, 세션을 일단 검색, EVENT 값을 보니 TX lock 발생
-- 관련 쿼리를 찾아서 SCHEDULER 에 SESSION KILL 하는 JOB 을 등록해서
-- 5초 단위로 돌리기로 맘 먹음.

SELECT SID,SERIAL#,USERNAME,STATUS,EVENT FROM V$SESSION WHERE USERNAME = 'POSMAST';
sqlplus / as sysdba
-- GRANT ALTER SYSTEM TO TESTUSER; 필요하면 사용
GRANT SELECT ON V_$TRANSACTION TO TESTUSER;
GRANT SELECT ON V_$SESSION TO TESTUSER;


ALTER SYSTEM SET JOB_QUEUE_PROCESSES=100;
-- 실행할 로직을 담은 PROCEDURE
CREATE OR REPLACE PROCEDURE POSMAST.KILL_INACTIVE_SESSIONS AS
BEGIN
  FOR CUR_REC IN (SELECT 'ALTER SYSTEM KILL SESSION ''' || SID || ',' || SERIAL# || '''' AS DDL
                       , S.PROCESS, S.MACHINE, S.TERMINAL, S.PROGRAM, S.MODULE
                       , TO_CHAR(S.LOGON_TIME,'DD/MON/YY HH24:MI:SS') LOGON_TIME
                    FROM V$TRANSACTION T, V$SESSION S
                    WHERE S.SADDR = T.SES_ADDR
                    ORDER BY START_TIME
                 )
  LOOP
    BEGIN
      EXECUTE IMMEDIATE CUR_REC.DDL;
    EXCEPTION
      WHEN OTHERS THEN
        -- YOU PROBABLY NEED TO LOG THIS ERROR PROPERLY HERE.
        -- I WILL JUST RE-RAISE IT.
        RAISE;
    END;
  END LOOP;
END;
/

-- DBMS_SCHEDULER 로  JOB 등록 5초 단위 실행
BEGIN
  DBMS_SCHEDULER.CREATE_JOB (
    JOB_NAME        => 'KILL_INACTIVESESSION_JOB',
    COMMENTS        => 'KILL OLD REPORTS IF THEY HAVE BEEN RUNNING FOR LONGER THAN 1 MINUTE.',
    JOB_TYPE        => 'PLSQL_BLOCK',
    JOB_ACTION      => 'BEGIN TESTUSER.KILL_INACTIVE_SESSIONS; END;',
    START_DATE      => SYSTIMESTAMP,
    REPEAT_INTERVAL => 'FREQ=SECONDLY;INTERVAL=5;',
    ENABLED         => TRUE);
END;
/

-- 등록 후에 체크 해보니 돌지를 않는다. 아래 내용 확인 및 조취 실행
SHOW PARAMETER JOB_QUEUE_PROCESSES; -- 0으로 되어 있음. 100 으로 늘려줬더니 돌기 시작
ALTER SYSTEM SET JOB_QUEUE_PROCESSES=100;

/*
  repeat_interval => 'freq=hourly ; interval=1'           <- 1시간 간격으로 수행하도록 설정
  repeat_interval => 'freq=minutely ; interval=30'        <- 30분 간격으로 수행하도록 설정
  repeat_interval => 'freq=secondly ; interval=5'         <- 5초 간격으로 수행하도록 설정     
  repeat_interval => 'freq=weekly ; interval=2'           <- 2주 간격으로 수행
  repeat_interval => 'freq=hourly ; interval=1'           <- 매달 수행하도록 설정
  repeat_interval => 'freq=hourly ; interval=1'           <- 매년 수행하도록 설정
  */

BEGIN DBMS_SCHEDULER.ENABLE('KILL_INACTIVESESSION_JOB'); END;
/
-- 수동 실행
BEGIN DBMS_SCHEDULER.RUN_JOB('KILL_INACTIVESESSION_JOB'); END;
/
BEGIN DBMS_SCHEDULER.DROP_JOB('KILL_INACTIVESESSION_JOB'); END;
/  

-- JOB 관련 확인 쿼리
ALTER SESSION SET NLS_DATE_FORMAT = 'YYYY-MM-DD HH24:MI:SS';
SELECT S.JOB_NAME, S.JOB_TYPE, O.OBJECT_ID, S.ENABLED, O.CREATED, CAST(S.NEXT_RUN_DATE AS DATE) NEXT_RUN_DATE, S.STATE, S.JOB_CLASS, SCHEDULE_TYPE
FROM  DBA_OBJECTS O
      , DBA_SCHEDULER_JOBS S
WHERE O.OBJECT_TYPE = 'JOB' ORDER BY CREATED DESC;
SELECT * FROM USER_SCHEDULER_JOBS;
SELECT * FROM USER_SCHEDULER_JOB_LOG ORDER BY LOG_DATE DESC;

```


[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io