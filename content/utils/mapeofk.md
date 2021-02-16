---
title: "Mapeo de FK"
---

# **Mapero de FK**

Usamos el siguiente system util para ver la información relativa a la tabla. Al final aparecen las FK:

```sql
USE <dataBaseName>;
GO  
EXEC sp_help N'<dataBaseName.schemaName.tableName>';  
```