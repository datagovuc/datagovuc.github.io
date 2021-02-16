---
weight: 1
# bookFlatSection: true
title: "Metadata"
---

# Queries para obtener la data relativa de Banner Normalizado

{{< highlight tsql "linenos=table, style=monokailight">}}
EXECUTE AS USER = 'psotou@uc.cl';

WITH all_tables_saturn AS(
SELECT name
		,object_id
		,principal_id
		,schema_id
		,SCHEMA_NAME(schema_id) as schema_name
		,type
		,type_desc
		,create_date
		,modify_date
		,is_ms_shipped
		,is_published
		,is_schema_published
FROM sys.objects where SCHEMA_NAME(schema_id) = 'SATURN' AND type IN ('U')
)

, all_tables_general AS ( --27 filas general

SELECT name
		,object_id
		,principal_id
		,schema_id
		,SCHEMA_NAME(schema_id) as schema_name
		,type
		,type_desc
		,create_date
		,modify_date
		,is_ms_shipped
		,is_published
		,is_schema_published
FROM sys.objects where SCHEMA_NAME(schema_id) = 'GENERAL' AND type IN ('U')

)


, all_object_banner AS ( --280 objetos (triggers, SP's, tables,primary_keys y constrains---
SELECT name
		,object_id
		,principal_id
		,schema_id
		,SCHEMA_NAME(schema_id) as schema_name
		,type
		,type_desc
		,create_date
		,modify_date
		,is_ms_shipped
		,is_published
		,is_schema_published
FROM sys.objects where SCHEMA_NAME(schema_id) = 'NORMALIZADO_BANNER' 
)

, all_tables_banner AS ( --46 tablas banner 

SELECT name
		,object_id
		,principal_id
		,schema_id
		,SCHEMA_NAME(schema_id) as schema_name
		,type
		,type_desc
		,create_date
		,modify_date
		,is_ms_shipped
		,is_published
		,is_schema_published
FROM sys.objects where SCHEMA_NAME(schema_id) = 'NORMALIZADO_BANNER' AND type IN ('U')

--select * FROM sys.objects where SCHEMA_NAME(schema_id) = 'NORMALIZADO_BANNER' AND type IN ('P','X','RF','PC')

)

, all_procedures_banner AS ( --95 sp's--
SELECT * 
FROM sys.procedures
WHERE SCHEMA_NAME(schema_id) = 'NORMALIZADO_BANNER'

)


, all_dependencies_sp AS (  --358 referencias--

SELECT
	--- ENTIDAD REFERENCIADORA
	OBJECT_SCHEMA_NAME ( referencing_id ) AS referencing_schema_name,  
    OBJECT_NAME(referencing_id) AS referencing_entity_name,
	o.type AS referencing_type,
    o.type_desc AS referencing_desciption,   
    COALESCE(COL_NAME(referencing_id, referencing_minor_id), '(n/a)') AS referencing_minor_id,   
    referencing_class_desc,
	---ENTIDAD REFERENCIADA 
	referenced_class_desc,  
    referenced_server_name, referenced_database_name, referenced_schema_name,  
    referenced_entity_name,
	o2.type AS referenced_type,
    o2.type_desc AS referenced_desciption, 
    COALESCE(COL_NAME(referenced_id, referenced_minor_id), '(n/a)') AS referenced_column_name,  
    is_caller_dependent, is_ambiguous  
FROM sys.sql_expression_dependencies AS sed  
INNER JOIN sys.objects AS o ON sed.referencing_id = o.object_id
LEFT JOIN sys.objects AS o2 ON sed.referenced_id = o2.object_id
WHERE OBJECT_SCHEMA_NAME ( referencing_id ) = 'NORMALIZADO_BANNER' and o.type IN ('P','X','RF','PC') --and OBJECT_NAME(referencing_id) = 'UPDATE_ACADEMIC_PROGRAM' --and OBJECT_NAME(referenced_id) = '__academic_situation'

)


/*** QUERY PARA OBTENER TABLAS DE INPUT DEL MODELO
SELECT DISTINCT referenced_schema_name,referenced_entity_name FROM all_dependencies_sp where referenced_schema_name NOT IN ('NORMALIZADO_BANNER','MCP')
***/


/*** QUERY PARA OBTENER TABLAS DE OUTPUT DEL MODELO
SELECT DISTINCT referenced_schema_name,referenced_entity_name FROM all_dependencies_sp where referenced_schema_name IN ('NORMALIZADO_BANNER')
***/


-------------------------------------------------------------------------------------------
--SELECT * FROM all_tables_saturn
--SELECT * FROM all_tables_general
--SELECT * FROM all_object_banner
--order by TYPE

--IMPORTANTE NO USAR sys.sql_dependencies
---This feature will be removed in a future version of Microsoft SQL Server. Avoid using this feature in new development work, and plan to modify applications that currently use this feature. Use sys.sql_expression_dependencies instead.



-- ESTA FUNCION NO ESTA EN DW SYNAPSE
--SELECT * FROM sys.dm_sql_referenced_entities

--select * FROM sys.sql_expression_dependencies

--GLOSARIO
--referencing_id => entidad que referencia
--referenced_id => entidad referenciada

-------------------------------ENTIDADES QUE SON REFERENCIADAS---------------------------
 
--SELECT OBJECT_NAME(referencing_id) AS referencing_entity_name,
--	o.type AS referencing_type,
--    o.type_desc AS referencing_desciption,   
--    COALESCE(COL_NAME(referencing_id, referencing_minor_id), '(n/a)') AS referencing_minor_id,   
--    referencing_class_desc,  
--    referenced_server_name, referenced_database_name, referenced_schema_name,  
--    referenced_entity_name,   
--    COALESCE(COL_NAME(referenced_id, referenced_minor_id), '(n/a)') AS referenced_column_name,  
--    is_caller_dependent, is_ambiguous  
--FROM sys.sql_expression_dependencies AS sed  
--INNER JOIN sys.objects AS o ON sed.referencing_id = o.object_id  
--WHERE OBJECT_SCHEMA_NAME ( referencing_id ) = 'NORMALIZADO_BANNER' and o.type IN ('P','X','RF','PC')
--GO

-------------------------------ENTIDADES QUE REFERENCIAN---------------------------

--SELECT OBJECT_SCHEMA_NAME ( referencing_id ) AS referencing_schema_name,  
--    OBJECT_NAME(referencing_id) AS referencing_entity_name,
--	o.type AS referencing_type,
--    o.type_desc AS referencing_desciption,   
--    COALESCE(COL_NAME(referencing_id, referencing_minor_id), '(n/a)') AS referencing_minor_id,   
--    referencing_class_desc, referenced_class_desc,  
--    referenced_server_name, referenced_database_name, referenced_schema_name,  
--    referenced_entity_name,   
--    COALESCE(COL_NAME(referenced_id, referenced_minor_id), '(n/a)') AS referenced_column_name,  
--    is_caller_dependent, is_ambiguous  
--FROM sys.sql_expression_dependencies AS sed  
--INNER JOIN sys.objects AS o ON sed.referencing_id = o.object_id  
--WHERE OBJECT_SCHEMA_NAME ( referencing_id ) = 'NORMALIZADO_BANNER' and o.type IN ('P','X','RF','PC') AND OBJECT_NAME(referencing_id) = 'UPDATE_SCHOOL_FINANCING_TYPE'
--GO


-------------------------------TAMBIEN SE PUEDEN VER VISTAS-TABLAS------------------------------- 

--SELECT OBJECT_SCHEMA_NAME ( referencing_id ) AS referencing_schema_name,  
--    OBJECT_NAME(referencing_id) AS referencing_entity_name,
--	o.type AS referencing_type,
--    o.type_desc AS referencing_desciption,   
--    COALESCE(COL_NAME(referencing_id, referencing_minor_id), '(n/a)') AS referencing_minor_id,   
--    referencing_class_desc, referenced_class_desc,  
--    referenced_server_name, referenced_database_name, referenced_schema_name,  
--    referenced_entity_name,   
--    COALESCE(COL_NAME(referenced_id, referenced_minor_id), '(n/a)') AS referenced_column_name,  
--    is_caller_dependent, is_ambiguous  
--FROM sys.sql_expression_dependencies AS sed  
--INNER JOIN sys.objects AS o ON sed.referencing_id = o.object_id  
--WHERE o.type IN ('V','U')






--SELECT * FROM MCP.CATALOGO_DSP

--, rel_tabla_sp AS (
--SELECT A.*,B.NOMBRE_TABLA,C.NOMBRE_PROCESO
--FROM MCP.MCP_DSP_REL_TABLA_SP A
--LEFT JOIN MCP.MCP_CATALOGO B ON A.ID_CATALOGO = B.ID_CATALOGO
--LEFT JOIN MCP.MCP_DSP_CATALOGO C ON A.ID_CATALOGO_SP = C.ID_CATALOGO_SP
--)


--, rel_sp_sp AS (
--SELECT A.*,B.NOMBRE_PROCESO AS NOMBRE_PROCESO_HIJO,C.NOMBRE_PROCESO AS NOMBRE_PROCESO_PADRE 
--FROM MCP.MCP_DSP_REL_SP_SP A
--LEFT JOIN MCP.MCP_DSP_CATALOGO B ON A.ID_CATALOGO_SP_HIJO = B.ID_CATALOGO_SP
--LEFT JOIN MCP.MCP_DSP_CATALOGO C ON A.ID_CATALOGO_SP_PADRE = C.ID_CATALOGO_SP
--)



--SELECT * 
--FROM rel_sp_sp A

--SELECT referencing_schema_name,referencing_entity_name,referenced_schema_name,referenced_entity_name
--FROM all_dependencies_sp 
--where referenced_type IN ('U') AND referenced_schema_name <> 'MCP' AND referenced_schema_name IN ('SATURN','GENERAL') order BY referenced_schema_name 
{{< / highlight >}}