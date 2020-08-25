---
title: SQL Server, Index Fragmentation
date: 2020-06-11 22:36:46
tags:
- sql
- sql-server
- index
- fragmentation
---

Indexes on the tables can get fragmented. In order to detect the fragmentation, following SQL script can be run:

```sql
SELECT a.object_id, object_name(a.object_id) AS TableName,
    a.index_id, name AS IndedxName, avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats
    (DB_ID (N'DB_NAME')
        , OBJECT_ID(N'TABLE_NAME')
        , NULL
        , NULL
        , NULL) AS a
INNER JOIN sys.indexes AS b
    ON a.object_id = b.object_id
    AND a.index_id = b.index_id;
GO
```

If fragmentation is higher than %10, then a corrective action should be taken.
Best course of action is to rebuild the indexes, if the table is not too big:

```sql
ALTER INDEX ALL ON DB_NAME.dbo.TABLE_NAME REBUILD;
```

If the table has too many rows, consider reorganizing. See links below.

[Detecting Fragmented Indexes](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/reorganize-and-rebuild-indexes?view=sql-server-ver15)
[Rebuilding Indexes](https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-index-transact-sql?view=sql-server-ver15#rebuilding-indexes)

[<- Back to all TILs](../../../05/19/til/)