---
title: "Docencia y formación de personas"
---

# **Tablas y cruces propuestos**

## **Cruce académicos y cursos dictados**

```sql
WITH CURSODICTADO AS (
    SELECT
        HPHC.COD_PERS           AS COD_PERS
        , HPHC.RUT              AS RUT
        , ACA.NOMBRE_COMPLETO   AS NOMBRE_COMPLETO
        , HC.COD_HIST_CURSO     AS COD_HIST_CURSO
        , HC.ANO_CURSO          AS ANO_CURSO
        , HC.SECCION            AS SECCION_CURSO
        , HC.SIGLA              AS SIGLA_CURSO
        , CU.NOM_CURSO          AS NOMBRE_CURSO
    FROM UC_BANNER.R_HIST_PERS_HIST_CURSO AS HPHC
    JOIN UC_BANNER.HIST_CURSO             AS HC 
        ON HPHC.COD_HIST_CURSO = HC.COD_HIST_CURSO
    JOIN UC_BANNER.CURSO                  AS CU
        ON HC.SIGLA = CU.SIGLA 
    JOIN web.Academicos_sipa              AS ACA
        ON HPHC.RUT = ACA.RUT
    GROUP BY
        HPHC.COD_PERS
        , HPHC.RUT
        , ACA.NOMBRE_COMPLETO
        , HC.COD_HIST_CURSO
        , HC.ANO_CURSO
        , HC.SECCION
        , HC.SIGLA
        , CU.NOM_CURSO
)
SELECT COUNT(DISTINCT COD_PERS) FROM CURSODICTADO        -- este cruce entrega 2993 personas distintas

SELECT COUNT(DISTINCT COD_PERS) FROM web.Academicos_sipa -- esta vista indica que hay 3575 académicos
```

Perfil de los datos generados:

+ Años de los cursos desde 1991 - 2019
+ 2993 académicos distintos
+ 5048 cursos distintos
+ Desde el 2016 en adelante, más de 3000 cursos por año


## **Cruce académicos y actividad académica**

```sql
WITH ACTIVACAD AS (
    SELECT 
        HPHC.COD_PERS                   AS COD_PERS
        , HPHC.RUT                      AS RUT
        , WAS.NOMBRE_COMPLETO           AS NOMBRE_COMPLETO
        , HA.NOM_ACTIV_ACADEMICO        AS NOMBRE_ACTIVIDAD_ACADEMICA
        , HA.COD_TIPO_ACTIV_ACADEMICO   AS COD_TIPO_ACTIV_ACADEMICA
        , HATA.NOM_TIPO_ACTIV_ACADEMICO AS NOMBRE_TIPO_ACTIV_ACADEMICA
        -- , HATA.INTERNO_UC
        , HATA.VIG_TIPO_ACTIV_ACADEMICO AS ACTIV_ACADEMICA_VIGENTE
        , CAST(HA.FECHA_INICIO AS DATE) AS FECHA_INICIO_ACTIV
        , CAST(HA.FECHA_FIN AS DATE)    AS FECHA_FIN_ACTIV
        , HA.DESCRIP_ABREV_ACTIV        AS DESCRIP_ABREV_ACTIV
    FROM HISTACADEMICO.ACTIV_ACADEMICO      AS HA
    JOIN UC_BANNER.R_HIST_PERS_HIST_CURSO   AS HPHC
        ON HA.COD_PERS = HPHC.COD_PERS
    JOIN web.Academicos_sipa                AS WAS
        ON HPHC.RUT = HAU.RUT
    JOIN HISTACADEMICO.TIPO_ACTIV_ACADEMICO AS HATA
        ON HA.COD_TIPO_ACTIV_ACADEMICO = HATA.COD_TIPO_ACTIV_ACADEMICO
    GROUP BY HPHC.COD_PERS
        , HPHC.RUT
        , WAS.NOMBRE_COMPLETO
        , HA.NOM_ACTIV_ACADEMICO
        , HA.COD_TIPO_ACTIV_ACADEMICO
        , HATA.NOM_TIPO_ACTIV_ACADEMICO
        -- , HATA.INTERNO_UC
        , HATA.VIG_TIPO_ACTIV_ACADEMICO
        , CAST(HA.FECHA_INICIO AS DATE)
        , CAST(HA.FECHA_FIN AS DATE)
        , HA.DESCRIP_ABREV_ACTIV
)
SELECT * FROM ACTIVACAD --1061
```

Perfil de los datos generados:
+ 1061 académicos distintos