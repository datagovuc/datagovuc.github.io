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



## **1. Admisión especial, según vía de Equidad**

### **Número de postulantes de carreras de pregrado, según vía de equidad, en un año-periodo de admisión.**

```sql
SELECT ANO_ADMIS
    , COUNT(1)
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD 
WHERE COD_CASO_ADMIS IN (218, 259, 221, 237, 209, 226)
GROUP BY ANO_ADMIS
ORDER BY ANO_ADMIS
```

### **Número de postulantes seleccionados a carreras de pregrado según vía de equidad en un año-periodo de admisión**

```sql
SELECT ANO_ADMIS
    , COUNT(1)
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD 
WHERE COD_CASO_ADMIS IN (218, 259, 221, 237, 209, 226)
    AND COD_ESTADO_POSTULACION IN (4, 6)
    OR COD_SIT_POSTULACION = 'S'
GROUP BY ANO_ADMIS
ORDER BY ANO_ADMIS
```

### **Número de estudiantes matriculados en carreras de pregrado según vía de equidad en un año-periodo de admisión**

```sql
SELECT ANO_ADMIS
    , COUNT(1)
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD 
WHERE COD_CASO_ADMIS IN (218, 259, 221, 237, 209, 226)
    AND COD_ESTADO_POSTULACION = 7
    OR COD_SIT_POSTULACION = 'U'
GROUP BY ANO_ADMIS
ORDER BY ANO_ADMIS
```

## **2. Estudiantes-postulantes pertenecientes a una etnia**

### **Número de estudiantes pertenecientes a una etnia, por año-periodo de admisión**

```sql
---- Se debe traer las tablas etnia y postul a sandbox
SELECT
    ANO_ADMIS
    , COUNT(1)
FROM ADMISION.POSTUL
WHERE COD_ETNIA <> 10
    AND COD_ETNIA IS NOT NULL
GROUP BY ANO_ADMIS
```

## **3. Postulantes con puntaje nacional por año-periodo de admisión**

### **Número de estudiantes con puntaje nacional por año-periodo de admisión**

```sql
SELECT ANO_ADMIS
    , COUNT(1)
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD
WHERE CIE = 850
    OR HYC = 850
    OR LYC = 850
    OR MAT = 850
GROUP BY ANO_ADMIS
ORDER BY ANO_ADMIS
```

## **4. Estudiantes de cursos superiores que postulan a la UC**

### **Número de estudiantes que no vienen directo de un establecimiento de educación secundaria (año de egreso)**

## **5. Estudiantes-Postulantes según nacionalidad por año-periodo de admisión**

### **Número de estudiantres-postulantes según nacionalidad por año-periodo de admisión**

```sql
-- Desagregado por país
SELECT COUNT(1) NUM_POSTUL
    , PA.NOM_PAIS
    , PE.ANO_ADMIS
FROM ADMISION.PERSONA_ADMISION AS PA 
INNER JOIN ADMISION.POSTULACION_EFECT AS PE 
    ON (PA.RUT = PE.RUT)
GROUP BY 
      PA.NOM_PAIS
    , PE.ANO_ADMIS
ORDER BY 
      PE.ANO_ADMIS

-- Desagregado por nacionalidad (chilena, extranjera)
SELECT COUNT(1) NUM_POSTUL
    , PA.NACIONALIDAD
    , PE.ANO_ADMIS
FROM ADMISION.PERSONA_ADMISION AS PA 
INNER JOIN ADMISION.POSTULACION_EFECT AS PE 
    ON (PA.RUT = PE.RUT)
GROUP BY 
      PA.NACIONALIDAD
    , PE.ANO_ADMIS
ORDER BY 
    PE.ANO_ADMIS
```


## **6. Estudiantes-Postulantes según IVE del establecimiento de origen, por año-periodo de admisión**

### **Número de estudiantes-postulantes según IVE del establecimiento de origen, por año-periodo de admisión**

