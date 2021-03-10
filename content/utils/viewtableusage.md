---
title: "Tablas consumidas por una vista"
---

### **Tablas que consume una vista**

Se creó la siguiente función en la BD `Sandbox-Datagov_Prod` para retornar las tablas que consume una vista con la fecha de modificación de cada tabla:

```sql
SELECT * FROM UTILS.ViewTablesUsage()
```