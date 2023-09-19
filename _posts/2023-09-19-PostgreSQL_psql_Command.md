---
title: PostgreSQL_psql_Command
tags:
  - Basic
  - PostgreSQL
  - psql
categories:
  - Database
  - PostgreSQL
---
# Show Help \h

```sql
appdb=> \h create database
Command:     CREATE DATABASE
Description: create a new database
Syntax:
CREATE DATABASE name
    [ WITH ] [ OWNER [=] user_name ]
           [ TEMPLATE [=] template ]
           [ ENCODING [=] encoding ]
           [ STRATEGY [=] strategy ] ]
           [ LOCALE [=] locale ]
           [ LC_COLLATE [=] lc_collate ]
           [ LC_CTYPE [=] lc_ctype ]
           [ ICU_LOCALE [=] icu_locale ]
           [ LOCALE_PROVIDER [=] locale_provider ]
           [ COLLATION_VERSION = collation_version ]
           [ TABLESPACE [=] tablespace_name ]
           [ ALLOW_CONNECTIONS [=] allowconn ]
           [ CONNECTION LIMIT [=] connlimit ]
           [ IS_TEMPLATE [=] istemplate ]
           [ OID [=] oid ]

URL: https://www.postgresql.org/docs/15/sql-createdatabase.html
```

# Prompt Setting

```sql
postgres=# \set PROMPT1 '%n@%M:%x %/# '
postgres@localhost: postgres#

format:
https://www.postgresql.org/docs/current/app-psql.html#APP-PSQL-PROMPTING
```

# Command Alias

```sql
\set eav 'explain analyze verbose'

:eav  == 'explain analyze verbose'
```

# List Databases \l

```sql
appdb=> \l+
                                                                                   List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    | ICU Locale | Locale Provider |   Access privileges   |  Size   | Tablespace |                Description
-----------+----------+----------+-------------+-------------+------------+-----------------+-----------------------+---------+------------+--------------------------------------------
 appdb     | appuser  | UTF8     | C           | en_US.utf8  |            | libc            |                       | 7613 kB | tbs_app    | appdb
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            |                       | 7597 kB | pg_default | default administrative connection database
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +| 7441 kB | pg_default | unmodifiable empty database
           |          |          |             |             |            |                 | postgres=CTc/postgres |         |            |
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |            | libc            | =c/postgres          +| 7677 kB | pg_default | default template for new databases
           |          |          |             |             |            |                 | postgres=CTc/postgres |         |            |
(4 rows)
```

# Watch

psql command，not SQL statement

```sql
<command>; \watch <sec>?

appdb=> select datname, usename, wait_event, wait_event_type, query from pg_stat_activity where state='active';\watch 2
 datname | usename | wait_event | wait_event_type |                                                  query
---------+---------+------------+-----------------+---------------------------------------------------------------------------------------------------------
 appdb   | appuser |            |                 | select datname, usename, wait_event, wait_event_type, query from pg_stat_activity where state='active';
(1 row)

                                                         Tue 19 Sep 2023 09:57:52 AM CST (every 2s)

 datname | usename | wait_event | wait_event_type |                                                  query
---------+---------+------------+-----------------+---------------------------------------------------------------------------------------------------------
 appdb   | appuser |            |                 | select datname, usename, wait_event, wait_event_type, query from pg_stat_activity where state='active';
(1 row)
```

# Show Tablespace \db

```sql
postgres=# \db+
                                          List of tablespaces
    Name    |  Owner   |        Location         | Access privileges | Options |  Size   | Description
------------+----------+-------------------------+-------------------+---------+---------+-------------
 pg_default | postgres |                         |                   |         | 22 MB   |
 pg_global  | postgres |                         |                   |         | 555 kB  |
 tbs_app    | postgres | /var/lib/pgsql/15/data2 |                   |         | 7625 kB |
(3 rows)
```

# Connect to database \c

```sql

\c[onnect] {[DBNAME|- USER|- HOST|- PORT|-] | conninfo}
                         connect to new database (currently "appdb")

appdb=> \c postgres postgres
Password for user postgres:
You are now connected to database "postgres" as user "postgres".

postgres=# \c appdb appuser 127.0.0.1 5432
Password for user appuser:
You are now connected to database "appdb" as user "appuser".

```

# Show Connection Info

```sql
\conninfo
appdb=> \conninfo
You are connected to database "appdb" as user "appuser" on host "127.0.0.1" at port "5432".
```

# Show encoding

```bash
appdb=> \encoding
UTF8
```

# Change Current User Password

```bash
\password [USERNAME]   securely change the password for a user

appdb-> \password
Enter new password for user "appuser":
Enter it again:
```

# Show Objects \d[S+]

```sql
postgres=# \c appdb appuser 127.0.0.1
Password for user appuser:
You are now connected to database "appdb" as user "appuser" on host "127.0.0.1" at port "5432".
appdb=> \d
         List of relations
 Schema  |  Name  | Type  |  Owner
---------+--------+-------+---------
 appuser | test01 | table | appuser
(1 row)

appdb=> \d+
                                      List of relations
 Schema  |  Name  | Type  |  Owner  | Persistence | Access method |    Size    | Description
---------+--------+-------+---------+-------------+---------------+------------+-------------
 appuser | test01 | table | appuser | permanent   | heap          | 8192 bytes |
(1 row)
```

