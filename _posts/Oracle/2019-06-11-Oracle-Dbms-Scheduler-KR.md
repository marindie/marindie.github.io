---
toc: true
title: "Oracle: Oracle DBMS_SCHEDULER Shell Script 실행"
description: "Oracle DBMS_SCHEDULER Shell Script 실행"
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/06/11/
---

> Oracle DBMS_SCHEDULER Shell Script 실행

### 사전 필요 권한 및 설정 {#toc1}

```sql
vi $ORACLE_HOME/rdbms/admin/externaljob.ora
run_user = oracle
run_group = oinstall
  
grant execute on sys.dbms_scheduler to SCOTT;
grant create job to SCOTT;
grant create external job to SCOTT;

```

### 등록시 순서 Program -> Schedule -> Job {#toc2}
```sql
BEGIN
    sys.dbms_scheduler.create_program(
        program_name => '"SCOTT"."TEST_SH_JOB"',
        program_action => '/home/oracle/test.sh',
        program_type => 'EXECUTABLE',
        number_of_arguments => 0,
        comments => 'This is to test a script run',
        enabled => FALSE);
    sys.DBMS_SCHEDULER.ENABLE(name=>'"SCOTT"."TEST_SH_JOB"');    
END;
/

BEGIN
    DBMS_SCHEDULER.CREATE_SCHEDULE(
    SCHEDULE_NAME => '"SCOTT"."TEST_SH_SCHED"',
    START_DATE => TRUNC(SYSDATE) + 1 + 9/24,
    END_DATE => NULL,
    REPEAT_INTERVAL => 'FREQ=DAILY; INTERVAL=1',
    COMMENTS => '매일아침 09시에 실행');
END;
/

BEGIN
    SYS.DBMS_SCHEDULER.CREATE_JOB (
            job_name => '"SCOTT"."TEST_SH_JOB_SCHED"',
            program_name => '"SCOTT"."TEST_SH_JOB"',
            start_date => NULL,
            repeat_interval => NULL,
            end_date => NULL,
            job_class => '"SYS"."DEFAULT_JOB_CLASS"',
            enabled => FALSE,
            auto_drop => FALSE,
            comments => '',
            job_style => 'REGULAR',
            credential_name => NULL,
            destination_name => NULL);       
     
    SYS.DBMS_SCHEDULER.SET_ATTRIBUTE(name => '"SCOTT"."TEST_SH_JOB_SCHED"',attribute => 'logging_level', value => DBMS_SCHEDULER.LOGGING_FULL);         
END; 
/
-- 로그 삭제 후 Manual 실행. enable 하면 run 이 실행되어 JOB 이 돈다.
EXEC DBMS_SCHEDULER.PURGE_LOG();
EXEC SYS.DBMS_SCHEDULER.enable(name => '"SCOTT"."TEST_SH_JOB_SCHED"');

-- 에러 관련 특이사항들
-- 모든 파일의 위치는 절대 경로여야 Schedule 에서 동작한다.
-- Ex. test.sh 안에 java -cp /home/oracle/test.jar:/home/oracle HelloWorld 형태로 넣어야 된다. HelloWorld 를 절대경로로 하면 에러남.
-- 폴더를 마춰줘서 package 명이랑 지지고 볶고 할수 있지만, 그냥 cp 에 HelloWorld 위치인 /home/oracle 잡아주고 HelloWorld 를 호출하는게
-- 더 편해서 그렇게 사용. 절대 경로 문제와 externaljob.ora 에 사용자 및 그룹 설정안한거 때문에 하루 보냈음.
-- 혹시나 shell 명령어 권한이 없다고 생각되는 분들을 위해 아래의 명령어 기록해둠
call dbms_java.grant_permission( 'SCOTT', 'SYS:java.io.FilePermission', '/bin/ps','execute' );
call dbms_java.grant_permission( 'SCOTT', 'SYS:java.io.FilePermission', '/bin/ls','execute' );
call dbms_java.grant_permission( 'SCOTT', 'SYS:java.io.FilePermission', '/bin/touch','execute' );
call dbms_java.grant_permission( 'SCOTT', 'SYS:java.io.FilePermission', '/bin/sh','execute' );
call dbms_java.grant_permission( 'SCOTT', 'SYS:java.io.FilePermission', '/usr/bin/java','execute' );

call dbms_java.grant_permission('SCOTT','java.io.FilePermission','/bin/sh','read');
call dbms_java.grant_permission('SCOTT','java.io.FilePermission','/bin/ps','execute');
call dbms_java.grant_permission('SCOTT','java.io.FilePermission','/bin/touch','read');

call dbms_java.grant_permission('SCOTT', 'java.util.PropertyPermission','*', 'read,write');

-- 잘 동작하는 지 확인 하는 쿼리
SELECT * FROM DBA_SCHEDULER_JOB_LOG WHERE JOB_NAME  LIKE 'TEST_SH_JOB%';
select *
from all_scheduler_job_run_details
where job_name = 'TEST_SH_JOB_SCHED'
order by log_id desc;
-- all_scheduler_job_run_details 테이블에서 additional_info 컬럼의 에러를 확인해야 그나마 에러를 잡을 수 있을것이다.

```

### 삭제시 순서 Job -> Schedule -> Program {#toc3}
```sql
exec sys.dbms_scheduler.drop_job(name=>'"SCOTT"."TEST_SH_JOB_SCHED"');
exec sys.dbms_scheduler.drop_schedule(schedule_name => '"SCOTT"."TEST_SH_JOB_SCHED"',force => false) ;
exec sys.dbms_scheduler.drop_program(program_name => '"SCOTT"."TEST_SH_JOB"',force => false) ;

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io