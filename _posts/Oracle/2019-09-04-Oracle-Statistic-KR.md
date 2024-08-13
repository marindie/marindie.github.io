---
toc: true
title: " Oracle Statistic 관련 "
description: "Oracle Oracle Statistic 관련 "
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/09/04/
---

> Oracle Statistic 관련

### Oracle Statistic Pending {#toc1}

```md
https://docs.oracle.com/cd/B28359_01/appdev.111/b28419/d_stats.htm#i1049486  내용 참고
통계 정보를 모을때, 새로 모은 통계 정보가 plan 에 악영향을 준다고 생각이 되면,
테스트 후에 적용이 되도록 하는 방법이 있다고 한다.
```

```sql
-- 아래의 PUBLISH false 는 통계정보의 즉각 update 를 방지한다. 
EXEC DBMS_STATS.SET_TABLE_PREFS('hr', 'employees', 'PUBLISH', 'false');
-- 통계정보 수집
EXEC DBMS_STATS.GATHER_TABLE_STATS ('hr', 'employees');
-- To test the newly gathered statistics, set optimizer_pending_statistics to TRUE in a session and run sample queries.
ALTER SESSION SET optimizer_use_pending_statistics = TRUE;
select * from test_stat;
--맘에 들면 반영
EXEC DBMS_STATS.PUBLISH_PENDING_STATS('hr', 'employees');
EXEC DBMS_STATS.SET_TABLE_PREF('hr', 'employees', 'PUBLISH', 'true'); 

-- 별개로 통계 정보 수집할 때, 테이블에 parallel 방식으로 진행하여 약간의 속도를 올릴수 있다.
EXEC DBMS_STATS.SET_TABLE_PREFS('TEST', C.TABLE_NAME, 'DEGREE', DBMS_STATS.AUTO_DEGREE);
EXEC DBMS_STATS.SET_GLOBAL_PREFS('DEGREE', DBMS_STATS.AUTO_DEGREE);

SET SERVEROUTPUT ON
BEGIN
    FOR C IN (SELECT * FROM ALL_TABLES WHERE OWNER = 'WONY') LOOP
        DBMS_STATS.SET_TABLE_PREFS('WONY', C.TABLE_NAME, 'DEGREE', DBMS_STATS.AUTO_DEGREE);
        DBMS_STATS.GATHER_TABLE_STATS('WONY',C.TABLE_NAME);
        DBMS_OUTPUT.PUT_LINE(C.TABLE_NAME||' GATHERING STATISTICS IS DONE.');
    END LOOP
END;
/

-- Schema Level
EXEC DBMS_STATS.SET_SCHEMA_PREFS('TEST', 'DEGREE','16');
EXEC DBMS_STATS.SET_SCHEMA_PREFS('TEST', 'DEGREE',DBMS_STATS.AUTO_DEGREE);
EXEC DBMS_STATS.GATHER_SCHEMA_STATS('TEST',);
```

### 내가 사용하는 쿼리 {#toc2}

```sql
-- USER 라고만 적으면 현재 접속한 사용자로 ORACLE 은 인식한다. 'TEST'
SET SERVEROUTPUT ON
EXEC DBMS_STATS.SET_SCHEMA_PREFS(USER, 'DEGREE','16');
EXECUTE DBMS_STATS.GATHER_SCHEMA_STATS(OWNNAME => USER);
SELECT LAST_ANALYZED FROM USER_TABLES;
BEGIN
    FOR C IN (SELECT * FROM ALL_TABLES WHERE OWNER = 'TEST') LOOP
        DBMS_STATS.SET_TABLE_PREFS('WONY',C.TABLE_NAME,'DEGREE', '16');
        DBMS_STATS.GATHER_TABLE_STATS(USER,C.TABLE_NAME,METHOD_OPT =>'FOR ALL INDEXED COLUMNS',CASCADE => TRUE);
        DBMS_OUTPUT.PUT_LINE(C.TABLE_NAME||' GATHERING STATISTICS IS DONE.');
    END LOOP;
END;
/ 
```


