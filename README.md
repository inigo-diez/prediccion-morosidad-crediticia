# Prediccion de Morosidad Crediticia

Este proyecto aborda un problema clasico del sector financiero: anticipar que clientes tienen mayor probabilidad de incurrir en mora antes de que esto ocurra. A traves de un analisis exploratorio riguroso y la aplicacion de distintos modelos de clasificacion, se busca construir una herramienta que permita a una entidad crediticia tomar decisiones mas informadas en la concesion y seguimiento de prestamos.

---

## Contexto y motivacion

La morosidad es uno de los principales riesgos a los que se enfrenta cualquier institucion financiera. Identificar de forma temprana a los clientes con mayor riesgo de impago permite actuar preventivamente, ya sea ajustando las condiciones del credito, reforzando el seguimiento o limitando la exposicion. El dataset utilizado proviene de una entidad financiera peruana y recoge informacion socioeconomica y de comportamiento crediticio de **8.399 clientes**.

---

## Variables del dataset

| Variable | Descripcion |
|----------|-------------|
| `mora` | Variable objetivo: 1 = moroso, 0 = cumplidor |
| `atraso` | Dias de atraso en pagos |
| `vivienda` | Tipo de vivienda del cliente |
| `edad` | Edad del cliente |
| `dias_lab` | Dias en activo laboral |
| `exp_sf` | Meses de experiencia en el sistema financiero |
| `ingreso` | Ingreso mensual declarado |
| `linea_sf` | Linea de credito disponible |
| `deuda_sf` | Deuda vigente en el sistema financiero |
| `score` | Puntuacion crediticia |
| `zona` | Region geografica del cliente |
| `clasif_sbs` | Clasificacion segun la Superintendencia de Banca y Seguros |
| `nivel_educ` | Nivel educativo alcanzado |

---

## Metodologia

### Analisis exploratorio
Se realizo una inspeccion inicial de los datos para comprender la distribucion de las variables, detectar valores ausentes y analizar el comportamiento de la variable objetivo. Se identificaron valores nulos en tres variables: `exp_sf` (1.830 registros), `linea_sf` (1.127) y `deuda_sf` (461), todos ellos imputados con la media de la columna correspondiente tras verificar que no existia un patron de ausencia sistematico.

### Tratamiento y limpieza de datos
- Eliminacion de outliers mediante el criterio del rango intercuartilico (IQR x 1.5)
- Descarte de la variable `nivel_ahorro` por presentar escasa variabilidad informativa
- Transformacion logaritmica y de raiz cuadrada sobre variables con distribucion muy sesgada

### Ingenieria de variables
Se construyeron nuevas variables a partir del conocimiento del dominio:
- **Endeudamiento**: ratio entre la deuda y el ingreso del cliente
- **Indicador de morosidad potencial**: boleano que indica si la deuda supera la linea de credito
- **Ratio deuda/linea**: medida de utilizacion del credito disponible
- **Agrupacion geografica**: las zonas con escasa representacion se agruparon en `High_Def` o `Low_Def` segun su tasa historica de morosidad

### Preprocesamiento y modelado
Las variables numericas se estandarizaron con `StandardScaler` y las categoricas se codificaron mediante `get_dummies`. El conjunto de datos se dividio en entrenamiento (70%) y prueba (30%). Se entrenaron y compararon tres modelos de clasificacion supervisada.

---

## Resultados

| Modelo | Accuracy | Precision | Recall | F1-Score |
|--------|----------|-----------|--------|----------|
| Regresion Logistica | 0.710 | 0.728 | 0.931 | 0.817 |
| Arbol de Decision | 0.742 | 0.787 | 0.861 | 0.823 |
| **Random Forest** | **0.851** | **0.841** | **0.969** | **0.900** |

El area bajo la curva ROC (AUC) se situa en **0.71**, lo que refleja una capacidad discriminativa moderada-buena del modelo logistico base.

---

## Conclusiones

El **Random Forest** se consolida como el modelo mas solido del estudio, alcanzando un F1-Score de **0.90** y capturando el **96.9%** de los clientes morosos reales. Esta capacidad de deteccion es especialmente valiosa en el contexto financiero, donde un falso negativo —no identificar a un moroso— tiene un coste significativamente mayor que una falsa alarma.

No obstante, el analisis pone de manifiesto un **desbalance de clases** relevante: aproximadamente el 70% de los registros corresponden a clientes morosos. Este sesgo puede inflar artificialmente el recall y merece consideracion al evaluar el rendimiento real del modelo en produccion.

El analisis del umbral de decision revela que ajustarlo por debajo de 0.5 permite aumentar la sensibilidad del modelo a costa de una mayor tasa de falsas alarmas, lo que ofrece un margen de ajuste segun el nivel de tolerancia al riesgo de la entidad.

En terminos generales, este trabajo demuestra que es posible construir un modelo predictivo de morosidad con una precision comercialmente util a partir de variables relativamente accesibles, sin necesidad de informacion altamente sensible o de dificil obtencion.

