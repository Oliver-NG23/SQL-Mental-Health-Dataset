# 🧠 Mental Health in the Tech Industry – SQL Analysis

Este proyecto explora el dataset **“Mental Health in the Tech Industry”**, el cual reúne información sobre la salud mental de personas que trabajan en el sector tecnológico.  
El objetivo principal es **comprender los factores que influyen en la salud mental dentro de la industria tecnológica** mediante consultas SQL.

---

## 📊 Descripción del Dataset

El dataset proviene de [Kaggle](https://www.kaggle.com/datasets/anth7310/mental-health-in-the-tech-industry) y contiene respuestas anónimas de profesionales del sector tecnológico sobre distintos aspectos de su salud mental y su entorno laboral.

---

## 🎯 Objetivos del Proyecto

1. **Explorar y comprender la estructura del dataset** para identificar sus variables clave y distribución de los datos.  

2. **Analizar los factores relacionados con la salud mental en el entorno laboral tecnológico** percibido por los empleados.  

3. **Examinar patrones demográficos y geográficos** (como edad, género y país) para detectar diferencias en la percepción, el tratamiento y la conciencia sobre la salud mental.  

4. **Generar insights accionables** para encontrar posibles áreas de mejora en la cultura laboral del sector tecnológico.  

---

## ❓ Preguntas de Análisis SQL

1. ¿Cúantos participantes aplicaron la encuesta durante los años?
```sql
SELECT s.SurveyID, s.Description, COUNT(a.AnswerText) AS TotalAnswers
FROM Answer AS a
JOIN Survey AS s 
ON a.SurveyID = s.SurveyID
GROUP BY s.SurveyID, s.Description
ORDER BY s.SurveyID;
```
**Objetivo:** Saber cuantas personas participaron en la aplicacion de la encuesta a lo largo de los años

2. ¿De que paises provienen los encuestados?
```sql
SELECT 
	CASE WHEN answertext IN ('United States of America','United States') THEN 'United States'
    ELSE answertext END AS pais, COUNT(*) AS cuenta
FROM Answer
WHERE questionid = 3 AND answertext NOT IN( '-1', 'N/A','') AND answertext IS NOT NULL
GROUP BY pais
ORDER BY cuenta DESC;
```
3. ¿Qué porcentaje de encuestados ha recibido tratamiento para su salud mental? 
```sql
SELECT CASE WHEN a.AnswerText = 1 THEN 'YES' ELSE 'NO' END AS Respuesta,
	COUNT(a.AnswerText) AS Total_Respuesta,
    ROUND(COUNT(*) * 100.0 / SUM (COUNT(*)) OVER (),4) AS porcentaje
FROM Answer AS a
JOIN Question AS q
ON a.QuestionID = q.QuestionID
WHERE q.QuestionText LIKE '%Have you ever sought treatment%'
group by a.AnswerText;
```
**Objetivo:** Conocer antecedentes de los empleados y su distribucion

4. ¿Cuáles son los 5 países con mayor porcentaje de encuestados que reportan haber recibido tratamiento?
```sql
SELECT  
	CASE 
        WHEN a.AnswerText IN ('United States of America', 'United States') THEN 'United States'
        ELSE a.AnswerText END AS Pais,
    COUNT(DISTINCT a.UserID) AS Total_Usuarios,
    ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 4) AS Porcentaje
FROM Answer AS a
JOIN Question AS q
    ON a.QuestionID = q.QuestionID
JOIN Survey AS s
    ON a.SurveyID = s.SurveyID
WHERE q.QuestionText LIKE '%country%' AND answertext NOT IN( '-1', 'N/A','') 
  AND answertext IS NOT NULL
  AND a.UserID IN(
        SELECT subA.UserID
        FROM Answer AS subA
        JOIN Question AS subQ
            ON subA.QuestionID = subQ.QuestionID
        WHERE subQ.QuestionText LIKE '%Have you ever sought treatment%'
          AND subA.AnswerText = '1')
GROUP BY Pais
ORDER BY Porcentaje DESC, s.SurveyID DESC
LIMIT 5;
```
**Objetivo:** Conocer si es un factor el pais de procedencia recibir tratamiento para la salud mental

5. ¿Existe relación entre el tamaño de la empresa y la probabilidad de haber recibido tratamiento?  
```sql
SELECT 
    CASE WHEN a1.AnswerText IN ('1-5','6-25') THEN 'Small'
    WHEN a1.AnswerText IN('26-100','100-500') THEN 'Medium'
    WHEN a1.AnswerText IN('500-1000','More than 1000') THEN 'Big' END AS Tamaño_de_Compañia,
    SUM(CASE WHEN a2.AnswerText = 1 THEN 1 ELSE 0 END) as Respuesta_Yes,
    SUM(CASE WHEN a2.AnswerText = 0 THEN 1 ELSE 0 END) as Respuesta_No,
    SUM(CASE WHEN a2.AnswerText IN (1,0) THEN 1 ELSE 0 END) AS TotalPeople,
    ROUND(SUM(CASE WHEN a2.AnswerText = 1 THEN 1 ELSE 0 END) * 100.0 / 
          SUM(CASE WHEN a2.AnswerText IN (1,0) THEN 1 ELSE 0 END),2) AS Porcentaje_Tratamiento
FROM Answer AS a1
JOIN Question AS q1
	ON a1.QuestionID = q1.QuestionID
JOIN Answer as a2
	ON a1.UserID = a2.UserID AND a1.SurveyID = a2.SurveyID
JOIN Question q2
	ON a2.QuestionID = q2.QuestionID
WHERE q1.QuestionText LIKE '%How many employees%' 
  AND q2.QuestionText LIKE '%Have you ever sought treatment%' 
  AND a1.AnswerText NOT IN ('-1', 'N/A', '') AND a1.AnswerText IS NOT NULL
GROUP BY Tamaño_de_Compañia
ORDER BY Porcentaje_Tratamiento DESC;
```
**Objetivo:** Conocer si hay relacion entre el tamaño de empresa y la probabilidad de recibir tratamiento

6. ¿Cuál es la realción entre el género y la probabilidad de haber recibido tratamiento?
```sql
SELECT 
    CASE WHEN a1.AnswerText IN ('Masculine','Male','male','MALE','masculino') THEN 'Male'
    WHEN a1.AnswerText IN ('Female','female','FEMALE') THEN 'Female' ELSE 'Other' END AS Genero,
    COUNT(DISTINCT a1.UserID) AS Total_Usuarios,
    ROUND(COUNT(DISTINCT a1.UserID)*100.0 / SUM(COUNT(DISTINCT a1.UserID)) OVER (),2) as Porcentaje
FROM Answer AS a1
JOIN Question AS q1 
	ON a1.QuestionID = q1.QuestionID
WHERE q1.QuestionText LIKE '%What is your gender%' AND a1.AnswerText NOT IN ('-1','43','N/A','')
	AND a1.UserID IN (
      SELECT subA.UserID
      FROM Answer AS subA
      JOIN Question as subQ
      	ON subA.QuestionID = subQ.questionid
      WHERE subQ.questiontext LIKE '%Have you ever sought treatment%'
      	AND subA.AnswerText = '1')
GROUP BY Genero
ORDER BY Total_Usuarios DESC;
```
**Objetivo:** Conocer si existen diferencias de genero en la busqueda de tratamiento

### Nivel 3 – Relaciones e indicadores más complejos 
8. ¿Cómo varía la edad promedio entre quienes han recibido tratamiento y quienes no?  

### Nivel 4 – Subconsultas y funciones avanzadas
10. Para cada empresa, ¿qué porcentaje de empleados se siente cómodo hablando de salud mental con su supervisor?  
11. ¿Cómo cambia el nivel de estrés promedio según los años trabajados en la empresa (agrupando por rangos)?  
12. Calcula el percentil de salario por país y analiza si los trabajadores con salarios más altos (percentil 90 o superior) tienden a recibir más o menos tratamiento que el resto.

---
