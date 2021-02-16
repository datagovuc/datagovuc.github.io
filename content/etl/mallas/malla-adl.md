---
weight: 1
title: "Malla ADL"
---


# **Definición general**

El proceso de datalake consiste en traer distintos documentos, tablas y archivos y depositarlos en un contenedor del datalake. En particular se depositan en el contenedor raw en la siguiente ruta:

```bash
raw/nombre_origen/nombre_sistema/nombre_owner/nombre_tabla/año/mes/dia/nombre_tabla_año_mes_dia.csv
```



## **Proceso de carga a datalake**

### **1. Verificar y cargar en MCP.MCP_CATALOGO**

Si la tabla exista, se debe verificar que la columna `FLG_ADL` mantenga el valor 1.

Si la tabla no existe, se debe realizar una inserción a la tabla de catálogo de la siguiente forma:

**Forma general**

```sql
INSERT INTO MCP.MCP_CATALOGO (ORIGEN_SISTEMA,NOMBRE_SISTEMA,NOMBRE_OWNER,NOMBRE_TABLA,DESC_TABLA) 
VALUES('ORIGEN_1','SISTEMA_1','ESQUEMA_1','TABLA_1','Descripción de la tabla 1');
```

**Ejemplo**

```sql
INSERT INTO MCP.MCP_CATALOGO (ORIGEN_SISTEMA,NOMBRE_SISTEMA,NOMBRE_OWNER,NOMBRE_TABLA,DESC_TABLA) 
VALUES('ORACLE','UCLIBE','ADMISION','INSTITUCION_RESP','tabla que registra las instituciones...');
```


### 2. Verificar/actualizar FLG

Como se mencionó anteriormente en caso de que exista la tabla en el catalogo solo se debe actualizar flag.

```sql
Update MCP.MCP_CATALOGO
SET FLG_ADL = 1
WHERE NOMBRE_TABLA = 'TABLA_1' AND NOMBRE_OWNER = 'ESQUEMA_1'
```


### **3. Verificar en ETL si existe el origen-sistema-owner creado**

Para esto se debe checkear el datafactory dependiendo del ambiente en el que se requiere realizar la carga, adf-dev o adf-prod.

Una vez dentro del datalake, se debe dirigir al pipeline:

```console
FASE 2 > Malla General > Malla ADL
```

Dentro de este pipeline se debe chequear que exista el ORIGEN para el cual se va a realizar la carga.

#### **3.1 En caso de existir en origen se debe checkear un nivel anidado si existe el SISTEMA en el ETL**

Dentro del pipeline anterior se debe dirigir a:

```console
FASE 2 > Malla General > Malla ADL > ORIGEN
```

Una vez dentro, se debe verificar si existe el sistema (pipeline con el nombre del sistema a cargar)

#### **3.1.1 En caso de existir el sistema**

Si existe el sistema queda por checkear solo si existe el owner para ese sistema

#### **3.1.2 En caso de no existir el sistema**

Se debe crear el pipeline correspondiente con el parametro del nuevo sistema.





