---
weight: 1
title: "Indicadores"
---

# Sesión de Convergencia: Indicadores Banner

A continuación un análisis con los cruces para responder a los indicadores para BANNER.

| ID | Indicador | Definición |
| :--: | :-- | :-- |
| 1 | Promedio Ponderado Acumulado (PPA) | Promedio ponderado de los alumnos a lo largo de su carrera, según la cantidad de créditos de cada curso |
| 2 | Estudiantes según información de admisión | Cantidad de estudiantes en un periodo académico, según la dependencia educacional del colegio del cual egresó |
| 2 | Estudiantes según información de admisión | Cantidad de estudiantes en un periodo académico, según su región o comuna de procedencia | 
| 3 | Estudiantes según situación académica | Cantidad de estudiantes según la situación académica que presentan en cada periodo académico |
| 4 | Retiro de cursos | Cantidad de estudiantes, por periodo académico, que retiran cursos | 
| 4 | Retiro de cursos | Cantidad de cursos retirados, por periodo académico |
| 5 | Estudiantes según nivel del programa | Cantidad de estudiantes vigentes, en cada periodo académico, según el nivel del programa en el que están inscritos |
| 6 | Deserción por carrera | Tasa de alumnos que desertan por cohorte, en cada periódo académico |
| 7 | Estudiantes con cursos inscritos según nivel del curso | Cantidad de estudiantes con cursos inscritos, según el nivel del curso |
| 8 | Carga académica | Cantidad de cursos inscritos por los estudiantes según el o los programas en los que está adscrito, en un determinado periodo académico |
| 8 | Carga académica | Cantidad de créditos inscritos por los estudiantes según el o los programas en los que está adscrito, en un determinado periodo académico |
| 9 | Estudiantes en carreras paralelas | Cantidad de estudiantes que estudian 2 o más programas en forma simultánea |
| 10 | Estudiantes sin atraso curricular | Cantidad de estudiantes que han aprobado todos sus créditos inscritos |
| 10 | Estudiantes sin atraso curricular | Cantidad de estudiantes, por cohorte, según grado de avance curricular |
| 11 | Tasa de aprobación o reprobación | Tasa de estudiantes total que aprueban o reprueban los cursos inscritos, respecto del total de estudiantes por curso, en un periodo académico |
| 11 | Tasa de aprobación o reprobación | Tasa de estudiantes de 1er año que aprueban o reprueban los cursos inscritos, respecto del total de estudiantes por curso, en un periodo académico |
| 12 | Duración real de la carrera | Promedio de la suma de semestres que tardan los estudiantes, por cohorte, en completar sus programas |
| 13 | Estudiantes que egresan o se titulan oportunamente | Cantidad de estudiantes que finalizan sus programas académicos en hasta 2 semestres más que la duración oficial de la carrera |
| 13 | Estudiantes que egresan o se titulan oportunamente | Tasa de estudiantes que finalizan sus programas académicos en hasta 2 semestres más que la duración oficial de la carrera, respecto del total de la cohorte |



## **1. Promedio Ponderado Acumulado (PPA)**
### **Promedio ponderado de los alumnos a lo largo de su carrera, según la cantidad de créditos de cada curso**

```sql
SELECT
    rut
  , semestre
  , ppa
FROM SC.STUDENT_360_PPA_PPS_PAS
```

## **2.  Estudiantes según información de admisión**
### **Cantidad de estudiantes en un periodo académico, según la dependencia educacional del colegio del cual egresó**

```sql
SELECT
    periodo_academico
  , school_financing_type_name
  , COUNT(*) AS num_estudiantes
FROM SC.STUDENT_360_PAS
WHERE school_financing_type_name IS NOT NULL
GROUP BY 
    periodo_academico
  , school_financing_type_name
ORDER BY periodo_academico DESC
```

### **Cantidad de estudiantes en un periodo académico, según su región o comuna de procedencia**

```sql
SELECT
    periodo_academico
  , region
  , COUNT(*) AS num_estudiantes
FROM SC.STUDENT_360_PAS
WHERE region IS NOT NULL
GROUP BY 
    periodo_academico
  , region
ORDER BY 
    periodo_academico DESC
```


## **3. Estudiantes según situación académica**
### **Cantidad de estudiantes según la situación académica que presentan en cada periodo académico**

Tener en cuentar que el statement `ORDER BY` es "costoso" en términos de recursos, por lo que usarlo implica que la query va a tardar en devolver resultados.

