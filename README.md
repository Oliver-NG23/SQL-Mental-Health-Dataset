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

### Nivel 1 – Exploración básica
1. ¿Cuántas personas se encuestarón?
```
-- Crear una tabla de empleados
CREATE TABLE empleados (
    id INT PRIMARY KEY,
    nombre VARCHAR(100),
    puesto VARCHAR(50),
    salario DECIMAL(10, 2)
);

-- Insertar un registro de ejemplo
INSERT INTO empleados (id, nombre, puesto, salario)
VALUES (1, 'Ana López', 'Desarrolladora', 3500.00);

-- Consultar empleados con salario mayor a 3000
SELECT * FROM empleados WHERE salario > 3000;
```

1. ¿Cuántas columnas hay y cuáles son sus nombres?  
2. ¿Cuál es la distribución de los encuestados por país o género?

### Nivel 2 – Filtrado y agrupaciones simples
4. ¿Qué porcentaje de encuestados ha recibido tratamiento para su salud mental?  
5. ¿Cuál es el salario promedio por género o por país?  
6. ¿Cuántos encuestados indicaron que su empresa tiene una política de salud mental, y cuántos no?

### Nivel 3 – Relaciones e indicadores más complejos
7. ¿Existe relación entre el tamaño de la empresa y la probabilidad de haber recibido tratamiento?  
8. ¿Cómo varía la edad promedio entre quienes han recibido tratamiento y quienes no?  
9. ¿Cuáles son los 5 países con mayor porcentaje de encuestados que reportan haber recibido tratamiento?

### Nivel 4 – Subconsultas y funciones avanzadas
10. Para cada empresa, ¿qué porcentaje de empleados se siente cómodo hablando de salud mental con su supervisor?  
11. ¿Cómo cambia el nivel de estrés promedio según los años trabajados en la empresa (agrupando por rangos)?  
12. Calcula el percentil de salario por país y analiza si los trabajadores con salarios más altos (percentil 90 o superior) tienden a recibir más o menos tratamiento que el resto.

---
