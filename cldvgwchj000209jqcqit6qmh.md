---
title: "MySQL Architecture"
datePublished: Wed Feb 08 2023 09:27:30 GMT+0000 (Coordinated Universal Time)
cuid: cldvgwchj000209jqcqit6qmh
slug: mysql-architecture
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Y9kOsyoWyaU/upload/79628cf38306f0b96bdfd5f10bd42048.jpeg
tags: mysql

---

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675607748209/4e0236fb-0672-42a1-94f3-a0e2eb517dfd.png align="center")

MySQL can be divided into three layers: Client Layer, Server Layer, and Storage Engine.

## Server Layer

The server Layer includes connectors, query cache, parser, optimizer, executor, and so on, which covers all the core services of MySQL. It also provides internal functions (such as Date, Time, Math, and encrypting), and other services (such as stored procedure, trigger, and view)

### Connectors

Connectors build the connections between the client program (such as Navicat, MySQL workbench, JDBC...) and the MySQL server.

```plaintext
[root@192]# mysql -h host[address] -u root[user] -p root[password] -P 3306
```

'mysql' in the command above is the client program, it builds the connection through the TCP. After that, the connector will start to authenticate the user's identity by the username and password.

If the username and password are wrong, you will get the "Access denied" error.

If the username and password are correct, the connectors will check all the permissions you have. After a user successfully built a connection, even if the administrator account modifies the user's permissions, it will not affect the existing permissions. After the modification is completed, only new connections will use the new permissions.

### Query Cache

After the connection is established, if the user queries (select statements) the database, then the query cache will engage.

When MySQL receives a select query, it will check the query cache if the statement was executed before. All the select statements and results may store in the query cache as key-value pairs.

eg. Query Cache

| Key | Value |
| --- | --- |
| 'select id from a' | '{1,2,3,4,5,6,7,8,9,10}' |

Therefore when the key is matched, the result will be returned immediately without querying the database.

Most of the time, the query cache is pointless, we only recommended using it on static tables for the following reasons.

* When a table data is changed, all the query cache records all be cleared. The hit ratio will be very low on a frequently updated table.
    
* The key(select statement) has to be the same.
    
    ```plaintext
    select id from a # will hit the cache
    select t.id from a as t # will not
    ```
    

If you want to use the query cache, I suggest you set the MySQL variable 'query\_cache\_type' to 'DEMAND'.

```plaintext
mysql.cnf
# query_cache_type:
# 0 - OFF
# 1 - ON
# 2 - DEMAND
query_cache_type=2
```

You can use the query cache by adding 'SQL\_CACHE' to your statement.

```plaintext
mysql> select SQL_CACHE * from a
```

***<mark>MySQL 8.0 already removed the query cache.</mark>***

### Parser

Parser is the component for analyzing the query statement. Parser includes two parts: the lexical scanner and the grammar rule module. The lexical scanner will break the statement into tokens. The grammar rule module will check if these tokens satisfy the MySQL grammar rule. If the query is wrong, you will get a SQL syntax error.

eg.

```plaintext
mysql> select * fro t;
ERROR 1064 (42000): You have an error in your SQL syntax;...
```

The following is the process of the parser:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675846236021/99d18be6-e20f-4dde-a212-39ef6bf6f6db.png align="center")

After the lexical scanner, you will get the parse tree:

![Database Compatibility :: ShardingSphere](https://shardingsphere.apache.org/document/current/img/db-compatibility/principle1.png align="left")

Then the parser work is done!

### Optimizer

Before executing the query, MySQL will try to optimize the query.

* If there are multiple indexes, the optimizer will use one of them.
    
* If there are multiple joins, the optimizer will decide the order of join.
    
* Other internal optimization.
    

### Executor

When executing the query, MySQL will check if the user has permission of the table. If the user is permitted, the executor will continue to execute the query and return the result.

## Storage Engine

The storage engines handle all the SQL operations(Insert, Delete, Update, Select). InnoDB is the default selection and is the most used engine. if you do not choose the storage engine when you create a table, InnoDB will be used.

**Storage Engines Feature Summary**

| **Feature** | **MyISAM** | **Memory** | **InnoDB** | **Archive** | **NDB** |
| --- | --- | --- | --- | --- | --- |
| **B-tree indexes** | Yes | Yes | Yes | No | No |
| **Backup/point-in-time recovery (note 1)** | Yes | Yes | Yes | Yes | Yes |
| **Cluster database support** | No | No | No | No | Yes |
| **Clustered indexes** | No | No | Yes | No | No |
| **Compressed data** | Yes (note 2) | No | Yes | Yes | No |
| **Data caches** | No | N/A | Yes | No | Yes |
| **Encrypted data** | Yes (note 3) | Yes (note 3) | Yes (note 4) | Yes (note 3) | Yes (note 3) |
| **Foreign key support** | No | No | Yes | No | Yes (note 5) |
| **Full-text search indexes** | Yes | No | Yes (note 6) | No | No |
| **Geospatial data type support** | Yes | No | Yes | Yes | Yes |
| **Geospatial indexing support** | Yes | No | Yes (note 7) | No | No |
| **Hash indexes** | No | Yes | No (note 8) | No | Yes |
| **Index caches** | Yes | N/A | Yes | No | Yes |
| **Locking granularity** | Table | Table | Row | Row | Row |
| **MVCC** | No | No | Yes | No | No |
| **Replication support (note 1)** | Yes | Limited (note 9) | Yes | Yes | Yes |
| **Storage limits** | 256TB | RAM | 64TB | None | 384EB |
| **T-tree indexes** | No | No | No | No | Yes |
| **Transactions** | No | No | Yes | No | Yes |
| **Update statistics for the data dictionary** | Yes | Yes | Yes | Yes | Yes |