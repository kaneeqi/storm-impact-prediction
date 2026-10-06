# Predicción temprana del impacto de eventos meteorológicos extremos

**Curso:** Data Mining Tools – CC209
**Universidad Peruana de Ciencias Aplicadas (UPC)**
**Docente:** Carlos Fernando Montoya Cubas

## Descripción

Proyecto integrador del curso. Desarrollamos un flujo de Data Science (problema y datos → EDA → preparación → modelamiento → evaluación) sobre la Storm Events Database de NOAA/NCEI, que registra eventos meteorológicos severos ocurridos en Estados Unidos junto con sus víctimas y daños estimados.

Por ahora el repositorio contiene la definición del problema, el análisis exploratorio y el análisis de calidad de los datos. Todavía no hay modelos.

## Objetivo actual

Analizar los datos y, posteriormente, desarrollar modelos capaces de estimar tempranamente el riesgo de alto impacto documentado, usando solo información compatible con el momento inicial del evento.

La variable objetivo la define el equipo (no es una definición oficial de NOAA): un evento es de alto impacto documentado si registra al menos una muerte o lesión, directa o indirecta, o un daño documentado a propiedad más cultivos de al menos USD 1,000,000. En 2025 cumplen esta condición 1,014 de 72,360 eventos (1.40 %).

## Dataset

