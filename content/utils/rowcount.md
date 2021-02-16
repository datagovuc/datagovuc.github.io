---
title: "Row count"
---

# **Row count de las tablas de un esquema en particular**

Para generar una tabla con el número de registros por tabla según esquema, ejecutamos la siguiente query:

```sql
WITH [RowCount] AS (
    SELECT SCHEMA_NAME(t.schema_id) AS [schema_name]
        , t.name AS table_name 
        , s.row_count
    FROM sys.tables t 
    JOIN sys.dm_db_partition_stats s
        ON t.object_id = s.object_id
        AND t.type = 'U'
        AND t.name NOT LIKE '%dss%'
        AND s.index_id IN (0, 1)
)
SELECT * FROM [RowCount] 
WHERE [schema_name] = '<nombreEsquema>'
    AND row_count <> 0 
ORDER BY [schema_name], row_count DESC
```