```sql
SELECT
    academic_status_validity_name  -- nombre del estado: vigente, no vigente, vigente sin cursos
  , periodo_academico
  , COUNT(*) AS num_estudiantes
FROM SC.STUDENT_360_PAS
GROUP BY 
    academic_status_validity_name
  , periodo_academico
ORDER BY periodo_academico DESC
```

## **4. Retiro de cursos**
### **Cantidad de estudiantes, por periodo académico, que retiran cursos**

```sql
SELECT
    registration_type_name
  , periodo_academico
  , count(*) AS num_estudiantes
FROM SC.STUDENT_360_PAS
WHERE registration_type_id IN ('DX', 'DD', 'DW', 'DS')
GROUP BY 
    registration_type_name
  , periodo_academico
ORDER BY periodo_academico DESC
```

### **Cantidad de cursos retirados, por periodo académico**

Cantidad de cursos retirados por cada período académico de acuerdo al tipo de retiro:

```sql
SELECT
    periodo_academico
  , registration_type_name
  , count(course_id) AS num_cursos_retirados
FROM SC.STUDENT_360_PAS
WHERE course_id IS NOT NULL
  AND registration_type_id IN ('DX', 'DD', 'DW', 'DS')
GROUP BY 
    periodo_academico
  , registration_type_name
ORDER BY periodo_academico DESC
```

Cantidad de curso retirados por estudiante por cada período académico de acuerdo al tipo de retiro:

```sql
SELECT
    rut
  , periodo_academico
  , registration_type_name
  , count(course_id) AS num_cursos_retirados
FROM SC.STUDENT_360_PAS
WHERE course_id IS NOT NULL
  AND registration_type_id IN ('DX', 'DD', 'DW', 'DS')
GROUP BY 
    rut
  , periodo_academico
  , registration_type_name
ORDER BY periodo_academico DESC
```

## **5. Estudiantes según nivel del programa**
### **Cantidad de estudiantes vigentes, en cada periodo académico, según el nivel del programa en el que están inscritos**

```sql
SELECT
    cohorte
  , academic_level_name
  , count(*) AS num_estudiantes_vigentes
FROM SC.STUDENT_360_PAS
WHERE academic_level_id IS NOT NULL
  AND academic_status_validity = 'AS'
GROUP BY 
    cohorte
  , academic_level_name
ORDER BY periodo_academico DESC
```

## **6. Deserción por carrera**
### **Tasa de alumnos que desertan por cohorte, en cada periódo académico**

Cantidad de alumnos desertados por período académico:

```sql
SELECT
    cohorte
  , periodo_academico
  , count(*) AS num_estudiante_desertados
FROM SC.STUDENT_360_PAS
WHERE academic_status_desc IN ('RENUNCIADO', 'ABANDONA', 'NOVIGCOMPERMA', 'ELIMINADO')
  AND periodo_academico > 201902 -- solo nos interesa el perido académico actual (2020)
GROUP BY 
    cohorte
  , periodo_academico
ORDER BY periodo_academico, cohorte DESC
```

## **7. Estudiantes con cursos inscritos según nivel del curso**
### **Cantidad de estudiantes con cursos inscritos, según el nivel del curso**

```sql
SELECT
    periodo_academico
  , academic_level_name
  , COUNT(DISTINCT rut) AS num_estudiantes
FROM SC.STUDENT_360_PAS
WHERE academic_status_desc = 'INPROGRESS'
  AND registration_type_id IN ('RW', 'RE', 'RQ', 'RS')
  AND academic_level_name IS NOT NULL
GROUP BY 
    periodo_academico
  , academic_level_name
ORDER BY periodo_academico DESC
```

## **8. Carga académica**
### **Cantidad de cursos inscritos por los estudiantes según el o los programas en los que está adscrito, en un determinado periodo académico**

```sql
SELECT
    periodo_academico
  , academic_program_name
  , COUNT(*) AS num_cursos
FROM SC.STUDENT_360_PAS
WHERE registration_type_id IN ('RW', 'RE', 'RQ', 'RS')
GROUP BY 
    periodo_academico
  , academic_program_name
ORDER BY periodo_academico DESC
```

### **Cantidad de créditos inscritos por los estudiantes según el o los programas en los que está adscrito, en un determinado periodo académico**

```sql
SELECT
    periodo_academico
  , academic_program_name
  , SUM(credits) AS creditos_totales
FROM SC.STUDENT_360_PAS
WHERE registration_type_id IN ('RW', 'RE', 'RQ', 'RS')
GROUP BY 
    periodo_academico
  , academic_program_name
ORDER BY periodo_academico DESC
```

