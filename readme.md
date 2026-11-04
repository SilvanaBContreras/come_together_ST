# Come Together - Series Temporales
Autora: Silvana Contreras

Trabajo práctico final de la materia **Data Mining de Series Temporales**.
Maestría en Data Mining, Facultad de Ciencias Exactas y Naturales, Universidad de Buenos Aires.

**The long and winding road**: búsqueda de perfiles en las trayectorias digitales de artistas musicales entre marzo de 2022 y marzo de 2026, a partir de métricas de streaming y redes sociales de la API de Chartmetric.

## Objetivo

Extender un análisis previo basado en perfiles digitales estáticos hacia un enfoque de series temporales. La unidad de análisis es el artista: cada artista es una trayectoria mensual multivariada.

## Notebooks

Se corren en orden. Todos usan R (kernel `ir`) con `data.table`.

| Notebook | Qué hace | Salida principal |
|---|---|---|
| `01_CT_ST_extraccion_API.ipynb` | Selección de la muestra por deciles de popularidad, extracción de Fan Stats y Playlist Evolution desde la API de Chartmetric, mensualización (último valor del mes) y consolidación. | Dataset mensual crudo `artistas_ST_raw_<n>_long.csv` / `_wide.csv` |
| `02_CT_ST_construccion_dataset.ipynb` | Cobertura por variable, cobertura conjunta (con el diagnóstico del pozo de Twitter), elección de variables y armado del panel con NA explícitos. | Panel `artistas_ST_<n>de<m>_top5000_22_26_na_long.csv` / `_wide.csv` |
| `03_CT_ST_preprocesamiento_filtros.ipynb` | Validación del muestreo mensual, limpieza de picos y series anómalas, interpolación de faltantes, transformación, suavizado con media móvil y filtro pasa-bajos FFT, features por artista. | Panel preprocesado y tabla de features |
| `04_CT_ST_clustering_distancias.ipynb` | Clustering de trayectorias por etapas. Etapa 1: matrices de distancia euclídea, DTW y SBD. | Matrices de distancia (local) |
| `05_CT_ST_clustering_particiones.ipynb` | Etapa 2: jerárquico (Ward), spectral y PAM sobre las tres matrices, k de 2 a 8, y diagnóstico de tamaños. | Particiones (local) |

El notebook 01 tiene el interruptor `EJECUTAR_EXTRACCION_API` (sección 1). Con `FALSE` (valor por defecto) corre completo desde el cache local, sin hacer requests a la API.

## Datos y licencia

Los datos provienen de Chartmetric bajo licencia académica: **no se publican datos crudos ni registros que permitan reconstruir métricas individuales**. Por eso:

- los CSV y RDS con datos están excluidos del repo (`.gitignore`);
- los notebooks se suben **sin salidas**, mediante el filtro `limpiar-salidas` de `.gitattributes`;
- el código de extracción se conserva para la replicabilidad, con credenciales propias en `.Renviron` (excluido del repo).

### Activar el filtro de salidas en un clon nuevo

La configuración del filtro no viaja con el repo. En cada clon, antes del primer commit:

```
git config filter.limpiar-salidas.clean "jupyter nbconvert --ClearOutputPreprocessor.enabled=True --to=notebook --stdin --stdout --log-level=ERROR"
git config filter.limpiar-salidas.smudge cat
git config filter.limpiar-salidas.required true
```
