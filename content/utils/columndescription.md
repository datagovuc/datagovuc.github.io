---
title: "Columns Description"
---

# **Descripción de la columna de una tabla**

```sql
    SELECT
        SCHEMA_NAME(so.schema_id) as [schema_name]
        , so.name                   as table_name
        , sc.name                   as column_name
        , sep.value                 as column_description
    FROM sys.objects        as so
    LEFT JOIN sys.columns   as sc
        ON so.object_id = sc.object_id
    LEFT JOIN sys.extended_properties as sep 
        ON so.object_id = sep.major_id 
        AND sc.column_id =sep.minor_id
    WHERE so.name = <tableName>
        AND sc.name = <columnName>
```


{{< hint info >}}
**Importante**  
Se creó la siguiente function:

`SELECT * FROM UTILS.ColumnDescription (N'<tableName>', N'<columnName>')`
{{< /hint >}}