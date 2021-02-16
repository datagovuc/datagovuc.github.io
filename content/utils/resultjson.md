---
title: "Resultado de query en JSON"
---

# **Queries que muestran el resultado en JSON**

1. Resultado con top-level element:

```sql
SELECT * 
FROM INFORMATION_SCHEMA.COLUMNS 
WHERE TABLE_NAME = '<tableName>' 
FOR JSON PATH, ROOT('<topLevelElementeName>')
```

2. Resultado sin top-level element:

```sql
SELECT * 
FROM INFORMATION_SCHEMA.COLUMNS 
WHERE TABLE_NAME = '<tableName>' 
FOR JSON PATH
```

2. Resultado que entrega la misma estructura que la del SELECT statement:

```sql
SELECT TABLE_SCHEMA
    , TABLE_NAME
    , COLUMN_NAME
FROM INFORMATION_SCHEMA.COLUMNS 
WHERE TABLE_NAME = '<tableName>' 
FOR JSON AUTO
```