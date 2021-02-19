---
weight: 5
title: "Malla MDM"
---

# **Malla MDM**

La Malla MDM se encarga de consolidar la información que se tiene sobre entidades relevantes, que actualmente se encuentra almacenada en diversos sistemas de la Universidad. Para esto se unifican los registros correspondientes y se almacena el registro consolidado (golden record), junto a las llaves necesarias para acceder a los registros originales, en el schema `MDIUC`.

El pipeline del proceso de la malla MDM se encuentra en la ruta [`Fase 1/NORMALIZADO/MCP_MDIUC`](https://adf.azure.com/en-us/authoring/pipeline/MCP_MDIUC?factory=%2Fsubscriptions%2F2e9b712f-7bd2-4ee5-9dba-8da83bc457fb%2FresourceGroups%2FETL%2Fproviders%2FMicrosoft.DataFactory%2Ffactories%2Fdatagov-df-devOps) de la rama `feature_mcp_mdiuc`.

# **Pipelines de la malla MDM**

Los pipelines que utiliza la malla MDM se detallan a continuación.

## **MCP_MDIUC**

Este pipelines revisa cuáles son las entidades maestras a procesar, registradas en la tabla `dev_stage_datagov.MCP_MDIUC.ENTITY`, cada una de las cuales se procesa en el pipeline [`PROCESS_ENTITY`](https://adf.azure.com/en-us/authoring/pipeline/PROCESS_ENTITY?factory=%2Fsubscriptions%2F2e9b712f-7bd2-4ee5-9dba-8da83bc457fb%2FresourceGroups%2FETL%2Fproviders%2FMicrosoft.DataFactory%2Ffactories%2Fdatagov-df-devOps)

## **PROCESS_ENTITY**

Verifica si se trata de una entidad que tiene una fuente oficial de datos. Si la tiene, se ejecuta el pipeline [`ENTITY_WITH_OFFICIAL_SOURCE`](https://adf.azure.com/en-us/authoring/pipeline/ENTITY%20WITH%20OFFICIAL%20SOURCE?factory=%2Fsubscriptions%2F2e9b712f-7bd2-4ee5-9dba-8da83bc457fb%2FresourceGroups%2FETL%2Fproviders%2FMicrosoft.DataFactory%2Ffactories%2Fdatagov-df-devOps)

## **ENTITY_WITH_OFFICIAL_SOURCE**

Ejecuta el Databricks Notebook [`match_with_official_source`](https://adb-203759025778857.17.azuredatabricks.net/?o=203759025778857#notebook/1505414730130282/command/4189266862738535), que registra el mapeo de una entidad con el registro de la fuente oficial en la tabla `dev_stage_datagov.MDIUC.<entityName>_TRANSLATION`. Si no existe un dato oficial que calce con un registro, este útlimo se ingresa en la tabla `dev_stage_datagov.MDIUC.UNMATCHED_<entityName>`.

Las reglas para encontrar pares entre los sistemas se encuentran en el Databricks Notebook.

## **ENTITY_WITHOUT_OFFICIAL_SOURCE**

1. Prepara las tablas auxiliares que se utilizarán en el proceso. Las tablas viven en la BD `dev_stage_datagov` y son las siguientes:

    | Tabla | Descripción |
    |:-- | :-- |
    | `MCP_MDIUC.PROCESSING_<entityName>` | almacena los datos agrupados de distintos orígenes |
    | `MCP_MDIUC.PROCESSED_<entityName>` | almacena los registros procesados que serán ingresados en el golden record |
    | `MCP_MDIUC.INVALID_GROUP_<entityName>` | almacena los `id` de los grupos que no son válidos, es decir, no corresponden a la misma entidad |

2. Copia los datos de distintos orígenes a la tabla `MCP_MDIUC.PROCESSING_<entityName>`.

3. Asigna un `id` de grupo a los registros de `MCP_MDIUC.PROCESSING_<entityName>` utilizando los criterios en la tabla `MCP_MDIUC.GROUPING_RULE`.

4. Identifica si el grupo no necesita ser validado. Esto puede ocurrir porque el grupo o bien contiene un solo registro, o porque los registros dentro del grupo son totalmente iguales.

5. Mueve los registros que no necesitan ser validados a la tabla `MCP_MDIUC.PROCESSED_<entityName>`.

6. Procesa los grupos que necesitan ser validados en el Databricks Notebook [`Verify Groups`](https://adb-203759025778857.17.azuredatabricks.net/?o=203759025778857#notebook/1480857689989693/command/2362756711463722). Este Notebook se encarga de escribir en las tablas `MCP_MDIUC.PROCESSED_<entityName>` y `MCP_MDIUC.INVALID_GROUP_<entityName>` utilizando los criterios de la tabla `MCP_MDIUC.GROUPING_RULE`.

7. Actualiza los datos del golden record en el esquema `dev_stage_datagov.MDIUC` según los nuevos datos recién procesados en la tabla `MCP_MDIUC.PROCESSED_<entityName>`.

# **Diagrama de los procesos de la malla MDM**

Los procesos asociados a la malla MDM se muestran en la siguiente imagen:

![](/mdm.png)

# **Tablas utilizadas en el proceso de la malla MDM**

A continuación, las tablas utilizadas en el proceso. Las tablas viven en el esquema `dev_stage_datagov.MCP_MDIUC`.

## `ENTITY`

Tabla con las entidades de registros maestros que se procesan en la malla MDM.

| Atributo | Descripción |
| :-- | :-- |
| `entity_id` | id único de una entidad |
| `name` | nombre de la entidad (igual al nombre de la tabla en el esquema `dev_stage_datagov.MDIUC`) |
| `is_valid` | indica si se debe procesar una entidad al ejecutar la malla |
| `has_oficcial_source` | indica si el procesamiento de una entidad requiere de datos externos (fuente oficial de datos) |

## `ENTITY_ATTRIBUTE`

Registra los atributos que se almacenan en el golden record de una entidad.

| Atributo | Descripción |
| :-- | :-- |
| `entity_attribute_id` | id único del registro |
| `entity_id` | id de la entidad de la tabla `MCP_MDIUC.ENTITY` a la que se hace referencia |
| `attribute_name` | nombre del atributo |
| `attribute_type` | tipo de dato del atributo; puede ser `int`, `nvarchar` o `datetime` |
| `is_primary_key` | indica si el atributo es parte de la primary key de la entidad en su tabla en `dev_stage_datagov.MDIUC` |

## `ENTITY_SYSTEM_TABLE`

Registra los atributos que se almacenan en el golden record de una entidad.

| Atributo | Descripción |
| :-- | :-- |
| `entity_system_table_id` | id único del registro |
| `entity_id` | id de la entidad de la tabla `MCP_MDIUC.ENTITY` a la que se hace referencia |
| `schema_name` | nombre del esquema en el que se encuentra la tabla |
| `table_name` | nombre de la tabla que almacena los registros de una entidad |
| `primary_key` | primary key de la tabla en `table_name`; se utiliza para hacer referencia entre el registro y el golden record |

## `ENTITY_ATTRIBUTE_SYSTEM_MAPPING`

Registra los atributos que se almacenan en el golden record de una entidad.

| Atributo | Descripción |
| :-- | :-- |
| `entity_attribute_system_mapping_id` | id único del registro |
| `entity_attribute_id` | id de un atributo del golden record que hace referencia a  `MCP_MDIUC.ENTITY_ATTRIBUTE` |
| `entity_system_table_id` | id de la tabla que contiene información sobre la entidad; hace referencia a `MCP_MDIUC.ENTITY_SYSTEM_TABLE` | 
| `column_name` | nombre de la columna del atributo deseado |
| `attribute_rank` | prioridad del origen del atributo al momento de armar el golden record |

## `GROUPING_RULE`

Describe los atributos que se utilizarán para agrupar posibles registros que corresponden a la misma entidad.

Si se ingresan los atributos A, B y C, los registros que tengan el mismo valor en el atributo A, el mismo valor en el atributo B y el mismo valor en el atributo C, se agruparán.

| Atributo | Descripción |
| :-- | :-- |
| `grouping_rule_id` | id único del registro |
| `entity_attribute_id` | id del atributo que se utiliza en la agrupación; hace referencia a `MCP_MDIUC.ENTITY_ATTRIBUTE` |

## `COMPARING_RULE`

Describe las reglas que aplicarán durante la comparación de los registros de un mismo grupo para determinar si corresponden a la misma entidad.

| Atributo | Descripción |
| :-- | :-- |
| `comparing_id` | id único del registro |
| `entity_attribute_id` | id del atributo utilizado en la comparación; hace referencia a `MCP_MDIUC.ENTITY_ATTRIBUTE`  |
| `rule_type` | tipo de regla que se aplica sobre los valores durante la comparación |
| `parameter` | parámetros que puedan necesitar el tipo de comparación |
| `process` | procesamiento que necesiten los atributos a comparar |

Para el atributo **`rule_type`** existen tres rglas de comparación:

| Regla | Descripción |
| :-- | :-- |
| `exact` | ambos atributos deben ser exactamente iguales |
| `fuzzy` | compara atributos utilizando el algoritmo [distancia de edición](https://es.wikipedia.org/wiki/Distancia_de_Levenshtein#:~:text=La%20distancia%20de%20Levenshtein%2C%20distancia,y%20ciencias%20de%20la%20computaci%C3%B3n.). En `parameter` se ingresa el porcentaje de diferencia mínimo necesario para el match |
| `fuzzy_allow_empty` | mismo comportamiento que `fuzzy`, pero, adicionalmente, considera una comparación positiva al comparar con un valor nulo |

Para el atributo **`process`** existe solo un valor posible:

| Regla | Descripción |
| :-- | :-- |
| `concat` | antes de realizar la comparación, concatena todos los valores que tengan el atributo `concat` en el campo `process`. Por ejemplo, si los atributos A, B, C tiene el valor `concat` en el campo `process`, al momento de comparar la comparación no se hará de manera individual; la comparación se realizará con el valor resultante de la concatenación de A, B y C. Los valores nulos se consideran strings vacíos | 

# **Pasos para ingresar una entidad a la malla MDM**

| Paso | Descripción |
| :-- | :-- |
| 0 | Paso previo a la malla. Se debe crear la tabla de la entidad en el esquema `dev_stage_datagov.MDIUC` que es donde se guardarán los datos al finalizar los procesos de la malla |
| 1 | Ingresar los datos de la entidad a `MCP_MDIUC.ENTITY` |
| 2 | Ingresar datos de los atributos a `MCP_MDIUC.ENTITY_ATTRIBUTE` |
| 3 | Ingresar datos de las tablas que se deben revisar a `MCP_MDIUC.ENTITY_SYSTEM_TABLE` |
| 4 | ngresar equivalencia entre los atributos de las tablas y la entidad a `MCP_MDIUC.ENTITY_ATTRIBUTE_SYSTEM_MAPPING` |
| 5 | Ingresar reglas de agrupación a `MCP_MDIUC.GROUPING_RULE` |
| 6 | Ingresar reglas de comparación a `MCP_MDIUC.COMPARING_RULE` |
| 7 | Ejecutar malla en Azure DataFactory `MCP_MDIUC` |


# **Pruebas de la malla**

Para probar la malla existen las tablas `NORMALIZADO_TEST.PEOPLE_SOFT_PERSON` y `NORMALIZADO_TEST.BANNER_PERSON`.

Cada tabla contiene el RUT, dígito verificador, apellido paterno y materno, y el nombre. Los registros de estas tablas de extrajeron de Banner y PeopleSoft respectivamente.

Cada tabla contiene 130 registros. Existen 100 RUTs que aparecen en ambas tablas . Los 30 registros restantes tienen RUTs exclusivos de esa tabla, es decir, no aparecen en la otra tabla.