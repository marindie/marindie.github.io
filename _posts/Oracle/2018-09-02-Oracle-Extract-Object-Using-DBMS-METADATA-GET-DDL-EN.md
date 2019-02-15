---
toc: true
title: "Oracle Extract Object Using DBMS_METADATA.GET_DDL"
description: "In this page, I will share the sqlplus options and queries that I used to extract objects from existing database."
categories: [oracle]
tags: [oracle Data Dictionary, DBMS_METADATA]
redirect_from:
  - /2018/09/02/
---

> In this post, I will show you how to generate object creation statement using Oracle Data Dictionary. (Schema Level)

# Assumption & Background

1. You know ID and Password for the schema you want to extract
2. You have either `SQL PLUS` or `SQL DEVELOPER`

# Set System Variable

```sql

-- You May Not Like the Following Options.. So it's up to you
SET ECHO OFF         -- Show Each Command before execute it
SET TRIMOUT ON
SET TRIMSPOOL ON
SET TERM OFF

-- I highly recommand you to use the following options.
SET SERVEROUTPUT ON  -- Control whether to display output of PL/SQL
SET LINESIZE 30000   -- Sets the total number of characters that SQL*Plus displays on one line before beginning a new line.
SET HEADING OFF
SET FEEDBACK OFF
SET PAGESIZE 3000
-- Create a column whose name is 'res' and width is 20000 characters. 
-- The hidden Magic of this column is when you try to extract procedures or packages.
-- you name the result column of your extraction queries to 'res' then you will not face annoying issue where unexpected like break happended 
-- in your result sql. This res column will contain up to 20000 chracters unless it faces the real line break syntax during extraction process.
-- In other words, it will print exactly the same line the original procedures has in the database. I can gurantee that. Cheers~~!!
-- Just use my queries at the below, with these options. Then you will have no problems. except a couple of lines. 
-- Ex) After SPOOL command it prints everything you typed in, so the queries will be included in your result file.
column res Format a20000 

```

# Set DBMS_METADATA

```sql
-- Support Line Break. I do not understand exactly what types of line it supports 
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM, 'PRETTY', TRUE); though...
-- Put ';' character at the end of each command.
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM, 'SQLTERMINATOR', TRUE); 
-- Display Storage Info, Most of cases, I do not change those Info, so I don't want to see them.
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM,'STORAGE', FALSE); 
-- When you want to extract partitioned table or index, then you should set true to have those information.
-- But.. You may face 'SEGMENT CREATE IMMEDIATE' or 'SEGMENT CREATE DEFERRERD' issue.
-- What I'm saying is that when those statement included, the create statement may not work. So you may remove and re-run to see if it work I guess.
-- It will work then.. most of cases. But you have to remove each create statements in the file. See below to see how I used sed command to remove it.
EXEC DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM,'SEGMENT_ATTRIBUTES', TRUE); 

EXEC DBMS_OUTPUT.ENABLE(1000000);
```

# Extract Object Queries

```sql

-- I assume that you are reading this article, because you decided to recreate objects and insert data from production database.
-- And since the production data is so huge, You may not be able to use data pump or exp command to export the whole. Anyway, I hope this help. Let's move on.

-- Before I show you my sed command, see the following queries I used to generate statements for creating orcle objects.
-- Make sure you connected to correct database and schema(or user) that you want to extract from before run the following.
-- Since I give no path information in spool command, the result file will be created at the path you run sqlplus command
SPOOL INDEX_DDL.SQL
SELECT DBMS_METADATA.GET_DDL('INDEX',index_name) as res from user_indexes;
SPOOL OFF

SPOOL PROCEDURE_DDL.SQL
SELECT DBMS_METADATA.GET_DDL('PROCEDURE',name) as res from (select distinct name from user_source where type='PROCEDURE');
SPOOL OFF

SPOOL FUNCTION_DDL.SQL
SELECT DBMS_METADATA.GET_DDL('FUNCTION',name) as res  from (select distinct name from user_source where type='FUNCTION');
SPOOL OFF

SPOOL SEQUENCE_DDL.SQL
SELECT DBMS_METADATA.GET_DDL('SEQUENCE',SEQUENCE_NAME) as res  from USER_SEQUENCES;
SPOOL OFF

SPOOL TABLE_DDL.SQL
SELECT DBMS_METADATA.GET_DDL('TABLE',TABLE_NAME,'YOUR_USERNAME') as res from USER_TABLES;
SPOOL OFF

SPOOL SYNONYM_DDL.SQL
SELECT DBMS_METADATA.GET_DDL ('SYNONYM', SYNONYM_NAME, OWNER) as res FROM   DBA_SYNONYMS WHERE  TABLE_OWNER = 'YOUR_USERNAME';
SPOOL OFF

-- If there is a case where some users have a specific privileges on certains objects, use the following to give same privileges to those users.
SPOOL USER_TAB_PRIV_DCL.SQL
BEGIN
    FOR C IN (SELECT * FROM USER_TAB_PRIVS WHERE GRANTEE IN ('USER1','USER2','USER3') ORDER BY GRANTEE) LOOP
        DBMS_OUTPUT.PUT_LINE('GRANT '||C.PRIVILEGE||' ON '||C.GRANTOR||'.'||C.TABLE_NAME||' TO '||C.GRANTEE||';'); 
    END LOOP;
END;
/
SPOOL OFF

-- For me, these informaiton was enought, you may need more information depends on how complex your existing database. Cheers.

```

# Remove `SEGMENT CREATE IMMEDIATE` or `SEGMENT CREATE DEFERRERD` in the file

```bash

# Now I will share SED command that will remove SEGMENT CREATE stuff syntax in your results files
# In Linux, type this, to check there are some matching lines
sed -n -r -e "s/SEGMENT CREATE IMMEDIATE|SEGMENT CREATE DEFERRERD//p" *.SQL 
# Then type this, to actually remove those from the files
sed -i'' -r -e "s/SEGMENT CREATE IMMEDIATE|SEGMENT CREATE DEFERRERD//" *.SQL

# For Mac, make sure you installed good sed. If the following sed command not work, then enter "brew install gnu-sed" to install sed. Make sure you have a homebrew 
# Then you may have to use gsed since it prefix with g during installation
# To Check Matching Text run this
gsed -n -r -e "s/SEGMENT CREATE IMMEDIATE|SEGMENT CREATE DEFERRERD//gp" *.SQL 
# Then Run to remove the syntax
gsed -i'' -r -e "s/SEGMENT CREATE IMMEDIATE|SEGMENT CREATE DEFERRED//g" *.SQL

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io