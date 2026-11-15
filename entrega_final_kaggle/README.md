# Entrega final Kaggle · Labo 1 Virtual 2026 · Analista Jr

**Alumno:** Ignacio Carraud  
**Competencia:** Labo 1 Virtual 2026 – Analista Jr  
**Submit final elegido:** `KA9906_1800.csv`  
**Experimento final:** `WF9906`  
**Public Score:** `91.048`

---

## Resumen

La entrega final corresponde al experimento `WF9906`.

El modelo utiliza LightGBM y un ensamble de **10 modelos**, entrenados con distintas semillas generadas a partir de la semilla primigenia `100183`. Las probabilidades de los 10 modelos se promedian para obtener la predicción final.

La configuración de `WF9906` surge de combinar distintas mejoras evaluadas previamente sobre el workflow de Analista Jr.

El submit seleccionado utiliza un corte de **1.800 clientes**, que obtuvo un Public Score de **91.048** en Kaggle.

---

## Configuración del modelo final

### Data Drift

Se utiliza:

`rank_cero_fijo`

Este tratamiento transforma las variables monetarias utilizando su posición relativa dentro de cada mes, manteniendo el valor cero como cero.

### Feature Engineering histórico

Para las variables lagueables se generan:

- `lag1`
- `lag2`
- `delta1`
- `delta2`

Además, se aplica un tratamiento específico para el período de pandemia.

Se consideran como meses de pandemia:

`202003` a `202010`

Los lags y deltas que apuntan a alguno de esos meses son anulados para evitar incorporar esa información histórica.

---

## Training Strategy

La validación utilizada es:

`202107`

El entrenamiento para la optimización de hiperparámetros utiliza información hasta:

`202105`

Los meses comprendidos entre `202003` y `202010` se excluyen del conjunto de entrenamiento.

Durante la búsqueda de hiperparámetros se utiliza:

`training_pct = 0.25`

Esto implica undersampling de la clase `CONTINUA` durante la optimización, manteniendo todos los registros de `BAJA+1` y `BAJA+2`.

El entrenamiento final se realiza sin undersampling y utiliza los meses disponibles hasta `202107`, excluyendo nuevamente el período de pandemia definido anteriormente.

La predicción final se realiza sobre:

`202109`

---

## Optimización de hiperparámetros

Se utiliza **Optimización Bayesiana** con `mlrMBO`.

Cantidad de iteraciones:

`300`

La función objetivo maximiza directamente la **ganancia obtenida en el conjunto de validación**.

Los cinco hiperparámetros optimizados son:

- `num_iterations`
- `num_leaves`
- `learning_rate`
- `feature_fraction`
- `min_sum_hessian_in_leaf`

Los mejores hiperparámetros obtenidos en `WF9906` fueron:

| Hiperparámetro | Valor |
| --- | --- |
| `num_iterations` | 178 |
| `num_leaves` | 1678 |
| `learning_rate` | 0.012821 |
| `feature_fraction` | 0.2968814 |
| `min_sum_hessian_in_leaf` | 1.0722965 |

La máxima ganancia registrada por la Optimización Bayesiana en validación fue:

`106.650.000`

---

## Modelo final

El algoritmo utilizado es LightGBM con:

`boosting = gbdt`

Se generan **10 modelos finales** con distintas semillas.

Las semillas utilizadas fueron:

`602621, 398969, 446197, 533593, 975691, 790649, 465743, 473741, 598487, 216421`

Cada modelo se entrena sobre el conjunto completo de entrenamiento final.

Para cada cliente de `202109`, se calcula la probabilidad predicha por cada uno de los 10 modelos y luego se obtiene el promedio simple de esas probabilidades.

---

## Elección del submit

A partir de las probabilidades promedio del ensamble, los clientes se ordenan de mayor a menor probabilidad.

Se evaluaron los siguientes cortes:

`1800, 1900, 2000, 2100, 2200, 2300 y 2400`

El mejor resultado obtenido en el Public Leaderboard fue:

| Experimento | Envíos | Public Score |
| --- | --- | --- |
| `WF9906` | **1800** | **91.048** |

Por este motivo se seleccionó:

`KA9906_1800.csv`

como submit final.

---

## Archivos

- `WF9906_final_junior.ipynb`: notebook completo utilizado para generar el modelo final.
- `exp_WF9906_PARAM.yml`: parámetros efectivamente utilizados y grabados por el workflow.
- `KA9906_1800.csv`: archivo seleccionado como submit final de Kaggle.
- `README.md`: documentación de la entrega.

---

## Dataset

El workflow utiliza:

`analistajr_competencia_2026.csv.gz`

El dataset no se incluye en esta carpeta.

---

## Reproducción

Para reproducir la corrida:

1. Utilizar el entorno de Analista Jr provisto por la cátedra.
2. Ubicar `analistajr_competencia_2026.csv.gz` en la carpeta de datasets esperada por el notebook.
3. Ejecutar completamente `WF9906_final_junior.ipynb` utilizando kernel R.
4. El workflow genera la carpeta correspondiente a `WF9906`.
5. Dentro de la carpeta `kaggle` se generan los archivos para los distintos cortes.
6. El archivo utilizado como entrega final es `KA9906_1800.csv`.
