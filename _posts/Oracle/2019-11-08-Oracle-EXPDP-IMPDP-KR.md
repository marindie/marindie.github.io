---
toc: true
title: "Oracle: Oracle EXPDP IMPDP Example. ORA-14102 이슈 포함"
description: "Oracle Oracle EXPDP IMPDP Example. ORA-14102 이슈 포함"
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/11/08/
---

> Oracle EXPDP IMPDP Example. ORA-14102 이슈 포함

### EXPDP {#toc1}

```sql
mkdir /home/oracle/dump
sqlplus / as sysdba
GRANT CREATE ANY DIRECTORY TO POSMAST ;   
CREATE DIRECTORY data_pump AS '/home/oracle/dump' ; 
GRANT READ, WRITE ON DIRECTORY data_pump TO POSMAST ;  
GRANT EXP_FULL_DATABASE, IMP_FULL_DATABASE to POSMAST ;
exit
expdp USER/pwd@TEST directory=data_pump schemas=USER job_name=TEST_DUMP_01_EXP dumpfile=expdpTEST.dmp logfile=expdpTEST.log
```

### IMPDP {#toc2}
```sql
sqlplus / as sysdba
drop user USER cascade;
create user USER identified by pwd;
grant resource, connect, dba to USER;
exit
impdp USER/pwd@TEST directory=data_pump job_name=TEST_DUMP_01_IMP dumpfile=expdpTEST.dmp logfile=impdpTEST.log TRANSFORM=SEGMENT_ATTRIBUTES:N

-- REMAP_SCHEMA=POSMAST:POSMGMAST 스키마를 변경하고 싶을땐 이 옵션 사용
```

### ORA-14102: only one LOGGING or NOLOGGING clause may be specified {#toc3}
```md
해당 이슈는 LOGGING NOLOGGING 을 둘다 추출하는 11g 의 export 나 DBMS_METADATA 쪽의 에러인데,
import 할때 TRANSFORM=SEGMENT_ATTRIBUTES:N 옵션으로 우회 가능하기에 그렇게 사용중. 필요에 따라 추가적으로 LOGGING, NOLOGING 확인하고 변경을 해야할 수도
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io