- **Fuente:** Storm Events Database, de NOAA – National Centers for Environmental Information (NCEI). [Página oficial](https://www.ncei.noaa.gov/stormevents/).
- **Año:** 2025.
- **Archivo:** `StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz`
- **Tamaño:** 72,360 eventos × 51 columnas.
- **Unidad de análisis:** un evento (`EVENT_ID`). `EPISODE_ID` agrupa los eventos de un mismo episodio meteorológico.

El archivo no se versiona en Git. Las instrucciones de descarga y verificación están en [`data/README.md`](data/README.md).

## Estado

Trabajo Parcial (TP1) en desarrollo.

**Hecho:**

- definición del problema;
- preparación inicial (construcción de la variable objetivo y transformaciones preliminares);
- análisis exploratorio (EDA);
- análisis de calidad de los datos.

**Pendiente:**

- separación train / validation / test;
- pipeline de preprocesamiento;
- baseline;
- modelos;
- evaluación;
- cierre del TP1 (hallazgos, limitaciones y plan hacia el TF1).

## Actualización del avance (TP1)

Después del análisis de calidad, el notebook `TP1.ipynb` se completó con las secciones 5 a 10. Es decir, los puntos listados arriba como pendientes (separación de los datos, pipeline, baselines, modelos, evaluación y cierre del TP1) ya están desarrollados en el notebook. Las siguientes secciones resumen lo nuevo.

## Decisiones principales

- **Predictores (compatibles con el momento inicial del evento):** `EVENT_TYPE`, `STATE`, `MONTH_NAME`, `MAGNITUDE_TYPE`, `viento_kt` y `granizo_in`.
- **Variables excluidas por leakage:** `SOURCE`, escala EF y medidas del tornado, duración, víctimas, daños y narrativas, porque contienen información posterior al inicio del evento o se asignan según el daño. La auditoría está al final de la sección 4 del notebook.
- **Separación de datos:** por episodio (`EPISODE_ID`), estratificada, con `random_state=42`. Queda 71.9 % train, 13.8 % validation y 14.4 % test, con prevalencia cercana a 1.4 % en los tres. El test queda sellado hasta la evaluación final del TF1.
- **Preprocesamiento:** `Pipeline` + `ColumnTransformer` de scikit-learn. Todo se ajusta solo con train.
- **Baselines:** `DummyClassifier` (prevalencia) y tasa de alto impacto por `EVENT_TYPE`.
- **Modelos preliminares:** regresión logística y Random Forest, ambos con `class_weight="balanced"` y sin ajuste de hiperparámetros.
- **Métrica principal:** PR-AUC (*Average Precision*). Se reportan también ROC-AUC, precision, recall, F1 y F2 con umbral 0.5 como referencia.
- **Herramientas:** Python (pandas + scikit-learn) en notebooks, ejecutados en Colab y verificados en local. La comparación con R, KNIME, Orange y Weka mediante una matriz ponderada está en [`docs/matriz_decision_herramientas.md`](docs/matriz_decision_herramientas.md).

## Resultados preliminares (validation)

Validation tiene 9,980 eventos y 140 positivos.

| Modelo | PR-AUC | ROC-AUC | Precision | Recall | F1 | F2 |
|---|---|---|---|---|---|---|
| Dummy | 0.014 | 0.500 | 0.000 | 0.000 | 0.000 | 0.000 |
| Baseline por `EVENT_TYPE` | 0.145 | 0.806 | 0.667 | 0.029 | 0.055 | 0.035 |
| Regresión logística | 0.132 | 0.815 | 0.046 | 0.657 | 0.086 | 0.180 |
| Random Forest | 0.252 | 0.845 | 0.089 | 0.621 | 0.156 | 0.283 |

- Solo Random Forest supera con claridad al baseline por tipo de evento. La regresión logística queda prácticamente igual.
- Con umbral 0.5 los modelos detectan más de la mitad de los positivos, pero la mayoría de las alertas son falsas. El umbral se elegirá en el TF1, sin usar el test.
- Son resultados **preliminares**: con 140 positivos en validation, las diferencias pequeñas no son concluyentes.

## Hallazgos y limitaciones

**Hallazgos principales**

- El problema está muy desbalanceado (1.40 % de positivos) y el target depende sobre todo de las víctimas: 779 de los 1,014 positivos tienen víctimas.
- El tipo de evento es la variable más informativa y ya da un PR-AUC de 0.145 por sí solo, por lo que el baseline fue difícil de superar.
- Las variables que más se asocian con el alto impacto (`SOURCE`, escala EF, duración, narrativas) no se pueden usar porque son posteriores al inicio del evento.

**Limitaciones principales**

- El target es "alto impacto *documentado*": el 20.6 % de los eventos no trae información de daño y se trata como 0, por lo que hay positivos reales que quedan como negativos.
- Se usa un solo año (2025) y solo datos de EE. UU.
- Los registros de NOAA ya fueron revisados después del evento, por lo que los resultados son una estimación optimista de un uso en tiempo real.
- Validation tiene pocos positivos (140), así que las comparaciones entre modelos no son concluyentes.
- No hay ajuste de hiperparámetros ni umbral elegido, y falta interpretar el modelo y analizar sus errores.

El detalle completo está en la sección 10 del notebook.

## Plan hacia el TF1

- validación cruzada agrupada por episodio, con intervalos de confianza;
- elección del umbral sin usar el test;
- ajuste de hiperparámetros;
- otros modelos (por ejemplo, boosting) y regresión logística con interacciones;
- evaluar más predictores compatibles con el inicio del evento;
- interpretabilidad y análisis de errores;
- pruebas de sensibilidad (umbral de daño y víctimas indirectas);
- separación por fechas como comprobación adicional;
- evaluación en test una sola vez, con modelo y umbral congelados;
- aplicación o API de demostración.

## Estructura del repositorio

```
storm-impact-prediction/
├── data/
│   ├── README.md       fuente, descarga y verificación del dataset
│   ├── raw/            archivo original de NOAA (no se versiona)
│   └── processed/      datos derivados (no se versionan)
├── notebooks/
│   └── TP1.ipynb       notebook del Trabajo Parcial, con sus resultados
├── src/                código reutilizable (vacío por ahora)
├── models/             modelos entrenados (vacío por ahora)
├── reports/
│   ├── figures/        figuras exportadas
│   └── slides/         presentaciones del TP1 y del TF1
├── docs/               documentación del proyecto (matriz de decisión de herramientas)
├── requirements.txt    dependencias de Python
└── README.md
```

## Ejecución

1. Clonar el repositorio:

   ```bash
   git clone https://github.com/kaneeqi/storm-impact-prediction.git
   cd storm-impact-prediction
   ```

2. (Opcional) Crear y activar un entorno virtual:

   ```bash
   python -m venv .venv
   .venv\Scripts\activate        # Windows
   source .venv/bin/activate     # macOS / Linux
   ```

3. Instalar las dependencias:

   ```bash
   pip install -r requirements.txt
   ```

4. Descargar el dataset siguiendo [`data/README.md`](data/README.md).
5. Colocarlo, sin descomprimir, en `data/raw/`.
6. Abrir el notebook:

   ```bash
   jupyter lab notebooks/TP1.ipynb
   ```

**Limitación actual:** la celda de carga de `TP1.ipynb` importa `google.colab` y monta Google Drive, por lo que hoy el notebook solo se ejecuta en Colab. Se ajustará para que en local lea el archivo desde `data/raw/` y en Colab lo descargue desde NOAA, sin depender de Drive.

**Versiones probadas:** con la celda de carga adaptada para leer el archivo local, el notebook se ejecutó completo con pandas 2.2.3 y con pandas 3.0.6, y los resultados numéricos coincidieron con los guardados en el notebook. Con pandas 3 solo cambia la presentación de algunos tipos de dato (`str` en lugar de `object`) y el orden de algunos empates en los conteos.

## Google Colab

GitHub es la fuente oficial del código y del notebook. Para trabajar en Colab:

1. Abrir el notebook desde GitHub: en Colab, *Archivo → Abrir notebook → GitHub*, o directamente [este enlace](https://colab.research.google.com/github/kaneeqi/storm-impact-prediction/blob/main/notebooks/TP1.ipynb) (si el repositorio es privado, Colab pedirá autorizar el acceso a GitHub).
2. No hace falta instalar `requirements.txt`: Colab ya incluye pandas, numpy, matplotlib y seaborn.
3. La celda de carga lee el archivo directamente desde la URL oficial de NOAA. Hoy, además, monta Google Drive para guardar ahí una copia del CSV; ese paso no es necesario para el análisis y se eliminará.
4. Los cambios hechos en Colab deben volver al repositorio (descargar el `.ipynb` y hacer commit, o *Archivo → Guardar una copia en GitHub*). Una copia que quede solo en Drive no es la versión oficial.

## Integrantes

- Eduardo Bravo
- Francesca Nicole Bances Torres
- (por completar)

## Uso de IA generativa

En este proyecto se han utilizado herramientas de IA generativa como apoyo para revisar código, mejorar la documentación y analizar alternativas. Las decisiones, interpretaciones y conclusiones son responsabilidad del equipo, que debe poder explicarlas y sustentarlas con la evidencia del repositorio.
