---
toc: true
title: " Oracle SQL LOADER (sqlldr) Control File Generator"
description: "I will share my fairly Simple Oracle SQL LOADER Control File Generator in this post"
categories: [Oracle]
tags: [SQL LOADER,Sqlldr]
redirect_from:
  - /2019/02/20/
---

> I will share my fairly Simple Oracle SQL LOADER Control File Generator in this post

### Background

1. Basic SQL LOADER understanding is required.
2. SQL LOADER is a tool that ORACLE provide most commonly for INSERT DML.
3. It is faster than simple INSERT DML, but slower than Bulk Collect FORALL or DIRECT LOAD which uses Append hint. 
   It's my understanding. So it could be wrong in your circumstances.
4. The motive I made this program was because I wanted to synchronize a Table in DB A with DB B without using DB LINK.
5. There might be a tool for handling DB Synchronization or 3rd Party Software for it, but my case was not on DB level. Just a couple of tables only
6. I thought there might be a better way of doing it. but.. I just made it.

### PL/SQL Block in bash

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

### Explanation

1. First, I assume that insert folder is already made, move into the folder, and select META information of the target TABLE in TAB_LIST.
   Using selected information, I generated information that SQL LOADER need it for the INSERT DML
2. LOAD DATA means the name of ctl (Control file) for this run. (In this example TABLE_TEST1.ctl TABLE_TEST2.ctl would be the case)
3. INFILE means the name of data file for this run. (In this example TABLE_TEST1.DAT TABLE_TEST2.DAT would be the case.)
4. BADFILE, DISCARDFILE, DISCARDMAX are used to define ERROR CASES.
5. APPEND means table is not empty and insert after the exsiting data. If you change "APPEND" to "INSERT", then it means the target table is
   empty. If the target table is not empty when SQL LOADER try to insert, it will throw errors.
6. INSERT INTO TABLE_TEST1 is the insert DML, and FIELDS TERMINATED BY & is the DELIMETER being used between each column values.
7. TRAILING NULLCOLS used to avoid error case where SQL LOADER expect number of DELIMETER SYMBOL is same as
   the number of columns defined in control file. If they are not the same SQL LOADER throws error. But using TRAILING NULLCOLS, you can skip the error.
8. For example, if there were extra '&' in TABLE_TEST1.DAT file, SQL LOADER will load the data incorrectly only if the remaining data are
   loadable
9. PL/SQL BLOCK generate column information for each column's data type. I focused only on TIMESTAMP and DATE. The rest is treated as string.
   LOB type is ignored

### TABLE_TEST1.ctl

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

### TABLE_TEST1.DAT

1. I assumed that apart from DATE TIMESTAMP data type, all are STRING data type.
2. For LOB case, which exceeds over 4000 characters, simply put max char value in ctl file.
   Ex) COLUMN_NAME char(10000)
   for handling next line within string data, put "var" in INFILE option or use LOBFILE

```md

1&2003-07-14 15:52:43.000000&2019-02-11 13:11:57.536000
2&2003-07-14 15:52:43.000000&2019-02-11 13:11:57.536000
3&2003-07-14 15:52:43.000000&2019-02-11 13:11:57.536000
...

```

### READSIZE BINDSIZE ROWS

```md
When you actually use sqlldr, you may want to control number of rows to insert at each operation.
ROWS are the one you need to change number of rows per each load operation.

Then, you will face some physical size issue. READSIZE BINDSIZE are for those physical size change.
Oracle guide you to use BINDSIZE since when READSIZE is smaller than BINDSIZE, then READSIZE will become BINDSIZE.
Max is 20971520 (20MB)
Ex)
sqlldr test/test22@TEST control="./insert/TEST.ctl" log="./log/TEST.log" ROWS=1000 BINDSIZE =20971520 READSIZE =20971520 
```

### CSV Format file Generator

For those who need to create CSV format file like the above example, [Click Here](https://marindie.github.io/databases/Oracle-CSV-SQLPLUS-EN/)

Hope it was useful. Thanks

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
