---
title: Data Block,Page Struct PostgreSQL,Oracle,MySQL
tags:
  - Oracle
  - PostgreSQL
  - MySQL
  - Page
  - Block
categories:
  - Database
  - PostgreSQL
---

# PostgreSQL

****Database Page Layout****

![postgresql_data_page](/assets/images/2023-09-15/postgresql_data_page.png)



**Table 73.4. HeapTupleHeaderData Layout**

| Field | Type | Length | Description |
| --- | --- | --- | --- |
| t_xmin | TransactionId | 4 bytes | insert XID stamp |
| t_xmax | TransactionId | 4 bytes | delete XID stamp |
| t_cid | CommandId | 4 bytes | insert and/or delete CID stamp (overlays with t_xvac) |
| t_xvac | TransactionId | 4 bytes | XID for VACUUM operation moving a row version |
| t_ctid | ItemPointerData | 6 bytes | current TID of this or newer row version |
| t_infomask2 | uint16 | 2 bytes | number of attributes, plus various flag bits |
| t_infomask | uint16 | 2 bytes | various flag bits |
| t_hoff | uint8 | 1 byte | offset to user data |

# Oracle

data block format

![oracle_data_block](/assets/images/2023-09-15/oracle_data_block.png)

row format

![oracle_row_format](/assets/images/2023-09-15/oracle_row_format.png)

# MySQL InnoDB

![mysql_innodb_index](/assets/images/2023-09-15/mysql_innodb_index.png)

Reference

https://www.postgresql.org/docs/15/storage-page-layout.html

https://docs.oracle.com/en/database/oracle/oracle-database/19/cncpt/logical-storage-structures.html#GUID-D01C1E10-43DE-461D-9B41-A65266EAB70B

https://blog.jcole.us/2013/01/07/the-physical-structure-of-innodb-index-pages/