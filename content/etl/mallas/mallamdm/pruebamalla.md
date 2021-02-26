---
weight: 2
title: "Prueba malla"
---


# **Pruebas de la malla**

Para probar la malla existen dos entidades:

+ `MDIUC.PERSON_TEST`
+ `MDIUC.COUNTRY`

## `PERSON_TEST`

Corresponde a una versión reducida de `PERSON`. Sus tablas de origen son:
+ `NORMALIZADO_TEST.PERSON_BANNER`
+ `NORMALIZADO_TEST.PERSON_PEOPLE_SOFT`

Cada tabla contiene el RUT, dígito verificador, apellido paterno y materno, y el nombre. Los registros de estas tablas de extrajeron de Banner y PeopleSoft respectivamente.

Cada tabla contiene 90 registros. 

Existen 80 RUTs que aparecen en ambas tablas. De ellos:

+ Hay 5 personas con el mismo RUT pero distintos nombres entre `PERSON_BANNER` y `PERSON_PEOPLE_SOFT` (personas distintas). Para una de ellas, existe una resolución manual en las tablas `CONFLICTIVE_PERSON_TEST `y `RESOLUTION_PERSON_TEST`.

+ Hay 10 personas con el mismo RUT pero con el nombre un poco distinto entre `PERSON_BANNER` y `PERSON_PEOPLE_SOFT` (misma persona).

+ Hay 65 personas con mismo RUT y mismo nombre entre `PERSON_BANNER` y `PERSON_PEOPLE_SOFT`.

## `COUNTRY`

Esta entidad con fuente oficial hace referencia a las tablas:

+ `UC_BANNER.PAIS`
+ `NORMALIZADO_PEOPLE_SOFT.COUNTRY`


# **Cómo llenar las tablas que consume el pipeline MDM**

A continuación, los pasos para llenar la tablas que se utilizan por el pipeline del MDM.

# **Pasos para llenar las tablas que consume el pipeline del MDM**

1. Limpiar las tablas que utiliza el pipeline. Para ello ejecutamos el siguiente script:

```sql
TRUNCATE TABLE MCP_MDIUC.COMPARING_RULE
TRUNCATE TABLE MCP_MDIUC.GROUPING_RULE
TRUNCATE TABLE MCP_MDIUC.ENTITY_ATTRIBUTE_SYSTEM_MAPPING

/* Due to some of their columns being referenced as FK by other tables, we cannot simply truncate these tables. So we delete their rows */
-- MCP_MDIUC.ENTITY_SYSTEM_TABLE
DELETE FROM MCP_MDIUC.ENTITY_SYSTEM_TABLE 
WHERE entity_system_table_id IN (
    SELECT entity_system_table_id 
    FROM MCP_MDIUC.ENTITY_SYSTEM_TABLE
    )

-- MCP_MDIUC.ENTITY_ATTRIBUTE
DELETE FROM MCP_MDIUC.ENTITY_ATTRIBUTE 
WHERE entity_attribute_id IN (
    SELECT entity_attribute_id 
    FROM MCP_MDIUC.ENTITY_ATTRIBUTE
    )

-- MCP_MDIUC.ENTITY
DELETE FROM MCP_MDIUC.ENTITY 
WHERE entity_id IN (
    SELECT entity_id 
    FROM MCP_MDIUC.ENTITY
    )
```

2. Poblamos las tablas con datos:

```sql
-- ENTITY
INSERT INTO MCP_MDIUC.ENTITY ([name], is_valid, has_official_source)
VALUES 
      ('PERSON_TEST',1,0)
    , ('PERSON_TEST_2',0,0)

-- ENTITY_ATTRIBUTE
INSERT INTO MCP_MDIUC.ENTITY_ATTRIBUTE (entity_id, attribute_name, attribute_type, is_primary_key)
VALUES
      (20,'rut','nvarchar',1)
    , (20,'dv','nvarchar',0)
    , (20,'paternal_last_name','nvarchar',0)
    , (20,'maternal_last_name','nvarchar',0)
    , (20,'name','nvarchar',0)
    , (21,'rut','nvarchar',1)
    , (21,'dv','nvarchar',0)
    , (21,'paternal_last_name','nvarchar',0)
    , (21,'maternal_last_name','nvarchar',0)
    , (21,'name','nvarchar',0)

-- ENTITY_SYSTEM_TABLE
INSERT INTO MCP_MDIUC.ENTITY_SYSTEM_TABLE(entity_id, [schema_name], table_name, primary_key)
VALUES
      (20,'NORMALIZADO_TEST','PERSON_BANNER','rut')
    , (20,'NORMALIZADO_TEST','PERSON_PEOPLE_SOFT','rut')
    , (21,'NORMALIZADO_TEST','PERSON_BANNER','rut')
    , (21,'NORMALIZADO_TEST','PERSON_PEOPLE_SOFT','rut')

-- ENTITY_ATTRIBUTE_SYSTEM_MAPPING
INSERT INTO MCP_MDIUC.ENTITY_ATTRIBUTE_SYSTEM_MAPPING (entity_attribute_id, entity_system_table_id, column_name, attribute_rank)
VALUES
      (85,36,'rut',1)
    , (85,37,'rut',2)
    , (86,36,'check_digit',1)
    , (86,37,'check_digit',2)
    , (87,36,'paternal_last_name',1)
    , (87,37,'paternal_last_name',2)
    , (88,36,'maternal_last_name',1)
    , (88,37,'maternal_last_name',2)
    , (89,36,'first_name',1)
    , (89,37,'name',2)
    , (90,38,'rut',1)
    , (90,39,'rut',2)
    , (91,38,'check_digit',1)
    , (91,39,'check_digit',2)
    , (92,38,'paternal_last_name',1)
    , (92,39,'paternal_last_name',2)
    , (93,38,'maternal_last_name',1)
    , (93,39,'maternal_last_name',2)
    , (94,38,'first_name',1)
    , (94,39,'name',2)

-- GROUPING_RULE
INSERT INTO MCP_MDIUC.GROUPING_RULE
VALUES
      (85)
    , (90)

-- COMPARING_RULE
INSERT INTO MCP_MDIUC.COMPARING_RULE (entity_attribute_id, rule_type, parameter, process)
VALUES
      (87,'fuzzy_allow_empty',70,'concat')
    , (88,'fuzzy_allow_empty',70,'concat')
    , (89,'fuzzy_allow_empty',70,'concat')
    , (92,'fuzzy_allow_empty',70,'concat')
    , (93,'fuzzy_allow_empty',70,'concat')
    , (94,'fuzzy_allow_empty',70,'concat')
```

