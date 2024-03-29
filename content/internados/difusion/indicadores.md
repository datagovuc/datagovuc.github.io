---
weight: 1
title: "Indicadores"
---

# Sesión de Convergencia: Indicadores Difusión

A continuación un análisis con los cruces para responder a los indicadores para Difusión.

| ID | Estado | Indicador | Definición |
| :--: | :--: | :-- | :-- |
| 1 | :white_check_mark: | Participación en actividades del programa de difusión. | Número de potenciales postulantes según grupo-dependencia del establecimiento de educación secundaria que participan en al menos una actividad del programa de Difusión. |
| 1 | :white_check_mark: | Participación en actividades del programa de difusión. | Número de potenciales postulantes alcanzados por cada actividad del programa de Disfusión. |
| 2 | :white_check_mark: | Potenciales postulantes interesados en la UC que participan en actividades del programa de Difusión. | Número de potenciales postulantes interesados en la UC que participan en  actividades del Programa de Difusión. |
| 3 | :white_check_mark: | Colegios que participan en el programa de difusión. | Número de colegios que participan en el programa de Difusión. |
| 4 | :white_check_mark: | Potenciales postulantes según carrera de preferencia declarada. | Número de potenciales postulantes según carrera de preferencia declarada. |
| 5 | :white_check_mark:  | Estudiantes embajadores UC y embajadores por cada carrera. | Número de estudiantes embajadores UC. |
| 6 | :white_check_mark: | Actividades del programa de Difusión. | Número de actividades del Programa de Difusión por tipo de organizador. |
| 7 | :white_check_mark: | Potenciales postulantes alcanzados según curso. | Número de potenciales postulantes que son alcanzados, según curso. |
| 8 | :white_check_mark: | Orientadores y directores de los establecimientos Educacionales, que pertenecen a la Red de Apoyo de la Comunidad Educativa. | Número de orientadores y directores de los establecimientos educacionales, que pertenecen a la Red de Apoyo de la Comunidad Educativa. |
| 9 | | Potenciales postulantes en programa de Preparación y Exploración Vocacional. | Número de potenciales postulantes en programa de Preparación y Exploración Vocacional. |
<!-- | 5 | | Estudiantes embajadores UC y embajadores por cada carrera. | Número de estudiantes embajadores embajadores por cada carrera. | -->

{{< hint danger >}}
**DISCLAIMER**  
Para responder a los indicadores se usa la vista:
```
Sandobox-Datagov_Prod.BDGI.DIFUSION
```
{{< /hint >}}


## **1. Participación en actividades del programa de difusión**

### **Número de potenciales postulantes según grupo-dependencia del establecimiento de educación secundaria que participan en al menos una actividad del programa de Difusión**

```sql
SELECT
    grupo_dependencia
  , COUNT(rut) AS num_potenciales_postul
FROM BDGI.DIFUSION
WHERE grupo_dependencia IS NOT NULL
GROUP BY grupo_dependencia
```

### **Número de potenciales postulantes alcanzados por cada actividad del programa de Disfusión**

```sql
select
	organizador_del_evento,
	nombre_evento,
	count(*) as numero_potenciales_postulantes
from BDGI.DIFUSION 
where organizador_del_evento = 'Difusión UC'
group by organizador_del_evento, nombre_evento
```

## **2. Potenciales postulantes interesados en la UC que participan en actividades del programa de Difusión**

### **Número de potenciales postulantes interesados en la UC que participan en actividades del Programa de Difusión**


```sql
SELECT 
  COUNT(rut) AS num_potenciales_postul
FROM BDGI.DIFUSION
WHERE organizador_del_evento = 'Difusión UC'
```

## **3. Colegios que participan en el programa de difusión**

### **Número de colegios que participan en el programa de Difusión**

```sql
-- La lógica que nos dio el equipo de SalesForce está incluida dentro de la vista
SELECT
  COUNT(DISTINCT id_colegio) AS num_colegios
FROM BDGI.DIFUSION
-- WHERE organizador_del_evento = 'Difusión UC'
```

## **4. Potenciales postulantes según carrera de preferencia declarada**

### **Número de potenciales postulantes según carrera de preferencia declarada**

```sql
SELECT
    carrera_de_interes
  , COUNT(DISTINCT rut) num_potenciales_postul
FROM BDGI.DIFUSION
GROUP BY carrera_de_interes
ORDER BY num_potenciales_postul DESC
```

## **5. Estudiantes embajadores UC y embajadores por cada carrera**

```sql
select
	carrera,
	count(*)
from BDGI.EMBAJADORES_DIFUSION
group by carrera
```

### **Número de estudiantes embajadores UC**


## **6. Actividades del programa de Difusión**

```sql
-- Para ver el número de eventos/actividades por organizador
select 
	distinct organizador_del_evento,
	count(organizador_del_evento) over (partition by organizador_del_evento) as numero_actividades
from BDGI.DIFUSION d 
where organizador_del_evento is not null
group by organizador_del_evento, nombre_evento
```

```sql
-- Para ver el nombre de los eventos/actividades por organizador
select
	organizador_del_evento,
	nombre_evento
from BDGI.DIFUSION d 
where organizador_del_evento is not null
group by organizador_del_evento, nombre_evento
```

### **Número de actividades del Programa de Difusión por tipo de organizador**

## **7. Potenciales postulantes alcanzados según curso**

### **Número de potenciales postulantes que son alcanzados, según curso**

```sql
SELECT 
    curso 
  , COUNT(DISTINCT rut) num_potenciales_postul
FROM BDGI.DIFUSION
WHERE curso IN ('7°', '8°', 'I°', 'II°', 'III°', 'IV°')
GROUP BY curso
```

## **8. Orientadores y directores de los establecimientos Educacionales, que pertenecen a la Red de Apoyo de la Comunidad Educativa**

### **Número de orientadores y directores de los establecimientos educacionales, que pertenecen a la Red de Apoyo de la Comunidad Educativa**

```sql
SELECT 
  COUNT(DISTINCT rut_rol) num_directores_y_orientadores
FROM BDGI.DIFUSION
WHERE nombre_cargo IN ('Director', 'Orientador')
```

## **9. Potenciales postulantes en programa de Preparación y Exploración Vocacional**

No hay claridad sobre cómo obtener este indicador.

### **Número de potenciales postulantes en programa de Preparación y Exploración Vocacional**