## **9. Estudiantes en carreras paralelas**
### **Cantidad de estudiantes que estudian 2 o más programas en forma simultánea**

Result set para quienes tienen tipo de admisión `Carrera Paralela`:

```sql
SELECT
    periodo_academico
  , academic_level_name
  , COUNT(*) as num_estudiantes
FROM SC.STUDENT_360_PAS
WHERE admission_type_name = 'Carrera Paralela'
  AND academic_status_validity = 'AS'
  AND academic_level_name IS NOT NULL
GROUP BY 
    periodo_academico
  , academic_level_name
ORDER BY periodo_academico DESC
```

<!-- PENDING
Número de alumnos que están vigentes en 2 o más programas en un mismo periodo académico. Se puede distinguir en el recuento según la combinación de niveles de los programas (por ej: ambos de pregrado, o uno de pregrado y otro de magíster). -->


## **10. Estudiantes sin atraso curricular**
### **Cantidad de estudiantes que han aprobado todos sus créditos inscritos**

```sql
SELECT
    periodo_academico
  , academic_level_name
  , COUNT(distinct enrollment_id) as num_estudiantes
FROM SC.STUDENT_360_PAS
WHERE registration_type_id IN ('RS', 'RQ', 'RE', 'RW')
  AND CAST(course_grade AS FLOAT) >= 4
  AND course_grade IS NOT NULL
  AND academic_level_name IS NOT NULL
GROUP BY 
    periodo_academico
  , academic_level_name
ORDER BY periodo_academico DESC
```

### **Cantidad de estudiantes, por cohorte, según grado de avance curricular**

<!-- VER COMO METER ESTO A LA VISTA O GENERAR UNA NUEVA VISTA
Cada estudiante tiene un porcentaje de avance curricular, que corresponde al cociente entre la suma de créditos aprobados pertenecientes al programa y el total de créditos que exige el programa. Al visualizar el dato, se muestra según tramos de avance: menor a 30%, 30-50%, 50-60%, 60-80%, 80-100%. Se puede distinguir en el recuento por nivel del estudiante: pregrado, postítulo, magíster o doctorado. 
-->

## **11. Tasa de aprobación o reprobación**
### **Tasa de estudiantes total que aprueban o reprueban los cursos inscritos, respecto del total de estudiantes por curso, en un periodo académico**

<!-- A nested query, I know. Queda pendiente poner esta lógica en el cubo (la sábana ya es bastante costosa dada la cantidad de joins). También queda pendiente la tasa. -->

<!-- ```sql
SELECT
      subquery.periodo_academico
    , subquery.academic_level_name
    , subquery.estado_curso
    , COUNT(distinct enrollment_id) as num_estudiantes
FROM (
      SELECT
          periodo_academico
        , academic_level_name
        , enrollment_id
        , CASE 
            WHEN CAST(course_grade AS FLOAT) >= 4 THEN 'aprobado'
            WHEN CAST(course_grade AS FLOAT) < 4 THEN 'reprobado' 
          END AS estado_curso
      FROM SC.STUDENT_360_PAS
      WHERE registration_type_id IN ('RS', 'RQ', 'RE', 'RW')
        AND course_grade IS NOT NULL
        AND academic_level_name IS NOT NULL
      ) AS subquery
GROUP BY 
    subquery.periodo_academico
  , subquery.academic_level_name
  , subquery.estado_curso 
ORDER BY periodo_academico DESC
``` -->

Para calcular la tasa de aprobación/reprobación, agregué una columna que tiene el número de aprobados/reprobados.

```sql
SELECT
    periodo_academico
  , academic_level_name
  , estado_curso
  , COUNT(distinct enrollment_id) as num_estudiantes
FROM SC.STUDENT_360_PAS
WHERE registration_type_id IN ('RS', 'RQ', 'RE', 'RW') --cursos inscritos only
  AND course_grade IS NOT NULL
  AND academic_level_name IS NOT NULL
GROUP BY 
    periodo_academico
  , academic_level_name
  , estado_curso
ORDER BY periodo_academico DESC
```

### **Tasa de estudiantes de 1er año que aprueban o reprueban los cursos inscritos, respecto del total de estudiantes por curso, en un periodo académico**

<!-- También queda pendiente la tasa. Sori. -->

