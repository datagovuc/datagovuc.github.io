---
weight: 1
# bookFlatSection: true
title: "Creación usuarios"
---

# Cómo crear usuarios y roles en Bases de Datos con AAD auth

## Estructura estándar

+ Cómo ver qué usuarios tienen accesos.
+ A qué objeto tiene acceso un usuario.
+ Cómo añadir un usuario nuevo a un grupo en AAD existente.
    + Cómo añadir al grupo AAD en SQL.




# 1. Crear usuario
##  1.1 Autenticación: SQL login

```tsql
CREATE LOGIN MaryUser WITH PASSWORD = '8YpFWHhrWUgTTbED';
CREATE USER MaryUser FROM LOGIN MaryUser;       -- son 2 veces, uno en la master y otro en tu BD
ALTER ROLE db_datareader ADD MEMBER MaryUser;   -- en tu BD
ALTER ROLE db_ddladmin ADD MEMBER MaryUser; 
ALTER ROLE db_datawriter ADD MEMBER MaryUser;
```


##  1.2 Autenticación: AAD 

```tsql
CREATE USER [g_magnet] FROM EXTERNAL PROVIDER;
```

##  1.2 Verificación de usuarios 
```tsql
SELECT name AS username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc AS authentication_type
FROM sys.database_principals
ORDER BY type_desc;
```