El ejemplo anterior va a generar las siguientes tablas:

### **MCP_MDIUC.ENTITY**

| entity_id | name | is_valid | has_official_source |
| :-- | :-- | :-- | :-- |
| 20 | PERSON_TEST | 1 | 0 |
| 21 | PERSON_TEST_2 | 0 | 0 |

### **MCP_MDIUC.ENTITY_ATTRIBUTE**

| entity_attribute_id | entity_id | attribute_name | attribute_type | is_primary_key |
| :-- | :-- | :-- | :-- | :-- |
| 85 | 20 | rut | nvarchar | 1 |
| 86 | 20 | dv | nvarchar | 0 |
| 87 | 20 | paternal_last_name | nvarchar | 0 |
| 88 | 20 | maternal_last_name | nvarchar | 0 |
| 89 | 20 | name | nvarchar | 0 |
| 90 | 21 | rut | nvarchar | 1 |
| 91 | 21 | dv | nvarchar | 0 |
| 92 | 21 | paternal_last_name | nvarchar | 0 |
| 93 | 21 | maternal_last_name | nvarchar | 0 |
| 94 | 21 | name | nvarchar | 0 |

### **MCP_MDIUC.ENTITY_SYSTEM_TABLE**

| entity_system_table_id | entity_id | schema_name | table_name | primary_key |
| :-- | :-- | :-- | :-- | :-- |
| 36 | 20 | NORMALIZADO_TEST | PERSON_BANNER | rut |
| 37 | 20 | NORMALIZADO_TEST | PERSON_PEOPLE_SOFT | rut |
| 38 | 21 | NORMALIZADO_TEST | PERSON_BANNER | rut |
| 39 | 21 | NORMALIZADO_TEST | PERSON_PEOPLE_SOFT | rut |

### **MCP_MDIUC.ENTITY_ATTRIBUTE_SYSTEM_MAPPING**

| entity_attribute_system_mapping | entity_attribute_id | entity_system_table_id | column_name | attribute_rank |
| :-- | :-- | :-- | :-- | :-- |
| 18 | 85 | 36 | rut | 1 |
| 19 | 85 | 37 | rut | 2 |
| 20 | 86 | 36 | check_digit | 1 |
| 21 | 86 | 37 | check_digit | 2 |
| 22 | 87 | 36 | paternal_last_name | 1 |
| 23 | 87 | 37 | paternal_last_name | 2 |
| 24 | 88 | 36 | maternal_last_name | 1 |
| 25 | 88 | 37 | maternal_last_name | 2 |
| 26 | 89 | 36 | first_name | 1 |
| 27 | 89 | 37 | name | 2 |
| 28 | 90 | 38 | rut | 1 |
| 29 | 90 | 39 | rut | 2 |
| 30 | 91 | 38 | check_digit | 1 |
| 31 | 91 | 39 | check_digit | 2 |
| 32 | 92 | 38 | paternal_last_name | 1 |
| 33 | 92 | 39 | paternal_last_name | 2 |
| 34 | 93 | 38 | maternal_last_name | 1 |
| 35 | 93 | 39 | maternal_last_name | 2 |
| 36 | 94 | 38 | first_name | 1 |
| 37 | 94 | 39 | name | 2 |

### **MCP_MDIUC.GROUPING_RULE**

| grouping_rule_id | entity_attribute_id |
| :-- | :-- |
| 4 | 85 |
| 5 | 90 |

### **MCP_MDIUC.COMPARING_RULE**

| comparing_id | entity_attribute_id | rule_type | parameter | process |
| :-- | :-- | :-- | :-- | :-- |
| 4 | 87 | fuzzy_allow_empty | 70 | concat |
| 5 | 88 | fuzzy_allow_empty | 70 | concat |
| 6 | 89 | fuzzy_allow_empty | 70 | concat |
| 7 | 92 | fuzzy_allow_empty | 70 | concat |
| 8 | 93 | fuzzy_allow_empty | 70 | concat |
| 9 | 94 | fuzzy_allow_empty | 70 | concat |