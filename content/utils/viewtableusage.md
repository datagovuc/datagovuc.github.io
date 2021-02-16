---
title: "Tablas consumidas por una vista"
---

### **Query para revisar las tablas que consume una vista**

La siguente query muesta las tablas con las que se construye una vista:

```sql
SELECT * 
FROM INFORMATION_SCHEMA.VIEW_TABLE_USAGE 
WHERE VIEW_NAME = '<tableName>'
```