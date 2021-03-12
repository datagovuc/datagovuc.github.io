---
weight: 1
title: "Indicadores"
---

# Sesión de Convergencia: Indicadores Admisión

A continuación un análisis con los cruces para responder a los indicadores para ADMISIÓN.

| ID | Indicador | Definición |
| :--: | :-- | :-- |
| 1 | Admisión especial, según vía de Equidad. | Número de postulantes de carreras de pregrado, según vía de equidad, en un año-periodo de admisión. |
| 1 | Admisión especial, según vía de Equidad. | Número de postulantes-seleccionados a carreras de pregrado, según vía de equidad en un año-periodo de admisión. |
| 1 | Admisión especial, según vía de Equidad. | Número de estudiantes matriculados en carreras de pregrado , según vía de equidad en un año-periodo de admisión. |
| 2 | Estudiantes-postulantes pertenecientes a una etnia. | Número de estudiantes pertenecientes a una etnia, por año-periodo de admisión. |
| 3 | Postulantes con puntaje nacional, por  año-periodo de admisión. | Número de estudiantes con puntaje nacional, por año-periodo de admisión. |
| 4 | Estudiantes de cursos superiores que postulan a la UC. | Número de estudiantes que no vienen directo de un establecimiento de educación secundaria (año de egreso). |
| 5 | Estudiantes-Postulantes según nacionalidad por año-periodo de admisión. | Número de estudiantres-postulantes según nacionalidad por año-periodo de admisión. |
| 6 | Estudiantes-Postulantes, según IVE del establecimiento de origen, por año-periodo de admisión. | Número de estudiantes- Postulantes, según IVE del establecimiento de origen, por año-periodo de admisión. | 
| 7 | Estudiantes, según carreras de preferencia postulada, por año-periodo de admisión. | Número de estudiantes seleccionados, según carreras de preferencia postulada por año-periodo de admisión. | 
| 7 | Estudiantes, según carreras de preferencia postulada, por año-periodo de admisión. | Número de estudiantes matriculados, según carreras de preferencia postulada por año-periodo de admisión. | 
| 8 | Admisión en programas de magister y doctorado. | Número de estudiantes: postulantes, aceptados y rechazados en programas de magíster y doctorado por año-periodo de admisión. |
| 8 | Admisión en programas de magister y doctorado. | Número de matriculados en programas de magíster y doctorado por año-periodo de admisión. |


{{< hint danger >}}
**DISCLAIMER**  
Para responder a los indicadores en **2**, **4**, **5**, **6** y **7** se debe usar la vista:
```
Sandobox-Datagov_Prod.ADMISION.TBL_VW_FT_ADMISION_PAS
```

Para responde a los indicadores en **1**, **3** y **8** se debe usar la vista:
```
Sandobox-Datagov_Prod.ADMISION.TBL_VW_FT_ADMISION_ESP_ORD_PAS
```
{{< /hint >}}

## **1. Admisión especial, según vía de Equidad**

### **Número de postulantes de carreras de pregrado, según vía de equidad, en un año-periodo de admisión.**

<!-- ```sql
SELECT ANO_ADMIS
    , COUNT(1)
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD 
WHERE COD_CASO_ADMIS IN (218, 259, 221, 237, 209, 226)
GROUP BY ANO_ADMIS
ORDER BY ANO_ADMIS
``` -->
```sql
SELECT 
      ANO_ADMIS
    , NOM_CASO_ADMIS
    , SEXO
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD_PAS
WHERE COD_CASO_ADMIS IN (218, 259, 221, 237, 209, 226)
GROUP BY 
      ANO_ADMIS
    , NOM_CASO_ADMIS
    , SEXO
ORDER BY ANO_ADMIS DESC
```


### **Número de postulantes seleccionados a carreras de pregrado según vía de equidad en un año-periodo de admisión**

<!-- ```sql
SELECT ANO_ADMIS
    , COUNT(1)
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD 
WHERE COD_CASO_ADMIS IN (218, 259, 221, 237, 209, 226)
    AND COD_ESTADO_POSTULACION IN (4, 6)
    OR COD_SIT_POSTULACION = 'S'
GROUP BY ANO_ADMIS
ORDER BY ANO_ADMIS
``` -->
```sql
SELECT 
      ANO_ADMIS
    , NOM_CASO_ADMIS
    , SEXO
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD_PAS
WHERE COD_CASO_ADMIS IN (218, 259, 221, 237, 209, 226)
    AND COD_EST_POSTULACION IN (4, 6)
    OR COD_SIT_POSTULACION = 'S'
GROUP BY 
      ANO_ADMIS
    , NOM_CASO_ADMIS
    , SEXO
ORDER BY ANO_ADMIS DESC
```

### **Número de estudiantes matriculados en carreras de pregrado según vía de equidad en un año-periodo de admisión**

<!-- ```sql
SELECT ANO_ADMIS
    , COUNT(1)
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD 
WHERE COD_CASO_ADMIS IN (218, 259, 221, 237, 209, 226)
    AND COD_ESTADO_POSTULACION = 7
    OR COD_SIT_POSTULACION = 'U'
GROUP BY ANO_ADMIS
ORDER BY ANO_ADMIS
``` -->

