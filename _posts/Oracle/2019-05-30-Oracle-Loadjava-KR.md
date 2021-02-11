---
toc: true
title: " Oracle loadjava 사용법"
description: "Oracle loadjava 사용법"
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/05/30/
---

> Oracle loadjava 사용법

### Loadjava 설명 중요함!! {#toc1}

```md
명령어 예시 => loadjava -u scott/tiger@ORCL -verbose -resolve OracleJDBCExample.java

loadjava 명령어를 사용하면 Oracle 은 OS JVM 이 아닌
Oracle DB 내부적으로 사용하는 JVM 을 사용합니다. 11g 는 기본 1.5 로 세팅 되어 있으며, 변경하는 방법은
저는 모릅니다. 검색해도 안나오더라구요. DBA 말로는 18c 에서는 JVM 이 설치되어 있지 않고 명령어로 설치를 해야 한다고하네요.

이것저것 해보니 안되는 상황이 좀 발생했습니다..
Hello World 수준은 당연히 잘 됩니다.

java, class, jar, zip 타입을 허용합니다.

java 파일내에서 사용하려는 함수는 무조건 Static 으로 정의 되어야 합니다.
이유는 Class명.함수명으로 호출해서 쓰기때문입니다. 

의존성 체크 없이 일단 loadjava 성공시키고 호출 시에 runtime 에러 체크를 하고자 하는경우에,
-resolve 옵션을 빼면 됩니다. 아마 jar 들을 넣을 때에는 그렇게 넣어야 할것 같아보이네요.

class 파일은 Oracle Javac 명령어로 컴파일 통과 한 녀석들만 load 가 되는것 같습니다.
OS javac 로 만들어진 녀석은 버전 이슈가 있을 경우, 안될 수 있습니다.

jar 파일을 zip 으로 묶어서 loadjava 하면 의존성 체크 안하고 전부다 JAVA RESOURCE 로
밀어 넣습니다. 근데 이녀석들을 참조하려고하면 안되는 것 같은 느낌이 드네요..

문제는 여러 Jar 파일의 소스를 참조하는 현실 코딩 상황에서 상당히 문제가 많습니다.
문제가 뭐냐하면
1. 참조 하는 JAR 를 loadjava 로 올려야 함
2. loadjava 로 jar 를 올리면 이녀석은 안의 class 파일을 일일이 다시 Oracle JVM 으로 compile 체크함
3. JVM 버전이 안맞으면 에러 발생
4. 관련 있는 jar 들을 한곳에 모아서 loadjava -u scott/tiger@ORCL -verbose *.jar 로 올리고 실행시에 에러 나는
   부분을 봐야 할 것 같습니다.

제가 정말 부득이 하게 개발을 Oracle 에서 해야 한다면, 저는 4번의 방법으로 시도하려 할 것입니다.
일단은 사용법만 적어 놓고 도전은 미뤘습니다. ㅋㅋ
```

### JVM 생성 관련 스크립트 파일 실행 명령어 {#toc11}

```sql
SQL>@$ORACLE_HOME/javavm/install/initjvm.sql
SQL>@$ORACLE_HOME/xdk/admin/initxml.sql
SQL>@$ORACLE_HOME/xdk/admini/xmljs.sql
SQL>@$ORACLE_HOME/rdbms/admin/catjava.sql
```

### 필요 권한 부여 {#toc2}

```sql
call dbms_java.grant_permission('USERNAME', 'java.util.PropertyPermission','*', 'read,write');
execute dbms_java.grant_permission('USERNAME','java.util.PropertyPermission','*','read');
execute dbms_java.grant_permission( 'USERNAME', 'SYS:java.lang.RuntimePermission', 'getClassLoader', ' ' );
execute dbms_java.grant_permission( 'USERNAME', 'SYS:oracle.aurora.security.JServerPermission', 'Verifier', ' ' );
execute dbms_java.grant_permission( 'USERNAME', 'SYS:java.lang.RuntimePermission', 'accessClassInPackage.sun.util.calendar', ' ' ) ; 
execute dbms_java.grant_permission( 'USERNAME', 'java.net.SocketPermission', '*', 'connect,resolve' );
execute dbms_java.grant_permission( 'USERNAME', 'SYS:java.lang.RuntimePermission', 'createClassLoader', ' ');
```

### loadjava 실행문 예시 {#toc3}

```md
loadjava -u scott/tiger@ORCL -verbose -resolve OracleJDBCExample.java
loadjava -u scott/tiger@ORCL -verbose -resolve OracleJDBCExample.class
loadjava -u scott/tiger@ORCL -verbose -resolve jarfile.zip
loadjava -u scott/tiger@ORCL -verbose -resolve test.jar
```

### loadjava 로 올라간 객체 상태 확인 {#toc4}

```sql
SELECT OBJECT_NAME, OBJECT_TYPE, STATUS
FROM USER_OBJECTS
WHERE OBJECT_TYPE LIKE '%JAVA%'
AND OBJECT_NAME = 'OracleJDBCExample';
```

### 함수 호출 관련 샘플 {#toc5}

```sql
CREATE OR REPLACE FUNCTION OracleTest (str VARCHAR2)
RETURN VARCHAR AS
    language java name 'OracleJDBCExample.conDb(java.lang.String)
    return java.lang.String';   
/

SELECT OracleTest('Run Test') STR
FROM dual;

--필요에 따라 java 파일을 JDeveloper 와 같은 IDE 명령창에서 바로 만들 수도 있는데 
--아래 샘플 참고
-- 다시한번 얘기하지만, 함수명은 static 이어야 합니다.
CREATE OR REPLACE and RESOLVE JAVA SOURCE NAMED "HelloWorld" AS /*파일명*/

    import java.lang.*;
    import java.io.*;
    
    public class HelloWorld { /*클래스명*/
        public static String run(String str) {  /*함수*/
            byte test_type = 0; 
            
            try {
                long stTime = System.currentTimeMillis();
                long endTime = System.currentTimeMillis();
                return "HelloWorld";
            } catch(Exception e) {
                System.out.println(e.getMessage() + "bye");
                e.printStackTrace();
            }
        }
    };
/

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