```sql
-- Traer trabla INDICE_VULNER_ESCOLAR a la BD Sandbox_Prod

SELECT COUNT(1) AS NUM_POSTUL
    , ADM.COD_COLEGIO
    , ADM.ANO_COLEGIO
    , ADM.NOM_COLEGIO
FROM ADMISION.INDICE_VULNER_ESCOLAR AS IVE 
INNER JOIN ADMISION.COLEGIO_ADMISION AS ADM 
    ON IVE.ROL_BASE_DATO = ADM.ROL_BASE_DATO
GROUP BY ADM.COD_COLEGIO
    , ADM.NOM_COLEGIO
    , ADM.ANO_COLEGIO
ORDER BY 
    ADM.ANO_COLEGIO DESC
```


## **7. Estudiantes según carreras de preferencia postulada, por año-periodo de admisión**

### **Número de estudiantes seleccionados según carreras de preferencia postulada por año-periodo de admisión**

```sql
SELECT COUNT(1) NUM_POSTUL
    , HC.NOM_CARRERA
    , PE.ANO_ADMIS
FROM ADMISION.POSTULACION_EFECT AS PE
INNER JOIN ADMISION.HIST_CARRERA AS HC
    ON (PE.CLAVE_NACIONAL = HC.CLAVE_NACIONAL)
WHERE PE.PREF = 1
    AND PE.ANO_ADMIS > 2008
    AND (PE.COD_SIT_POSTULACION_INICIAL = 'S'
        OR PE.COD_SIT_POSTULACION_ANT = 'S'
        OR PE.COD_SIT_POSTULACION = 'S')
GROUP BY HC.NOM_CARRERA
    , PE.ANO_ADMIS
```


### **Número de estudiantes matriculados según carreras de preferencia postulada por año-periodo de admisión**

```sql
SELECT COUNT(1) NUM_POSTUL
    , HC.NOM_CARRERA
    , PE.ANO_ADMIS
FROM ADMISION.POSTULACION_EFECT AS PE
INNER JOIN ADMISION.HIST_CARRERA AS HC
    ON (PE.CLAVE_NACIONAL = HC.CLAVE_NACIONAL)
WHERE PE.PREF = 1
    AND PE.ANO_ADMIS > 2008
    AND (PE.COD_SIT_POSTULACION = 'U'
        OR PE.COD_SIT_POSTULACION_ANT ='U'
        OR PE.COD_SIT_POSTULACION_INICIAL ='U')
GROUP BY HC.NOM_CARRERA
    , PE.ANO_ADMIS
```


## **8. Admisión en programas de magister y doctorado**

Revisar si los COD_ESTADO_POSTULACION están correctos.

### **Número de estudiantes: postulantes, aceptados y rechazados en programas de magíster y doctorado por año-periodo de admisión**

```sql
SELECT COUNT(1)
    , ANO_ADMIS
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD
WHERE COD_CASO_ADMIS IN (110, 150, 190, 111, 125, 112)
    AND COD_ESTADO_POSTULACION <> 7
GROUP BY ANO_ADMIS
ORDER BY ANO_ADMIS
```

### **Número de matriculados en programas de magíster y doctorado por año-periodo de admisión**

```sql
SELECT COUNT(1)
    , ANO_ADMIS
FROM ADMISION.TBL_VW_FT_ADMISION_ESP_ORD
WHERE COD_CASO_ADMIS IN (110, 150, 190, 111, 125, 112)
    AND COD_ESTADO_POSTULACION IN (6, 7, 8)
GROUP BY ANO_ADMIS
ORDER BY ANO_ADMIS
```


{{< hint danger >}}
**DISCLAIMER**  
Para responder a los indicadores en **2**, **4**, **5**, **6** y **7** se generó la vista:
```
Sandobox-Datagov_Prod.ADMISION.TBL_VW_FT_ADMISION_PAS
```
{{< /hint >}}