```sql
SELECT 
      ANO_ADMIS
    , NOM_CASO_ADMIS
    , COD_CASO_ADMIS
    , SEXO
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD_PAS
WHERE COD_CASO_ADMIS IN (218, 259, 221, 237, 209, 226)
    AND (COD_EST_POSTULACION = 7
         OR COD_SIT_POSTULACION = 'U')
GROUP BY
      ANO_ADMIS
    , NOM_CASO_ADMIS
    , COD_CASO_ADMIS
    , SEXO
ORDER BY ANO_ADMIS DESC
```

## **2. Estudiantes-postulantes pertenecientes a una etnia**

### **Número de estudiantes pertenecientes a una etnia, por año-periodo de admisión**

```sql
SELECT 
      ANO_ADMISION
    , ETNIA
    , SEXO
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_PAS
WHERE ETNIA IS NOT NULL
    AND COD_ETNIA <> 10
GROUP BY 
      ANO_ADMISION
    , ETNIA
    , SEXO
ORDER BY ANO_ADMISION DESC
```

## **3. Postulantes con puntaje nacional por año-periodo de admisión**

### **Número de estudiantes con puntaje nacional por año-periodo de admisión**

```sql
SELECT 
      ANO_ADMIS
    , SEXO
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD_PAS
WHERE CIE = 850
    OR HYC = 850
    OR LYC = 850
    OR MAT = 850
GROUP BY 
      ANO_ADMIS
    , SEXO
ORDER BY ANO_ADMIS DESC
```

## **4. Estudiantes de cursos superiores que postulan a la UC**

### **Número de estudiantes que no vienen directo de un establecimiento de educación secundaria (año de egreso)**

```sql
SELECT
      ANO_ADMISION
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_PAS
WHERE (ANO_ADMISION - ANO_EGRESO_COLEGIO) > 1
GROUP BY ANO_ADMISION
ORDER BY ANO_ADMISION DESC
```

## **5. Estudiantes-Postulantes según nacionalidad por año-periodo de admisión**

### **Número de estudiantres-postulantes según nacionalidad por año-periodo de admisión**

```sql
SELECT
      ANO_ADMISION
    , PAIS
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_PAS
GROUP BY 
      ANO_ADMISION
    , PAIS
ORDER BY ANO_ADMISION DESC
```

## **6. Estudiantes-Postulantes según IVE del establecimiento de origen, por año-periodo de admisión**

### **Número de estudiantes-postulantes según IVE del establecimiento de origen, por año-periodo de admisión**

```sql
SELECT
    ANO_ADMISION
  , IVE_TIER
  , IVE_TIER_DESC
  , SEXO 
  , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_PAS
WHERE IVE_COLEGIO IS NOT NULL
    AND IVE_TIER IS NOT NULL
GROUP BY 
    ANO_ADMISION
  , IVE_TIER
  , IVE_TIER_DESC
  , SEXO
ORDER BY ANO_ADMISION DESC
```


## **7. Estudiantes según carreras de preferencia postulada, por año-periodo de admisión**

### **Número de estudiantes seleccionados según carreras de preferencia postulada por año-periodo de admisión**

```sql
SELECT
     ANO_ADMISION
    , CARRERA
    , PREFERENCIA
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_PAS
WHERE (COD_SIT_POSTUL = 'S'
       OR COD_SIT_POSTUL_ANT = 'S'
       OR COD_SIT_POSTUL_INICIAL = 'S')
GROUP BY
      ANO_ADMISION
    , CARRERA
    , PREFERENCIA
ORDER BY 
        ANO_ADMISION DESC
      , PREFERENCIA ASC
```


### **Número de estudiantes matriculados según carreras de preferencia postulada por año-periodo de admisión**

```sql
SELECT
     ANO_ADMISION
    , CARRERA
    , PREFERENCIA
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_PAS
WHERE (COD_SIT_POSTUL = 'U'
       OR COD_SIT_POSTUL_ANT = 'U'
       OR COD_SIT_POSTUL_INICIAL = 'U')
GROUP BY
      ANO_ADMISION
    , CARRERA
    , PREFERENCIA
ORDER BY 
        ANO_ADMISION DESC
      , PREFERENCIA ASC
```


## **8. Admisión en programas de magister y doctorado**

### **Número de estudiantes: postulantes, aceptados y rechazados en programas de magíster y doctorado por año-periodo de admisión**

```sql
SELECT 
      ANO_ADMIS
    , SEXO
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD_PAS
WHERE COD_CASO_ADMIS IN (110, 150, 190, 111, 125, 112)
    AND COD_EST_POSTULACION <> 7
GROUP BY 
      ANO_ADMIS
    , SEXO
ORDER BY ANO_ADMIS DESC
```

### **Número de matriculados en programas de magíster y doctorado por año-periodo de admisión**

```sql
SELECT 
      ANO_ADMIS
    , SEXO
    , COUNT(1) AS NUM_POSTULANTES
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD_PAS
WHERE COD_CASO_ADMIS IN (110, 150, 190, 111, 125, 112)
    AND COD_EST_POSTULACION IN (6, 7, 8)
GROUP BY 
      ANO_ADMIS
    , SEXO
ORDER BY ANO_ADMIS DESC
```


<!-- {{< hint danger >}}
**DISCLAIMER**  
Para responder a los indicadores en **2**, **4**, **5**, **6** y **7** se generó la vista:
```
Sandobox-Datagov_Prod.ADMISION.TBL_VW_FT_ADMISION_PAS
```
{{< /hint >}} -->