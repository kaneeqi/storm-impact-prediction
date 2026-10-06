# Matriz de decisión de herramientas

Este documento justifica las herramientas con las que desarrollamos el proyecto. Primero comparamos la plataforma principal de minería de datos con una matriz ponderada. Después registramos decisiones más acotadas (entorno de ejecución, bibliotecas y versionado) y las que quedan pendientes para el TF1.

Las notas son un juicio del equipo. Se basan en lo que exige el proyecto y en la documentación oficial de cada herramienta (ver [Fuentes](#fuentes)). Las revisaremos al iniciar el TF1.

## 1. Qué necesita el proyecto

Los criterios salen de lo que el TP1 ya exige, no de preferencias generales:

| # | Necesidad | Dónde aparece en `TP1.ipynb` |
|---|---|---|
| N1 | Construir el target a partir de texto: los daños vienen con sufijo (K, M o B) y la columna de magnitud mezcla viento (nudos) y granizo (pulgadas). | Secciones 3.0 y 4 |
| N2 | Separar por episodio: hay 9,846 `EPISODE_ID` y 484 de los 1,014 positivos comparten episodio con otro positivo. Un split por fila daría métricas optimistas. | Sección 5 |
| N3 | Ajustar el preprocesamiento solo con train, también dentro de cada fold de la validación cruzada del TF1. | Secciones 6 y 10.3 |
| N4 | Evaluar con métricas para clases desbalanceadas (1.40 % de positivos): PR-AUC, curva precision-recall y elección del umbral. | Secciones 9 y 10.3 |
| N5 | Poder reejecutar el flujo de principio a fin y revisar los cambios en GitHub, que es la fuente oficial. | README |
| N6 | Que todos los integrantes puedan abrir y ejecutar el trabajo, en Colab o en local. | README |

**Filtros previos.** Antes de puntuar, descartamos las herramientas que no cumplen lo mínimo: no tener costo de licencia para uso académico, manejar 72,360 × 51 en la memoria de una laptop o de Colab (12.6 MB comprimido) y permitir entrenar y evaluar modelos de clasificación. Por eso quedan fuera:

- **Hojas de cálculo (Excel, Google Sheets):** el volumen entra, pero no permiten separar por episodio, armar pipelines ni hacer validación cruzada de forma reproducible.
- **Spark (PySpark) o Dask:** están pensados para datos que no caben en la memoria de una sola máquina. Con 12.6 MB solo agregarían complejidad.

**Candidatas.** Comparamos cinco herramientas gratuitas de uso común en minería de datos: dos basadas en código y tres visuales.

- **Python:** pandas + scikit-learn, en notebooks Jupyter.
- **R:** tidyverse + tidymodels, con Quarto o R Markdown.
- **KNIME:** KNIME Analytics Platform.
- **Orange:** Orange Data Mining.
- **Weka.**

## 2. Matriz ponderada: plataforma principal

### Criterios y pesos

Definimos los pesos antes de puntuar. El soporte metodológico pesa más porque un error ahí (por ejemplo, leakage entre train y validation) invalida los resultados. Los criterios de comodidad pesan menos.

| Criterio | Peso | Qué evalúa | Necesidades |
|---|:-:|---|:-:|
| C1. Soporte metodológico | 30 % | Separación agrupada y estratificada, preprocesamiento ajustado solo con train, validación cruzada agrupada, PR-AUC y umbral | N2, N3, N4 |
| C2. Reproducibilidad y revisión en Git | 20 % | Reejecutar todo con semilla fija y revisar los cambios en un pull request | N5 |
| C3. Transformaciones a medida | 15 % | Parseo de daños, separación de magnitudes y reglas de negocio | N1 |
| C4. Continuidad con el TP1 | 15 % | Cuánto del trabajo ya hecho y verificado se puede reutilizar | — |
| C5. Acceso y trabajo en equipo | 10 % | Abrir y ejecutar sin instalaciones complejas; trabajar varios a la vez | N6 |
| C6. Documentación y comunidad | 10 % | Documentación oficial y ejemplos para resolver problemas | — |

Escala de 1 a 5:

- **5:** lo resuelve directamente con funciones estándar.
- **3:** es posible con trabajo manual o combinando componentes.
- **1:** obliga a hacerlo fuera de la herramienta.

### Matriz

| Criterio (peso) | Python | R | KNIME | Orange | Weka |
|---|:-:|:-:|:-:|:-:|:-:|
| C1. Soporte metodológico (30 %) | 5 | 5 | 3 | 2 | 2 |
| C2. Reproducibilidad y revisión en Git (20 %) | 4 | 5 | 2 | 2 | 2 |
| C3. Transformaciones a medida (15 %) | 5 | 5 | 4 | 2 | 1 |
| C4. Continuidad con el TP1 (15 %) | 5 | 2 | 2 | 2 | 1 |
| C5. Acceso y trabajo en equipo (10 %) | 5 | 4 | 2 | 2 | 2 |
| C6. Documentación y comunidad (10 %) | 5 | 5 | 4 | 3 | 3 |
| **Puntaje ponderado (máx. 5)** | **4.80** | **4.45** | **2.80** | **2.10** | **1.80** |

Puntaje ponderado = Σ (peso × nota). Por ejemplo, Python: 0.30 × 5 + 0.20 × 4 + 0.15 × 5 + 0.15 × 5 + 0.10 × 5 + 0.10 × 5 = 4.80.

### Justificación de las notas

**Python (pandas + scikit-learn)**

- **C1 (5).** El split por episodio ya está hecho con `train_test_split` sobre los episodios. El preprocesamiento va en `Pipeline` + `ColumnTransformer`. `StratifiedGroupKFold` resuelve la validación cruzada agrupada del TF1, y `average_precision_score` y `PrecisionRecallDisplay` cubren la PR-AUC y la curva.
- **C2 (4).** Con la celda de carga adaptada, el notebook se reejecutó completo en local y los resultados coincidieron con los guardados. Le quitamos un punto porque el `.ipynb` es un JSON que incluye las salidas. Al guardar desde Colab se reordenan sus claves, y por eso el commit que agregó la sección 10 (`2e48109`) aparece con +1,509 / −1,430 líneas aunque el contenido nuevo es mucho menor. Revisar un notebook en un pull request es difícil.
- **C3 (5).** pandas ya resolvió el parseo de daños y la separación de `viento_kt` y `granizo_in` (secciones 3.0 y 4).
- **C4 (5).** Todo el TP1 está hecho en Python.
- **C5 (5).** Colab abre el notebook directamente desde GitHub y ya trae las bibliotecas que usamos. En local basta con `pip install -r requirements.txt`.
- **C6 (5).** La guía de usuario de scikit-learn documenta cada clase con ejemplos, y la comunidad es muy amplia.

**R (tidyverse + tidymodels)**

- **C1 (5).** `rsample::group_vfold_cv()` hace validación cruzada agrupada y acepta `strata`. `recipes` y `workflows` ajustan el preprocesamiento dentro de cada fold, y `yardstick::pr_auc()` calcula la PR-AUC. En lo técnico cubre lo mismo que Python.
- **C2 (5).** Quarto y R Markdown son texto plano, así que un pull request muestra solo las líneas que cambiaron. En este criterio supera a nuestro uso de Python.
- **C3 (5).** `dplyr` y `stringr` permiten las mismas transformaciones que pandas.
- **C4 (2).** Habría que reescribir el notebook completo y volver a verificar cada resultado. No le ponemos 1 porque la lógica se traslada casi línea por línea.
- **C5 (4).** Colab ofrece un entorno de R, pero no es el predeterminado y puede que haya que instalar paquetes en cada sesión.
- **C6 (5).** Tiene la documentación de tidymodels.org y el libro abierto *Tidy Modeling with R*.

**KNIME Analytics Platform**

- **C1 (3).** El nodo `X-Partitioner` estratifica por clase, pero no agrupa por episodio. Para eso habría que calcular aparte una columna de fold y recorrerla con un bucle (`Group Loop`). El preprocesamiento sí puede ir dentro del bucle. No encontramos un nodo estándar para la curva precision-recall ni para la PR-AUC: el `Binary Classification Inspector` se centra en la curva ROC y el umbral.
- **C2 (2).** Cada workflow se guarda como una carpeta con un archivo XML de configuración por nodo. Se puede versionar, pero el diff no muestra el cambio de lógica de forma legible.
- **C3 (4).** `String Manipulation`, `Rule Engine` y `Math Formula` cubren el parseo de daños y las reglas, aunque con más pasos que en código.
- **C4 (2).** Habría que rehacer el flujo con nodos.
- **C5 (2).** Hay que instalar la aplicación de escritorio en cada equipo, y no corre en Colab.
- **C6 (4).** Tiene documentación por nodo, un foro activo y ejemplos en KNIME Hub.

**Orange**

- **C1 (2).** `Test and Score` hace validación cruzada estratificada. Su opción *Cross validation by feature* agrupa por una variable, pero con `EPISODE_ID` crearía un fold por episodio (9,846 folds). Entre sus métricas no figura la PR-AUC; sí figuran AUC ROC, accuracy, F1, precision, recall y MCC.
- **C2 (2).** El workflow (`.ows`) es XML, con el mismo problema que en KNIME.
- **C3 (2).** `Feature Constructor` cubre fórmulas simples. El parseo de daños llevaría al widget `Python Script`, es decir, a escribir código de todas formas.
- **C4 (2).** Habría que rehacer el flujo con widgets.
- **C5 (2).** Es una aplicación de escritorio.
- **C6 (3).** Tiene documentación por widget y tutoriales, pero pocos ejemplos para casos como el nuestro.

**Weka**

- **C1 (2).** La validación cruzada estratifica por clase, pero no agrupa. El split por episodio tendría que hacerse fuera de Weka para luego cargar train y test por separado. A favor: `FilteredClassifier` ajusta los filtros solo con los datos de entrenamiento y los resultados incluyen la *PRC Area*.
- **C2 (2).** El Explorer es interactivo y no deja un registro ejecutable. Knowledge Flow o la línea de comandos lo hacen reproducible, pero es más difícil de revisar.
- **C3 (1).** El parseo de daños y la separación de magnitudes tendrían que hacerse antes, en otra herramienta.
- **C4 (1).** Además de rehacer el flujo, seguiríamos dependiendo de otra herramienta para preparar los datos.
- **C5 (2).** Es una aplicación de escritorio (Java).
- **C6 (3).** Cuenta con el libro de referencia de sus autores y con la documentación de la API, pero hay pocos ejemplos recientes.

### Análisis de sensibilidad

Los pesos son una decisión nuestra, así que revisamos si el resultado cambia al moverlos:

| Escenario | Python | R | KNIME | Orange | Weka |
|---|:-:|:-:|:-:|:-:|:-:|
| Pesos base | **4.80** | 4.45 | 2.80 | 2.10 | 1.80 |
| Pesos iguales (1/6 cada criterio) | **4.83** | 4.33 | 2.83 | 2.17 | 1.83 |
| Sin C4 (sin contar lo ya hecho; los demás pesos se reescalan) | 4.76 | **4.88** | 2.94 | 2.12 | 1.94 |

- **Las herramientas visuales quedan detrás en todos los escenarios.** Su límite está en C1: ninguna resuelve directamente la separación agrupada por episodio, que es la decisión que más protege la validez de los resultados.
- **Entre Python y R, la diferencia es pequeña y depende de la continuidad.** Si no contáramos el trabajo ya hecho, R quedaría ligeramente adelante por la legibilidad de sus documentos en Git. Para este problema no hay una ventaja técnica de Python sobre R. Elegimos Python porque el TP1 ya está implementado y verificado en ese lenguaje, y porque Colab lo ejecuta sin configuración adicional.

### Decisión

Usamos **Python (pandas + scikit-learn)** como plataforma principal. Para reducir su punto débil (C2):

- moveremos a `src/`, como archivos `.py`, el código que se reutilice en el TF1 (carga, target y pipeline), para que los cambios de lógica se revisen como código;
- en los pull requests que modifiquen el notebook, indicaremos qué secciones cambiaron, porque el diff del `.ipynb` no lo muestra con claridad. Para revisarlos por celda se puede usar `nbdime`.

## 3. Entorno de ejecución

| Aspecto | Google Colab | JupyterLab local |
|---|---|---|
| Instalación | Ninguna: abre el notebook desde GitHub | Python + `pip install -r requirements.txt` |
| Datos | Descarga el archivo desde la URL de NOAA | Lee `data/raw/`, verificado con SHA-256 |
| Versiones de bibliotecas | Las define Google y cambian con el tiempo | Las define el equipo |
| Capacidad | Suficiente para 72 mil filas | Suficiente para 72 mil filas |
| Trabajo en equipo | Basta con compartir un enlace | Cada integrante configura su máquina |

**Decisión:** usar los dos, con roles distintos.

- **Colab:** para el trabajo diario, porque cualquier integrante puede ejecutar el notebook sin instalar nada.
- **Ejecución local:** para comprobar la reproducibilidad antes de cerrar cada entrega. Así se verificó el TP1, con pandas 2.2.3 y 3.0.6.

En ambos casos, la versión oficial es la de GitHub.

**Pendiente:** hoy la celda de carga monta Google Drive, así que el notebook solo corre en Colab sin cambios. Hay que adaptarla para que lea `data/raw/` en local y descargue desde NOAA en Colab (ver README).

## 4. Bibliotecas y herramientas de apoyo

| Etapa | Elegida | Alternativas consideradas | Motivo |
|---|---|---|---|
| Carga y manipulación | pandas | polars; Dask o PySpark | 72,360 × 51 entra con holgura en memoria. polars es más rápido, pero con este volumen la diferencia no se nota, y scikit-learn y seaborn trabajan directamente con DataFrames de pandas. Dask y PySpark son para datos que no caben en memoria. |
| Visualización | matplotlib + seaborn | plotly | Los gráficos estáticos quedan guardados en el notebook, se ven en GitHub y se pueden exportar a `reports/figures/`. Los gráficos interactivos de plotly no se muestran en la vista de notebooks de GitHub. |
| Preprocesamiento y modelos | scikit-learn | statsmodels; XGBoost o LightGBM | scikit-learn reúne en una misma API el pipeline, la separación agrupada, `class_weight` y las métricas. statsmodels está orientado a la inferencia (coeficientes y p-valores) más que a validar modelos predictivos. Para boosting usaremos primero `HistGradientBoostingClassifier`, que ya viene en scikit-learn. |
| Evaluación | `sklearn.metrics` | — | `average_precision_score`, `roc_auc_score`, `PrecisionRecallDisplay` y `ConfusionMatrixDisplay` cubren las métricas de la sección 9. |
| Versionado y colaboración | Git + GitHub, con ramas y pull requests | Google Drive | GitHub guarda el historial y permite revisar antes de integrar (por ejemplo, el PR #1). Una copia en Drive no tiene un historial revisable y no es la versión oficial. |

## 5. Decisiones pendientes para el TF1

| Decisión | Opciones | Cómo decidiremos |
|---|---|---|
| Modelo de boosting | `HistGradientBoostingClassifier`, XGBoost, LightGBM | Empezar por el de scikit-learn, que no agrega dependencias. Cambiar a otro solo si mejora la PR-AUC de la validación cruzada agrupada en más que la variación entre folds. |
| Interpretabilidad | `permutation_importance` (scikit-learn), SHAP | Primero la importancia por permutación, como indica el plan de la sección 10.3. Usar SHAP solo si hace falta explicar predicciones individuales. |
| Aplicación o API de demostración | Streamlit, Gradio, FastAPI | Comparar el tiempo de desarrollo y la posibilidad de desplegarla sin costo. Además, considerar quién la usará: si es una persona, conviene una interfaz (Streamlit o Gradio); si es otro sistema, una API (FastAPI). |

## Fuentes

- scikit-learn, [`StratifiedGroupKFold`](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.StratifiedGroupKFold.html) y [`Pipeline`](https://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html).
- tidymodels, [`group_vfold_cv()`](https://rsample.tidymodels.org/reference/group_vfold_cv.html).
- KNIME, [nodo `X-Partitioner`](https://nodepit.com/node/org.knime.base.node.meta.xvalidation.XValidatePartitionerFactory) y [*Visual Scoring Techniques for Classification Models*](https://www.knime.com/blog/visual-scoring-techniques-for-classification-models).
- Orange, [widget `Test and Score`](https://orangedatamining.com/widget-catalog/evaluate/testandscore/).
- Weka, [documentación de `Evaluation`](https://weka.sourceforge.io/doc.stable/weka/classifiers/Evaluation.html).
