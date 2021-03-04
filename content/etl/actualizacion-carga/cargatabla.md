---
title: "Carga de tablas"
---

# **Carga de tablas cuando el owner existe en MCP_CATALOGO**

A continuación, se ilustra cómo cargar las tablas `ETNIA`, `INDICE_VULNER_ESCOLAR` y `POSTUL` desde el esquema `ADMISION` de la BD `Stage-Datagov_Prod` hacia la BD `Sandbox-Datagov_Prod`. Ambas BDs viven en el server `datagov-uc`.

## **Paso 1**

Primero, vemos la última fecha de actualización de las tablas de interés para corroborar que no sea la del día en el que se hará la carga. Para ello corremos el script:

```sql
SELECT * 
FROM MCP.MCP_FECHA_PROCESO 
WHERE ID_CATALOGO IN (
    SELECT ID_CATALOGO 
    FROM MCP.MCP_CATALOGO 
    WHERE NOMBRE_TABLA IN ('ETNIA', 'INDICE_VULNER_ESCOLAR', 'POSTUL') AND NOMBRE_OWNER = 'ADMISION'
)
```

## **Paso 2**

Luego, seteamos la última y la próxima fecha de actulización de las tablas, tanto para la malla ADL como para la malla STG:

```sql
UPDATE MCP.MCP_FECHA_PROCESO 
SET   FCH_ULT_ACTUALIZACION_STG = '1900-01-01'
    , FCH_ULT_ACTUALIZACION_ADL = '1900-01-01'  
    , FCH_PROX_ACTUALIZACION_STG = '2021-02-04' -- Fecha del día en que hacemos la carga
    , FCH_PROX_ACTUALIZACION_ADL = '2021-02-04' -- Fecha del día en que hacemos la carga
WHERE ID_CATALOGO IN (
    SELECT ID_CATALOGO 
    FROM MCP.MCP_CATALOGO 
    WHERE NOMBRE_TABLA IN ('ETNIA', 'INDICE_VULNER_ESCOLAR', 'POSTUL') AND NOMBRE_OWNER = 'ADMISION'
    )
```

## **Paso 3**

Antes de ir a DataFactory y ejecutar los pipelines, debemos asegurarnos que las tablas a cargar estén en la tabla `SBX.CARGA_TABLA_STG_A_SBX`. Para ellos corremos lo siguiente:

```sql
SELECT * 
FROM SBX.CARGA_TABLA_STG_A_SBX
WHERE NOMBRE_TABLA IN ('ETNIA', 'INDICE_VULNER_ESCOLAR', 'POSTUL')
    AND NOMBRE_OWNER = 'ADMISION'
```

Es de esperar que la query no arroje resultados. Por lo que debemos agregar las tablas que se cargarán:

```sql
INSERT INTO SBX.CARGA_TABLA_STG_A_SBX (NOMBRE_OWNER, NOMBRE_TABLA, FLG_SBX)
VALUES ('ADMISION', 'ETNIA', 1)
     , ('ADMISION', 'INDICE_VULNER_ESCOLAR', 1)
     , ('ADMISION', 'POSTUL', 1)
```

Notemos que solo ingresamos los datos relevantes.

## **Paso 4**

Una vez ejecutados estos pasos, se ejecutan las mallas ADL, STG y SBX en el Azure Data Factory. La Ejecución debe ser secuencial:

`Malla ADL` :arrow_right: `Malla STG` :arrow_right: `Malla SBX`

Finalmente, y desde la BD `Sandbox-Datagov_Prod`, corremos lo siguiente para asegurarnos que las tablas se cargaron:

```sql
SELECT [name]
    , create_date
    , modify_date
    , schema_id
    , SCHEMA_NAME(schema_id)
FROM sys.objects
WHERE SCHEMA_NAME(schema_id) = 'ADMISION'
    AND CAST(create_date AS DATE) = '2021-02-04'
```