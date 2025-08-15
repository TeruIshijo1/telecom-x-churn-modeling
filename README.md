## Telecom X – Churn Modeling (Parte 2)

Este repositorio contiene el desarrollo de la segunda parte del reto de **Data Science LATAM** para la empresa **Telecom X**.  En esta fase se construyen modelos predictivos capaces de anticipar qué clientes tienen mayor probabilidad de cancelar sus servicios (churn) a partir de variables demográficas, de servicios contratados y de facturación.

### Propósito del proyecto

El objetivo principal es crear un pipeline completo de preparación de datos y modelado que permita predecir la cancelación de clientes.  A partir de un conjunto de datos tratado (obtenido en la primera parte del reto), se aplican técnicas de codificación, normalización y selección de variables para entrenar y evaluar modelos de clasificación.  Los resultados proporcionan insights accionables sobre los factores que más influyen en el churn y sirven como base para estrategias de retención.

### Estructura del repositorio

```
├── data/
│  └── TelecomX_Data_Treated.csv   # datos limpios y codificados (sin customerID)
├── notebooks/
│  └── TelecomX_Churn_Modeling.ipynb  # cuaderno con todo el pipeline de modelado
├── images/
│  ├── churn_distribution.png
│  ├── contract_churn.png
│  ├── senior_churn.png
│  └── ...otros gráficos opcionales
└── README.md  # este archivo
```

* **data/**: contiene el archivo CSV con los datos tratados.  Se eliminó el identificador `customerID`, se transformaron todas las variables categóricas mediante *one‑hot encoding* y se convirtieron los campos numéricos (`tenure`, `MonthlyCharges`, `TotalCharges`) a tipo numérico.  El target `Churn` está codificado como `0 = No` y `1 = Yes`.
* **notebooks/**: almacena el cuaderno `TelecomX_Churn_Modeling.ipynb`, que implementa el pipeline de preprocesamiento, modelado y evaluación.
* **images/**: incluye algunos ejemplos de visualizaciones exploratorias (EDA) generadas en la primera parte del reto, como la distribución de churn y su relación con el tipo de contrato o la edad.

### Proceso de preparación de los datos

1. **Importación de datos**: se cargó el archivo JSON original desde GitHub y se aplanaron las estructuras anidadas (`customer`, `phone`, `internet`, `account`).  Después se guardó el resultado tratado en `data/TelecomX_Data_Treated.csv`.
2. **Selección de variables**: se eliminaron columnas irrelevantes para la predicción (por ejemplo, `customerID`).  El dataset final cuenta con 45 variables predictoras (mezcla de numéricas y dummies) y la variable objetivo `Churn`.
3. **Tipificación**: las variables numéricas (`tenure`, `MonthlyCharges`, `TotalCharges`) se convirtieron a tipo numérico y las variables categóricas se codificaron con **one‑hot encoding** usando `pandas.get_dummies`.
4. **Normalización**: algunos modelos, como la regresión logística o KNN, requieren que las variables estén en la misma escala.  Por ello se utilizó `StandardScaler` para normalizar las features al entrenar la regresión logística.  Los modelos basados en árboles (Decision Tree, Random Forest) no necesitan este paso.
5. **División de datos**: se separó el conjunto en 70 % para entrenamiento y 30 % para prueba utilizando `train_test_split` con la opción `stratify=y` para preservar la proporción de clases.
6. **Modelado**: se entrenaron al menos dos modelos de clasificación:
   - **Regresión Logística** – requiere normalización porque optimiza coeficientes basados en distancias.  Permite interpretar la influencia de cada variable a través de los coeficientes.
   - **Random Forest** – un ensamble de árboles de decisión que maneja bien relaciones no lineales y no requiere escalado.  Proporciona importancias de variables basadas en la reducción de impureza.
   Otros modelos como SVM o KNN pueden añadirse fácilmente siguiendo la misma lógica de preprocesamiento.
7. **Evaluación**: se utilizaron métricas de clasificación (exactitud, precisión, recall, F1‑score) y matrices de confusión para comparar los modelos.  También se generaron gráficos de importancia de variables y una matriz de correlación para explorar las relaciones entre las variables y el churn.

### Decisiones de modelización

* **Manejo de valores faltantes**: se eliminaron las filas con valores `NaN` para simplificar el análisis.  Si el desbalance entre clases se acentúa por esta eliminación, se puede recurrir a técnicas de oversampling/undersampling (como **SMOTE**) en futuros experimentos.
* **Desbalance de clases**: aproximadamente el 26 % de los clientes se considera churn.  Se mantuvo la proporción original en la división de entrenamiento/prueba, pero se recomienda probar técnicas de balanceo para mejorar el recall en la clase minoritaria.
* **Elección de modelos**: se eligieron modelos complementarios para comparar un algoritmo lineal (Regresión Logística) con un algoritmo de árboles (Random Forest).  La regresión logística ofrece interpretabilidad y se beneficia de la normalización, mientras que Random Forest captura relaciones no lineales y maneja variables de diferentes escalas.

### Ejemplos de visualizaciones e insights

* **Distribución de churn**: se observa que ~74 % de los clientes permanecen y ~26 % se dan de baja, lo que indica un leve desbalance de clases.
* **Churn por tipo de contrato**: los usuarios con contratos mensuales presentan una tasa de cancelación mucho mayor que aquellos con contratos anuales o de dos años.
* **Churn por antigüedad**: mediante un boxplot de `tenure` se evidencia que los clientes que cancelan tienen una antigüedad media significativamente menor que los que permanecen.
* **TotalCharges vs Churn**: clientes que han gastado menos en total tienden a cancelar con mayor frecuencia, lo que sugiere que la antigüedad y el gasto acumulado son indicadores de fidelidad.

### Instrucciones para ejecutar el cuaderno

1. **Clonar o descargar** este repositorio en tu entorno local:

   ```bash
   git clone https://github.com/tu_usuario/tu_repositorio.git
   cd tu_repositorio
   ```

2. Instalar las dependencias necesarias (se recomienda un entorno virtual):

   ```bash
   pip install pandas numpy matplotlib seaborn scikit-learn
   ```

3. Abrir el cuaderno `TelecomX_Churn_Modeling.ipynb` con Jupyter Notebook o Google Colab.  Si utilizas Colab, sube el archivo `TelecomX_Data_Treated.csv` a la ruta `/content` para que el cuaderno pueda cargarlo correctamente.

4. Ejecutar las celdas en orden.  El cuaderno cargará el dataset tratado, realizará la separación de datos, ajustará los modelos y mostrará las métricas y gráficos generados.

### Conclusiones y próximos pasos

El modelo de **Regresión Logística** obtuvo un mejor equilibrio entre precisión y recall que el **Random Forest**, con una exactitud alrededor de 0,80.  Las variables más influyentes fueron la antigüedad (`tenure`), el gasto total (`TotalCharges`), la tarifa mensual (`MonthlyCharges`), el método de pago electrónico y la contratación de internet por fibra óptica.  

Clientes con mayor antigüedad y contratos de uno o dos años presentan menor probabilidad de cancelar, mientras que cuotas mensuales altas y el método de pago *Electronic check* incrementan el riesgo de churn.  Como trabajo futuro se recomienda probar técnicas de balanceo de clases, ajustar hiperparámetros de los modelos y explorar algoritmos adicionales como XGBoost o SVM.