<!-- ```sql
SELECT top 10
      subquery.periodo_academico
    , subquery.academic_level_name
    , subquery.estado_curso
    , COUNT(distinct enrollment_id) as num_estudiantes
FROM (
      SELECT
          periodo_academico
        , academic_level_name
        , enrollment_id
        , CASE 
            WHEN CAST(course_grade AS FLOAT) >= 4 THEN 'aprobado'
            WHEN CAST(course_grade AS FLOAT) < 4 THEN 'reprobado' 
          END AS estado_curso
      FROM SC.STUDENT_360_PAS
      WHERE registration_type_id IN ('RS', 'RQ', 'RE', 'RW')
        AND LEFT(cohorte, 4) = CAST(year AS NVARCHAR)
        AND course_grade IS NOT NULL
        AND academic_level_name IS NOT NULL
      ) AS subquery
GROUP BY 
    subquery.periodo_academico
  , subquery.academic_level_name
  , subquery.estado_curso 
ORDER BY periodo_academico DESC
``` -->

Para calcular la tasa de aprobación/reprobación, agregué una columna que tiene el número de aprobados/reprobados.

```sql
SELECT
    periodo_academico
  , academic_level_name
  , estado_curso
  , COUNT(distinct enrollment_id) as num_estudiantes
FROM SC.STUDENT_360_PAS
WHERE registration_type_id IN ('RS', 'RQ', 'RE', 'RW') --cursos inscritos only
  AND LEFT(cohorte, 4) = CAST(year AS NVARCHAR)
  AND course_grade IS NOT NULL
  AND academic_level_name IS NOT NULL
GROUP BY 
    periodo_academico
  , academic_level_name
  , estado_curso
ORDER BY periodo_academico DESC
```


{{< hint danger >}}
**DISCLAIMER**  
Para responder a los indicadores en **12** y **13** se necesita el dato de duración de la carrera que viene en la tabla `NORMALIZADO_BANNER.ACADEMIC_PROGRAM`. Sin embargo, el campo `duration` viene con NULLs.
{{< /hint >}}


## **12. Duración real de la carrera**
### **Promedio de la suma de semestres que tardan los estudiantes, por cohorte, en completar sus programas**

<!-- 
Sólo se consideran en la suma los semestres en que los estudiantes están con situación académica "In progress", con cursos. No se cuentan los periodos en que el alumno está suspendido, anulado, en abandono o renunciado. No deben promediarse programas distintos, ya que la duración estimada podría ser distinta. Sólo entran en el recuento los alumnos que ya han terminado sus programas. Se puede distinguir según cohortes de ingreso o titulación, vías de ingreso, y según si duración hasta el egreso o hasta titulación. 
-->

## **13. Estudiantes que egresan o se titulan oportunamente**
### **Cantidad de estudiantes que finalizan sus programas académicos en hasta 2 semestres más que la duración oficial de la carrera**

<!-- 
Para calcular cuánto se demoró un estudiante en completar un programa se considera la suma de los semestres en que el estudiante está con situación académica "In progress", con cursos inscritos, en ese programa. No se cuentan los periodos en que el alumno está suspendido, anulado, en abandono o renunciado. Sólo entran en el recuento los alumnos que ya han terminado sus programas. Se puede realizar el recuento por cohortes, o la suma histórica hasta cierto período académico. Se distingue también según si el estudiante egresa o se titula, y según vía de admisión del estudiante (incluye si el estudiante pasó desde College o no, por traspaso o articulación). Por último, se diferencia si los programas tienen sólo licenciatura como máximo (y no un título), o no.
-->

### **Tasa de estudiantes que finalizan sus programas académicos en hasta 2 semestres más que la duración oficial de la carrera, respecto del total de la cohorte**

<!-- 
Para calcular cuánto se demoró un estudiante en completar un programa se considera la suma de los semestres en que el estudiante está con situación académica "In progress", con cursos inscritos, en ese programa. No se cuentan los periodos en que el alumno está suspendido, anulado, en abandono o renunciado. Sólo entran en el recuento los alumnos que ya han terminado sus programas. Se puede realizar el recuento por cohortes (una o más). La tasa corresponde al cociente entre los alumnos que han terminado en una duración oportuna y aquellos que han excedido esta duración. Se distingue también según si el estudiante egresa o se titula, y según vía de admisión del estudiante (incluye si el estudiante pasó desde College o no, por traspaso o articulación). Por último, se diferencia si los programas tienen sólo licenciatura como máximo (y no un título), o no.
-->