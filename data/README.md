# Datos

Los archivos de datos **no se versionan en Git**. El `.gitignore` excluye todo el contenido de `data/raw/` y `data/processed/` (salvo los `.gitkeep` que conservan las carpetas). Cada integrante descarga el archivo RAW desde la fuente oficial y lo coloca en `data/raw/`; el nombre exacto y el SHA-256 de abajo garantizan que todos trabajamos con el mismo archivo.

## Fuente

| | |
|---|---|
| Dataset | Storm Events Database, archivo de detalle de eventos (`StormEvents_details`) |
| Institución | NOAA – National Centers for Environmental Information (NCEI). Los registros los prepara el National Weather Service (NWS). |
| Página oficial | https://www.ncei.noaa.gov/stormevents/ |
| Directorio de descarga (CSV) | https://www.ncei.noaa.gov/pub/data/swdi/stormevents/csvfiles/ |
| Documentación de columnas | [Storm-Data-Bulk-csv-Format.pdf](https://www.ncei.noaa.gov/pub/data/swdi/stormevents/csvfiles/Storm-Data-Bulk-csv-Format.pdf) |

## Archivo utilizado

| | |
|---|---|
| Archivo | `StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz` |
| Año de los eventos | 2025 |
| Versión | creada por NCEI el 2026-08-19 |
| Tamaño | 12,585,209 bytes (gzip, no descomprimir) |
| SHA-256 | `d9b46b4c6aae554723cadbb9691f3d5258371c02e530ba815d6fe41e4550149f` |
| Contenido | 72,360 eventos (filas) × 51 columnas |

En el nombre del archivo, `d2025` es el año de los datos y `c20260819` la fecha de creación del archivo, según la documentación de NOAA.

## Descarga

1. Descargar el archivo desde
   https://www.ncei.noaa.gov/pub/data/swdi/stormevents/csvfiles/StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz
2. Guardarlo, **sin descomprimir**, en `data/raw/`:

   ```
   data/raw/StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz
   ```

3. Verificar que el SHA-256 coincide con el de la tabla anterior:

   | Sistema | Comando |
   |---|---|
   | Windows (PowerShell) | `Get-FileHash data\raw\StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz -Algorithm SHA256` |
   | Linux | `sha256sum data/raw/StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz` |
   | macOS | `shasum -a 256 data/raw/StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz` |

Los pasos 1 y 2 también se pueden hacer por terminal, desde la raíz del repositorio:

```bash
# Linux, macOS o Git Bash
curl -L -o data/raw/StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz https://www.ncei.noaa.gov/pub/data/swdi/stormevents/csvfiles/StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz
```

```powershell
# Windows PowerShell
Invoke-WebRequest -Uri "https://www.ncei.noaa.gov/pub/data/swdi/stormevents/csvfiles/StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz" -OutFile "data\raw\StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz"
```

## Si el archivo deja de estar disponible

Al 2026-10-05, el directorio de descarga contiene una sola versión del archivo por año. Cuando NCEI regenera un año, la fecha `c` del nombre cambia y la versión anterior puede dejar de estar disponible. En ese caso:

- no reemplazar el archivo por la versión nueva sin avisar: los registros pueden haber sido revisados y los resultados del notebook cambiarían;
- pedir una copia al equipo y comprobar el SHA-256;
- si el equipo decide usar otra versión, registrar aquí el nuevo nombre, su SHA-256 y el motivo del cambio.

Por eso conviene que el equipo conserve una copia del archivo exacto en una carpeta compartida.

## Condiciones de uso

La ficha de metadatos de NCEI para este dataset ([`gov.noaa.ncdc:C00510`](https://www.ncei.noaa.gov/access/metadata/landing-page/bin/iso?id=gov.noaa.ncdc:C00510)) no indica una licencia específica. En su campo *Use Constraints* pide "Cite dataset when used as a source" y advierte que "the NWS does not guarantee the accuracy or validity of the information". Por eso citamos la fuente en el notebook y en el informe, y tratamos los montos de daño como estimaciones.

Cita sugerida (formato propio; NCEI no define uno):

> NOAA National Centers for Environmental Information (NCEI). *Storm Events Database*, archivo `StormEvents_details-ftp_v1.0_d2025_c20260819.csv.gz`. https://www.ncei.noaa.gov/stormevents/

## Carpetas

- `raw/`: archivos originales, tal como se descargan. No se modifican.
- `processed/`: datos derivados que generen los notebooks. Se pueden regenerar a partir de `raw/`, por eso tampoco se versionan.
