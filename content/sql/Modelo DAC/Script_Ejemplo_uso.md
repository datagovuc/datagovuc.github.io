---
weight: 1
title: "Script ejemplo de uso DAC"
---


Discretionary access control (DAC)

### 1. Crear politicas
```sql
UPDATE DAC_SECURITY.DAC_USER_ENTITY_ACCESS SET flag_entity_access = 0 -- negar acceso a tabla
UPDATE DAC_SECURITY.DAC_USER_ROW_LEVEL_SECURITY SET flag_rls_vigencia = 1
UPDATE DAC_SECURITY.DAC_USER_UNMASK SET flag_unmask = 1
UPDATE DAC_SECURITY.DAC_DATA_MASKING SET flag_dm_vigencia = 1
UPDATE DAC_SECURITY.DAC_DATA_CLASSIFICATION SET flag_dc_vigencia = 1/
```
### 1. Borrar politicas
```sql
UPDATE DAC_SECURITY.DAC_USER_ENTITY_ACCESS SET flag_entity_access = 1  -- dar acceso a tabla
UPDATE DAC_SECURITY.DAC_USER_ROW_LEVEL_SECURITY SET flag_rls_vigencia = 0
UPDATE DAC_SECURITY.DAC_USER_UNMASK SET flag_unmask = 0
UPDATE DAC_SECURITY.DAC_DATA_MASKING SET flag_dm_vigencia = 0
UPDATE DAC_SECURITY.DAC_DATA_CLASSIFICATION SET flag_dc_vigencia = 0/
```
### 3. Validar existencia de políticas
```sql
SELECT * FROM sys.security_policies -- RLS
SELECT * FROM sys.masked_columns -- Data Masking
SELECT * FROM sys.sensitivity_classifications -- Data Classification
SELECT * FROM DAC_SECURITY.DAC_USER_CHECK_SELECT_PERMISSIONS -- SELECT permissions
```