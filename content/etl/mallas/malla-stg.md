---
weight: 2
title: "Malla STG"
---




Asumiendo que se realizó anteriormente el proceso de malla ADL, la tabla a actualizar ya se encuentra cargada en el MCP.MCP_CATALOGO, Con esto se deben realizar dos cosas:


### 1. Verificar/actualizar FlG 
Como se mencionó anteriormente en caso de que exista la tabla en el catalogo solo se debe actualizar flag.
```sql
Update [MCP].[MCP_CATALOGO]
SET FLG_STG = 1
WHERE NOMBRE_TABLA = 'TABLA_1' and NOMBRE_OWNER = 'ESQUEMA_1'
```

### 2. Actualizar fecha de proceso