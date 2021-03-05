---
title: "Actualización de tablas"
---

# **Actualización de tablas cuando el OWNER existe en MCP_CATALOGO**

Para actualizar los datos de una tabla en particular en la BD `Stage-Datagov_Prod`, debemos aplicar los siguientes pasos:

{{< hint info >}}
**Nota**
Queda pendiente poner este proceso en un proceso unificado, ya sea SP o cualquier otro artefacto.
{{< /hint >}}

## **Paso 1**

Si, por ejemplo, queremos actualizar las tablas que consume una vista en la BD `Sandbox-Datagov_Prod`, corremos los siguiente:

```sql
SELECT
      TABLE_SCHEMA
    , TABLE_NAME
FROM INFORMATION_SCHEMA.VIEW_TABLE_USAGE 
WHERE VIEW_NAME = '<nombreVista>'
```

Como se aprecia, solo recuperamos los campos que nos interesan, es decir, `TABLE_SCHEMA` y `TABLE_NAME`.

## **Paso 2**

Para asegurarnos que la tabla (o tablas) que queremos actualizar existen, hacemos la siguiente consulta en la BD `Stage-Datagov_Prod`:

```sql
SELECT * FROM MCP.MCP_CATALOGO
WHERE NOMBRE_OWNER = '<nombreOwner>'
AND NOMBRE_TABLA = '<nombreTabla>'
```

Haremos el update de la data de las tablas que existan en `MCP.MCP_CATALOGO`.

## **Paso 3**

Luego, vemos la última fecha de actualización de las tablas para corroborar la útlima fecha de actualización de las tablas. Para ello corremos el script en la BD `Stage-Datagov_Prod`:

```sql
SELECT * 
FROM MCP.MCP_FECHA_PROCESO 
WHERE ID_CATALOGO IN (
    SELECT ID_CATALOGO 
    FROM MCP.MCP_CATALOGO 
    WHERE NOMBRE_TABLA = '<nombreTabla>' 
    AND NOMBRE_OWNER = '<nombreOwner>'
)
```

Donde `<nombreTabla> = TABLE_NAME` y `<nombreOwner> = TABLE_SCHEMA` según la información recuperada en el **Paso 0**.

## **Paso 4**

Luego, seteamos la última y la próxima fecha de actulización de las tablas, tanto para la malla ADL como para la malla STG. Lo siguiente hará que se actualicen los datos en lsa tablas de la BD `Stage-Datagov_Prod` una vez que se ejecute el pipeline:

```sql
UPDATE MCP.MCP_FECHA_PROCESO 
SET   FCH_ULT_ACTUALIZACION_ADL = '1900-01-01'
    , FCH_PROX_ACTUALIZACION_ADL = (SELECT CAST(GETDATE() AS DATE)) -- Fecha del día en que hacemos la carga
    , FCH_ULT_ACTUALIZACION_STG = '1900-01-01'
    , FCH_PROX_ACTUALIZACION_STG = (SELECT CAST(GETDATE() AS DATE)) -- Fecha del día en que hacemos la carga    
WHERE ID_CATALOGO IN (
    SELECT ID_CATALOGO 
    FROM MCP.MCP_CATALOGO 
    WHERE NOMBRE_TABLA = '<nombreTabla>'
    AND NOMBRE_OWNER = '<nombreOwner>'
)
```

## **Paso 5**

Una vez configurada la fecha de actualización debemos ir al Azure Data Factory de producción y en la [FASE 2](https://adf.azure.com/en-us/authoring/pipeline/Malla%20General?factory=%2Fsubscriptions%2F2e9b712f-7bd2-4ee5-9dba-8da83bc457fb%2FresourceGroups%2Fetl-prod%2Fproviders%2FMicrosoft.DataFactory%2Ffactories%2Fgobdi-df-prod) ejecutamos, primero, la malla ADL y luego la malla STG:

![](/mallaspipeline.png)

## **Paso 6**

Para asegurarnos que las tablas se modicaron en la fecha que hicimos el update corremos lo siguiente en la BD `Stage-Datagov_Prod`:

```sql
SELECT [name]
    , create_date
    , modify_date
    , schema_id
    , SCHEMA_NAME(schema_id)
FROM sys.objects
WHERE SCHEMA_NAME(schema_id) = '<nombreOwner>'
ORDER BY modify_date DESC
```


Para ver el log del proceso corremos:

```sql
SELECT TOP 50 * 
FROM [MCP].[MCP_LOG_PROCESO_BULK] 
ORDER BY ID_LOG_PROCESO DESC
```