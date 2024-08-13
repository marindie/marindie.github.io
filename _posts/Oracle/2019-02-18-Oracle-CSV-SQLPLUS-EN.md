---
toc: true
title: " SQL Query Output to CSV Format in Oracle SQLPLUS (Any Tables)"
description: "I will show you how to write CSV file like query outout using SQLPLUS"
categories: [Oracle]
tags: [SQLPLUS,CSV]
redirect_from:
  - /2019/02/18/
---

> I will show you how to write CSV file like query outout using SQLPLUS.

### Background

1. Basic PL/SQL Knowledge Requried
2. Basic SQL Knowledge Required
3. In general, users use sql developer IDE to extract query output into CSV Format File.
   But that does not mean it will generate data automatically. User have to conduct export step every time he/she needs it.
   So I tried to make dynamic csv format generator program, and this is the result.

### PL/SQL Block

```sql
SET SERVEROUTPUT ON SIZE UNLIMITED FORMAT WRAPPED
SET FEEDBACK OFF
SET LINESIZE 20000
SET PAGESIZE 0
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
    DBMS_SQL.PARSE(CUR, 'SELECT * FROM TEST', DBMS_SQL.NATIVE);
    DBMS_SQL.DESCRIBE_COLUMNS (CUR, CNT, DESC_TAB);
    FOR I IN 1 .. CNT LOOP
        DBMS_SQL.DEFINE_COLUMN(CUR, I, V_VARCHAR, 32767);
    END LOOP;        
    RES := DBMS_SQL.EXECUTE(CUR);
	WHILE DBMS_SQL.FETCH_ROWS(CUR)>0  
    LOOP
		FOR I IN 1 .. CNT LOOP
			DBMS_SQL.COLUMN_VALUE(CUR, I, V_VARCHAR);                    
			IF (I > 1) THEN
				DBMS_OUTPUT.PUT('&!'||V_VARCHAR);
			ELSIF (I = CNT OR I = 1) THEN
				DBMS_OUTPUT.PUT(V_VARCHAR);
			END IF;
		END LOOP;  
		DBMS_OUTPUT.PUT('$');
		DBMS_OUTPUT.NEW_LINE;
    END LOOP;
	DBMS_SQL.CLOSE_CURSOR (CUR); 
END;
/

```

### Explanation & Obstacles

1. The program should be able to handle columns types based on given user query
2. So far, this is the simplest one that I can make using PL/SQL Block
3. DBMS_SQL uses OPEN_CURSOR for target SQL Query, PARSE for run the Query, and DESCRIBE_COLUMNS for Column Meta Information. 
   DEFINE_COLUMN is used to set each column's value into variable, 
   COLUMN_VALUE is used to assign the real column data into the pre-defined variable.
4. By using DEFINE_COLUMN and COLUMN_VALUE, we can avoid hard-coded delimeter appending SQL every time we need it.
5. The first and last column print only the value, and the rest will print delemeter along with the value. So it becomes CSV format output.
6. The DELIMETER value is '&' in this example.
7. In this example, the SQL query is hard coded, but this part can be dynamic using shell script(bash). See below
8. However, when you check out the results, you would be disappointed by the mess. There are so many new empty lines. 
9. After that, I tried to solve this issue and the following code display the result in a way I expected.
10. The point is I used ">" command after EOF so that the result can be saved as a file. In that way I was able to print the query 
    result properly without line breaks.
11. FORMAT WRAPPED preserve whitespace of query result. Ex) ' &!aa&1'
12. This example assume that Each end of line is terminated by $ and each Field data is seperated by &!
13. Beaware that PGA size should be enough to load data before write into a file.

### PL/SQL Block in bash

1. This bash script assumes that user will pass two parameters. [ $1 $2 accordingly.. Ex) test.sh "test" "select * from test"]
2. First parameter is table name. Second parameter is SQL query.
3. Now it will generate csv file using given table name in current folder. "./${table}.csv Ex) ./test.csv"
4. That's it.

```md

#/bin/bash
SOURCE_DB="TEST"
S_USER="TEST"
S_PWD="test"

query=$2

for table in $1
do
OUTPUT=$(sqlplus -S $S_USER/$S_PWD@$SOURCE_DB << EOF > "./${table}.csv"
SET SERVEROUTPUT ON SIZE UNLIMITED
SET FEEDBACK OFF
SET LINESIZE 20000
SET PAGESIZE 0
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
	WHILE DBMS_SQL.FETCH_ROWS(CUR)>0  
    LOOP
		FOR I IN 1 .. CNT LOOP
			DBMS_SQL.COLUMN_VALUE(CUR, I, V_VARCHAR);                    
			IF (I > 1) THEN
				DBMS_OUTPUT.PUT('&!'||V_VARCHAR);
			ELSIF (I = CNT OR I = 1) THEN
				DBMS_OUTPUT.PUT(V_VARCHAR);
			END IF;
		END LOOP;  
		DBMS_OUTPUT.PUT('$');
		DBMS_OUTPUT.NEW_LINE;
    END LOOP;
	DBMS_SQL.CLOSE_CURSOR (CUR); 
END;
/
EOF
)

echo "SELECT ${table} DATA FINISHED"
done

```

Hope you Like it. Thanks


