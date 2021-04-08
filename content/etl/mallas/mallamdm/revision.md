---
weight: 2
title: "revision"
---


# **revision mdm**


/***
# 1. USABILIDAD
+ 1.1 Configuración de pipeline con un grupo normalizado: se requieren (siete insert en cada prueba de configuración) => mayor probabilidad de fuente error humano.
+ 1.2 Te permite solo un grupo limitado de datos para ingresar (datetime,int,nvarchar,varchar) ->errores con float / bit 
+ 1.3 
# RESULTADOS
## 2.1 RENDIMIENTO TABLA PAISES 
	----------PRUEBA 1------------------
			TABLA GOLDEN 249-->
			TABLA DESTINO 145 --> queda con menos datos de los originales apesar de ser golden record, se descartan 104
			145/249 => 58 %

	----------PRUEBA DE CRUCES CON SQL------------
	30 SEGS
			TABLA GOLDEN 249-->
			TABLA DESTINO 249 --> queda con menos datos de los originales apesar de ser golden record, se descartan 104
			188/249 => 75%

## 2.2 RENDIMIENTO TABLA PERSONA 

BANNER,PEOPLESOFT,PERS

***/





# PASO A PRODUCCION GUIA

1.eliminar todo del esquema normalizado_banner : 
		a) tablas -> EXEC  [NORMALIZADO_BANNER].[DROP_ALL_TABLES] ok
		b) sp -> consulta manual de SP's 
		c) utils -> consulta manual de SP's
		d) eliminar-> funciones SQL_SCALAR_FUNCTION
		

2.eliminar todo del esquema normalizado_peoplesoft : 
		a) tablas -> procedure 
		b) sp -> consulta manual de SP's 
		c) utils -> consulta manual de SP's
		d) eliminar funciones-> SQL_SCALAR_FUNCTION

3.eliminar todos los utils del esquema normalizado: (ESTE ES UN ESQUEMA EN COMUN)
		a) utils -> consulta manual de SP's

4.crear del esquema normalizado:
		a) utils -> de la carpeta del github utils : ETL_Datawarehouse\UTILS
		
5.crear del esquema normalizado_banner:
		a) utils -> de la carpeta del github : NORMALIZADO_BANNER\UTILS
		b) tablas -> de la carpeta del github : NORMALIZADO_BANNER ABLES
		c) sp -> de la carpeta del github : NORMALIZADO_BANNER\UTILS

6.crear del esquema normalizado_people_soft:
		a) utils -> de la carpeta del github : NORMALIZADO_PEOPLE_SOFT\UTILS  ok
		b) tablas -> de la carpeta del github : NORMALIZADO_PEOPLE_SOFT ABLES  ok 
		c) sp -> de la carpeta del github : NORMALIZADO_PEOPLE_SOFT\UTILS   ok


7.para verificar cantidad de datos en tablas :
			SELECT * FROM MCP.MCP_ESPACIO_OCUPADO_BD
			WHERE SchemaName = 'NORMALIZADO_PEOPLE_SOFT'

7.1 para verificar existencia de objetos (tablas, triggers,sp's,fk,pk,funciones)
			SELECT * FROM sys.objects 
			where schema_name(schema_id) = 'NORMALIZADO_PEOPLE_SOFT' 
			order by type_desc


8.PASO FINAL PARA CARGAR CON DATOS
		a) obtener listado de tablas que consumen los modelos. para eso ejecutar script => (FALTA ESTANDARIZAR ESE PROCESO)
		b) Actualizar fecha de proceso en tabla MCP.MCP_FECHA_PROCESO => falta estandarizar esto
		c) ejecutar script utils. EXEC [NORMALIZADO_PEOPLE_SOFT].[UPDATE_MCP_DSP]
								  EXEC [NORMALIZADO_BANNER].[UPDATE_MCP_DSP]

		

***/