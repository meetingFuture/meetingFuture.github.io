---
title: PostgreSQL Best Practice - After Instance inited And create tablespace,database,schema,user,role for a app
tags:
  - Best Practice
  - PostgreSQL
  - Initial
categories:
  - Database
  - PostgreSQL
---
Summary

1.Oraclelike management style，a app service own it’s user/schema/database/tablespace/priveliges.

2.Not object in public schema or pg_default tablespace.

3.Minimized permission configuration.

This is the Best Practices according my experience.

1.Create User

```sql
postgres=# create user appuser password 'appuser';
CREATE ROLE
```

2.Create Tablespace

```sql
mkdir c:\pgapp

postgres=# create tablespace tbs_app location 'd:\pgapp';
CREATE TABLESPACE
```

3.Create Database

```sql
postgres=# create database appdb owner appuser tablespace tbs_app template template0 encoding 'UTF8' lc_ctype 'en_US.utf8' lc_collate 'C';
CREATE DATABASE
```

Tips：

owner/tablespace/encoding/lc_collate is necessory!

4.Create Schema

```sql
postgres=# create schema appschema;
CREATE SCHEMA
```

5.Create Role

```sql
postgres=# create role app_rw;
CREATE ROLE
```

6.Role Grants

```sql
postgres=# grant select,update,insert,delete on all tables in schema appschema to app_rw;
GRANT
```

7.User Grant

```sql
postgres=# grant app_rw to appuser;
GRANT ROLE
```

8.Create table test

```sql
appdb=> create table test01(id int ,name varchar(20));
CREATE TABLE
appdb=> \d
               关联列表
 架构模式 |  名称  |  类型  | 拥有者
----------+--------+--------+---------
 public   | test01 | 数据表 | appuser
(1 行记录)

appdb=> insert into test01 values (1,'zhangfei');
INSERT 0 1
appdb=> select * from test01;
 id |   name
----+----------
  1 | zhangfei
(1 行记录)
```