# Predicción temprana del impacto de eventos meteorológicos extremos

## Curso

Data Mining Tools – CC209  
Universidad Peruana de Ciencias Aplicadas (UPC)  
Docente: Carlos Fernando Montoya Cubas

## Descripción

Proyecto integrador del curso. Desarrollamos un flujo de Data Science (problema y datos → EDA → preparación → modelamiento → evaluación) sobre la Storm Events Database de NOAA/NCEI, que registra eventos meteorológicos severos ocurridos en Estados Unidos junto con sus víctimas y daños estimados.

Por ahora el repositorio contiene la definición del problema, el análisis exploratorio y el análisis de calidad de los datos. **Todavía no hay modelos.**

## Objetivo actual

Analizar los datos y, posteriormente, desarrollar modelos capaces de estimar tempranamente el riesgo de **alto impacto documentado**, usando solo información compatible con el momento inicial del evento.

La variable objetivo la define el equipo (no es una definición oficial de NOAA): un evento es de alto impacto documentado si registra al menos una muerte o lesión, directa o indirecta, o un daño documentado a propiedad más cultivos de al menos USD 1,000,000. En 2025 cumplen esta condición 1,014 de 72,360 eventos (1.40 %).

## Dataset

- **Fuente:** Storm Events Database, de NOAA – National Centers for Environmental Information (NCEI). [Página oficial](https://www.ncei.noaa.gov/stormevents/).
- **Año:** 2025.
- **Archivo:** `StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz`
- **Tamaño:** 72,360 eventos × 51 columnas.
- **Unidad de análisis:** un evento (`EVENT_ID`). `EPISODE_ID` agrupa los eventos de un mismo episodio meteorológico.

El archivo no se versiona en Git. Las instrucciones de descarga y verificación están en [data/README.md](data/README.md).

## Estado

**Trabajo Parcial (TP1) en desarrollo.**

Hecho:

- definición del problema;
- preparación inicial (construcción de la variable objetivo y transformaciones preliminares);
- análisis exploratorio (EDA);
- análisis de calidad de los datos.

Pendiente:

- separación train / validation / test;
- pipeline de preprocesamiento;
- baseline;
- modelos;
- evaluación;
- cierre del TP1 (hallazgos, limitaciones y plan hacia el TF1).

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
├── docs/               documentación del proyecto
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

4. Descargar el dataset siguiendo [data/README.md](data/README.md).
5. Colocarlo, sin descomprimir, en `data/raw/`.
6. Abrir el notebook:

   ```bash
   jupyter lab notebooks/TP1.ipynb
   ```

> **Limitación actual:** la celda de carga de `TP1.ipynb` importa `google.colab` y monta Google Drive, por lo que hoy el notebook solo se ejecuta en Colab. Se ajustará para que en local lea el archivo desde `data/raw/` y en Colab lo descargue desde NOAA, sin depender de Drive.

Versiones probadas: con la celda de carga adaptada para leer el archivo local, el notebook se ejecutó completo con pandas 2.2.3 y con pandas 3.0.6, y los resultados numéricos coincidieron con los guardados en el notebook. Con pandas 3 solo cambia la presentación de algunos tipos de dato (`str` en lugar de `object`) y el orden de algunos empates en los conteos.

## Google Colab

GitHub es la fuente oficial del código y del notebook. Para trabajar en Colab:

1. Abrir el notebook desde GitHub: en Colab, *Archivo → Abrir notebook → GitHub*, o directamente
   https://colab.research.google.com/github/kaneeqi/storm-impact-prediction/blob/main/notebooks/TP1.ipynb
   (si el repositorio es privado, Colab pedirá autorizar el acceso a GitHub).
2. No hace falta instalar `requirements.txt`: Colab ya incluye pandas, numpy, matplotlib y seaborn.
3. La celda de carga lee el archivo directamente desde la URL oficial de NOAA. Hoy, además, monta Google Drive para guardar ahí una copia del CSV; ese paso no es necesario para el análisis y se eliminará.
4. Los cambios hechos en Colab deben volver al repositorio (descargar el `.ipynb` y hacer commit, o *Archivo → Guardar una copia en GitHub*). Una copia que quede solo en Drive no es la versión oficial.

## Integrantes

- Eduardo Bravo
- _(por completar)_
- _(por completar)_

<!-- TODO: agregar los nombres completos de los otros dos integrantes (y sus códigos UPC, si el docente los pide). -->

## Uso de IA generativa

En este proyecto se han utilizado herramientas de IA generativa como apoyo para revisar código, mejorar la documentación y analizar alternativas. Las decisiones, interpretaciones y conclusiones son responsabilidad del equipo, que debe poder explicarlas y sustentarlas con la evidencia del repositorio.