\dt  table

\di index

\ds sequence

\dv view

\df function

# Match Tables

```sql
appdb=> \dt+ pg_catalog.pg_t*
                                             List of relations
   Schema   |       Name       | Type  |  Owner   | Persistence | Access method |    Size    | Description
------------+------------------+-------+----------+-------------+---------------+------------+-------------
 pg_catalog | pg_tablespace    | table | postgres | permanent   | heap          | 48 kB      |
 pg_catalog | pg_transform     | table | postgres | permanent   | heap          | 0 bytes    |
 pg_catalog | pg_trigger       | table | postgres | permanent   | heap          | 8192 bytes |
 pg_catalog | pg_ts_config     | table | postgres | permanent   | heap          | 40 kB      |
 pg_catalog | pg_ts_config_map | table | postgres | permanent   | heap          | 56 kB      |
 pg_catalog | pg_ts_dict       | table | postgres | permanent   | heap          | 48 kB      |
 pg_catalog | pg_ts_parser     | table | postgres | permanent   | heap          | 40 kB      |
 pg_catalog | pg_ts_template   | table | postgres | permanent   | heap          | 40 kB      |
 pg_catalog | pg_type          | table | postgres | permanent   | heap          | 160 kB     |
(9 rows)
```

# Show Schema  \dn[S+]

```sql
appdb=> \dn+
                                        List of schemas
  Name   |       Owner       |           Access privileges            |      Description
---------+-------------------+----------------------------------------+------------------------
 appuser | appuser           |                                        |
 public  | pg_database_owner | pg_database_owner=UC/pg_database_owner+| standard public schema
         |                   | =U/pg_database_owner                   |
(2 rows)
```

# List All Schemas

```sql
postgres=# select * from pg_namespace;
  oid  |      nspname       | nspowner |                            nspacl
-------+--------------------+----------+---------------------------------------------------------------
    99 | pg_toast           |       10 |
    11 | pg_catalog         |       10 | {postgres=UC/postgres,=U/postgres}
  2200 | public             |     6171 | {pg_database_owner=UC/pg_database_owner,=U/pg_database_owner}
 13915 | information_schema |       10 | {postgres=UC/postgres,=U/postgres}
 16399 | appuser            |    16387 | {appuser=UC/appuser,app_rw=UC/appuser}
(5 rows)
```

# Execute OS Command

```sql
appdb=> \! ls -ltr
total 4
drwx------ 5 postgres postgres 4096 Sep 18 17:10 15
appdb=>
```

# Toggle Timing of Commands

```sql
\timing on
```

# Encoding

```sql
appdb=> show server_encoding;
+-----------------+
| server_encoding |
+-----------------+
| UTF8            |
+-----------------+
(1 row)

appdb=> SELECT pg_encoding_to_char(encoding) FROM pg_database WHERE datname = current_database();
+---------------------+
| pg_encoding_to_char |
+---------------------+
| UTF8                |
+---------------------+
(1 row)

appdb=> SELECT current_setting('server_encoding');
+-----------------+
| current_setting |
+-----------------+
| UTF8            |
+-----------------+
(1 row)

appdb=> SELECT setting FROM pg_settings WHERE name = 'server_encoding';
+---------+
| setting |
+---------+
| UTF8    |
+---------+
(1 row)
```

# Show Roles/Users \dg \du

```sql
postgres=# \dg+
                                          List of roles
 Role name |                         Attributes                         | Member of | Description
-----------+------------------------------------------------------------+-----------+-------------
 app_rw    | Cannot login                                               | {}        |
 appuser   |                                                            | {app_rw}  |
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}        |
```

# Show Table Access Privileges

```sql
postgres=# \c appdb appuser 127.0.0.1
Password for user appuser:
You are now connected to database "appdb" as user "appuser" on host "127.0.0.1" at port "5432".
appdb=>
appdb=> \dt+
                                      List of relations
 Schema  |  Name  | Type  |  Owner  | Persistence | Access method |    Size    | Description
---------+--------+-------+---------+-------------+---------------+------------+-------------
 appuser | test01 | table | appuser | permanent   | heap          | 8192 bytes |
(1 row)

appdb=> \z test01
                              Access privileges
 Schema  |  Name  | Type  | Access privileges | Column privileges | Policies
---------+--------+-------+-------------------+-------------------+----------
 appuser | test01 | table |                   |                   |
(1 row)
```

# Output Style

```sql
appdb=> \pset border 0;
Border style is 0.
appdb=> select * from test01;
id name
-- ----
 1 Tom
(1 row)

appdb=> \pset border 1;
Border style is 1.
appdb=> select * from test01;
 id | name
----+------
  1 | Tom
(1 row)

appdb=> \pset border 2;
Border style is 2.
appdb=> select * from test01;
+----+------+
| id | name |
+----+------+
|  1 | Tom  |
+----+------+
(1 row)
```