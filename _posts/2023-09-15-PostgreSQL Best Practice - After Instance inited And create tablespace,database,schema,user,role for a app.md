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

(venv) [root@dev-tx-ops-dbops 15]# mkdir -p /var/lib/pgsql/15/data2
(venv) [root@dev-tx-ops-dbops 15]# chown -R postgres.postgres /var/lib/pgsql/15/data2/
su - postgres

postgres=# create tablespace tbs_app location '/var/lib/pgsql/15/data2';
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
\c appdb appuser 127.0.0.1 5432
Password for user appuser:
You are now connected to database "appdb" as user "appuser" on host "127.0.0.1" at port "5432".

appdb=> show search_path;
   search_path
-----------------
 "$user", public
(1 row)

appdb=> \dn+
                                        List of schemas
  Name   |       Owner       |           Access privileges            |      Description
---------+-------------------+----------------------------------------+------------------------
 appuser | appuser           |                                        |
 public  | pg_database_owner | pg_database_owner=UC/pg_database_owner+| standard public schema
         |                   | =U/pg_database_owner                   |
(2 rows)
```

Tips: Due to the parameter search_path <username> = <schema_name>

5.Create Role

```sql
postgres=# create role app_rw;
CREATE ROLE
```

6.Role Grants

```sql

postgres=# GRANT USAGE ON SCHEMA appschema TO app_rw;
GRANT

postgres=# grant select,update,insert,delete on all tables in schema appschema to app_rw;
GRANT
```

7.User Grant

```sql
postgres=# grant app_rw to appuser;
GRANT ROLE
```

8.Connect to appuser

```bash
postgres=# \c appdb appuser 127.0.0.1 5432
Password for user appuser:
You are now connected to database "appdb" as user "appuser" on host "127.0.0.1" at port "5432".
```

8.Create table test

```sql
appdb=> create table test01(id int ,name varchar(20));
CREATE TABLE
appdb=> \dt+
                                    List of relations
 Schema  |  Name  | Type  |  Owner  | Persistence | Access method |  Size   | Description
---------+--------+-------+---------+-------------+---------------+---------+-------------
 appuser | test01 | table | appuser | permanent   | heap          | 0 bytes |
(1 row)

appdb=> insert into test01 values (1,'Tom');
INSERT 0 1
appdb=> select * from test01;
 id | name
----+------
  1 | Tom
(1 row)
```