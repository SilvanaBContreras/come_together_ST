# Inventario del material de la materia

Data Mining de Series Temporales (Marcelo Risk y Federico Albanese), Maestría en Data Mining, FCEyN UBA, 2026.
Relevado el 1/10/2026 sobre los 13 PDFs, 7 notebooks y 1 Rmd de `material_material/`. Este archivo reemplaza la relectura del material: las sesiones siguientes trabajan sobre él.

Fechas: presentación de avances el lunes 5/10/2026; video y presentación oral el 23/11 y el 30/11/2026. Consigna del TP: aplicar métodos vistos en clase para corroborar o refutar una hipótesis. Estructura sugerida del video (≈15 min): carátula, introducción, hipótesis y objetivos, material y métodos, resultados, discusión y conclusiones, bibliografía, anexos.

## 1. Qué hay y qué no hay en el material

- Todo el código práctico es R base (`stats`), salvo causalidad (Python, `tfcausalimpact`) y deep learning (clase 11, código en capturas, no extraíble como texto).
- No hay ninguna clase de clustering, DTW, medidas de distancia entre series ni detección de puntos de cambio. Para esas etapas del TP, la materia aporta los insumos (correlación cruzada, espectros, derivadas, wavelets, pruebas de estacionaridad) y el algoritmo de agrupamiento (jerárquico, k-means) viene del resto de la maestría.
- `Clase_02_Dominio_Frecuencia_DMST.pdf` no trae la teoría de Fourier: es la versión 2026 (R y Python) de filtrado en el dominio de la frecuencia, igual que la clase 04. La clase 09 (PDF) y `08_Clase_Tiempo_Frecuencia_DMST.Rmd` son el mismo contenido.
- Paquetes instalados hoy en R 4.4.3: `data.table`, `stats`, `lmtest`, `zoo`, `cluster`, `mclust`. Faltan `tseries`, `forecast`, `fpp` y `wavethresh` (usados en clases 09 y 10).

## 2. Temas por clase

| Clase | Tema | Algoritmos y conceptos | Funciones y paquetes de R | Aplica al panel |
|---|---|---|---|---|
| 01 Intro | Organización y consigna del TP | Hipótesis verificable, experimentos para validarla | | Marco del video |
| 01 Representación | Definición de serie temporal; dominios de análisis | Serie como función del tiempo a intervalos regulares; métodos en DT, DF y tiempo-frecuencia; media, DE, rango, histograma | `ts(start, frequency)`, `frequency`, `deltat`, `time`, `hist`, `boxplot`, `read.csv`/`write.csv` | Sí: definición para el guion ("señal a intervalos regulares") y `ts(..., frequency = 12)` |
| 01 Señales | Analógico y digital | Muestreo en tiempo y amplitud; teorema del muestreo F_muestreo ≥ 2·F_máxima | | Sí, conceptual: con un dato por mes, la oscilación más rápida observable dura 2 meses |
| 02 y 04 Filtrado DF | Filtrado en el dominio de la frecuencia | FFT directa, producto por máscara, FFT inversa; pasa-bajos, pasa-altos, pasa-banda, elimina-banda; espectro simétrico (índice k y su espejo N-k); selectivo pero offline (necesita toda la serie) | `fft`, `fft(..., inverse = TRUE)/N`, `Mod`, `Re` | Sí: pasa-bajos para la tendencia, ya probado en `music_industry_ST_filtros.ipynb` sobre un artista |
| 03 Media móvil | Suavizado y descomposición con MA | MA(n) como FIR pasa-bajos imperfecto (lóbulos laterales); más taps, más suavizado y más distorsión de picos; verificación por FFT; descomposición por diferencia de MA (MA7 - MA21 aísla el ciclo intermedio) | `filter(x, rep(1/n, n), circular = TRUE)` | Sí: suavizado ya probado (3, 6 y 12 meses con `frollmean`); descomposición MA corta - MA larga para separar tendencia de fluctuación |
| 05 Convolución y correlación | Convolución, correlación cruzada, autocorrelación | Convolución en DT = producto en DF; convolución circular por FFT (envoltorio en bordes); correlación = convolución conjugada; el lag del pico de la ccf mide el desfase y su signo indica quién adelanta; acf revela periodicidad | `convolve(conj = FALSE/TRUE)`, `ccf(lag.max)`, `acf` | Sí, clave: similaridad entre trayectorias con desfase (responde la pregunta abierta "uno explota en 2023 y otro en 2025") y acople entre variables de un mismo artista |
| 06 Interpolación | Remuestreo e imputación | Cuatro casos: subir frecuencia, bajar frecuencia (con pasa-bajos previo para no introducir alias), muestreo irregular a uniforme, muestras perdidas; lineal vs spline cúbica; comparación de espectros | `approx(x, y, n o xout)`, `spline(x, y, n)` | Sí: imputación de huecos (0,5% de celdas, máximo 9 meses seguidos) |
| 07 Causalidad | Granger e impacto causal | Granger: una serie aporta información sobre valores futuros de otra (AR con rezagos); no controla terceras variables; sirve para predecir, no para intervenir; contrafáctico, diferencias en diferencias, Causal Impact con modelos bayesianos estructurales (Brodersen et al. 2015) | Python `causalimpact`; en R, `lmtest::grangertest` (instalado, no usado en clase) | Opcional: Granger entre playlists y seguidores dentro de un perfil; fuera del alcance de un video de 15 min |
| 08 FIR e IIR | Filtros digitales | FIR: no recursivo, estable, coeficientes que suman 1; Hanning (1/4, 1/2, 1/4), FIR5, Hamming; derivadas FIR de 2, 3 y 4 muestras (pasa-altos); IIR recursivo y(n) = a·x(n) - b·y(n-1), puede ser inestable | `filter(x, c(0.25, 0.5, 0.25))`, `filter(x, c(0.5, 0, -0.5))`, loop para IIR | Sí: Hanning como suavizado con menos lóbulos que el MA; derivada FIR sobre log1p = tasa de crecimiento mensual, base para segmentar explosiones |
| 09 Tiempo-frecuencia | Wavelets | Fourier no localiza en el tiempo; para series no estacionarias, descomposición tiempo-frecuencia; algoritmo piramidal de Mallat; coeficientes de aproximación (C) y detalle (D) por nivel | `wavethresh::wd(x, filter.number = 4, family = "DaubExPhase")`, `accessC`, `accessD` | Parcial: `wd` exige largo potencia de 2 (49 meses no lo es: recortar a 32 o completar a 64); coeficientes de detalle ubican en el tiempo los cambios bruscos. Paquete no instalado |
| 10 Forecasting | Estacionaridad y modelos clásicos | Estacionaridad (media y varianza constantes); ADF (H0 raíz unitaria) y KPSS (H0 estacionaria) combinados en tabla de decisión; `diff` y detrend por `lm`; regresión con tendencia, Durbin-Watson; descomposición clásica y STL; ARMA, espectro AR vs periodograma; ARIMA(p, d, q), orden por ACF/PACF y AIC/AICc/BIC | `tseries::adf.test`, `kpss.test`, `diff`, `lm`, `forecast::tslm`, `forecast`, `decompose`, `stl`, `seasadj`, `spec.pgram`, `spec.ar`, `auto.arima`, `Arima`, `Acf`, `Pacf`, `lmtest::dwtest` | Sí para diagnóstico: ADF/KPSS muestran que los stocks no son estacionarios y que `diff` sobre log1p sí lo es (justifica la transformación). Pendiente o coeficientes AR como rasgos. El forecasting en sí no es objetivo del TP |
| 11 Forecasting 2 | Deep learning y Prophet | Perceptrón, backpropagation, CNN 1D, RNN, LSTM; Prophet (tendencia + estacionalidad + feriados, Taylor y Letham 2018) | Código en capturas | No aplica: 49 puntos por serie y objetivo descriptivo, no predictivo |

## 3. Herramientas de la materia por etapa del TP

**Imputación de huecos (clase 06).** `approx(x = meses_con_dato, y = valores, xout = todos_los_meses, rule = 2)` por artista y variable. Lineal antes que spline: los stocks (seguidores, suscriptores) crecen casi monótonos y la spline cúbica puede oscilar y hasta bajar dentro de un hueco largo. `rule = 2` repite el valor del extremo en huecos al inicio o al final (con `rule = 1` quedan NA). Imputar sobre log1p equivale a interpolar el crecimiento relativo, más coherente con series que crecen en proporción. Conservar una marca de celda imputada.

**Transformación (clases 01 y 10).** log1p para llevar a escala comparable niveles que van de miles a decenas de millones; centrado por artista (restar la media de su serie) para que el clustering compare forma y no tamaño. ADF/KPSS antes y después como evidencia (requiere instalar `tseries`). Alternativa: diferencia de log1p (`diff`), que da tasas de crecimiento mensual y es estacionaria, pero amplifica el ruido mes a mes (es un pasa-altos, clase 08).

**Suavizado (clases 03, 04, 08).** Media móvil centrada (`frollmean` en vez de `filter(..., circular = TRUE)`, porque una trayectoria no es periódica y el filtro circular mezcla el final con el comienzo), Hanning, o pasa-bajos FFT. Antes de la FFT conviene quitar la tendencia lineal (`lm`, clase 10): la FFT supone serie periódica y el salto entre el último y el primer mes de una serie creciente reparte energía en todas las frecuencias.

**Similaridad entre trayectorias (clase 05).** Máximo de la `ccf` entre dos artistas dentro de un rango de lags, como similaridad que tolera desfase; distancia = 1 - ccf máxima. Lag 0 equivale a correlación de Pearson entre formas. La ccf ya normaliza, así que no depende del nivel del artista. Con varias variables: una matriz por variable y promedio, o una sola variable resumen.

**Rasgos por artista (clases 03, 08, 09, 10).** Pendiente de la tendencia (`lm`), volatilidad del residuo (serie menos MA), crecimiento máximo mensual (derivada FIR), mes del crecimiento máximo, energía en baja vs alta frecuencia (FFT), coeficientes AR (`spec.ar` / `ar`), energía de coeficientes wavelet de detalle.

**Clustering (fuera del material).** `hclust` sobre la matriz de distancias ccf y `kmeans` sobre rasgos estandarizados (`stats`). Validación: silueta (`cluster::silhouette`) para elegir k; estabilidad con varias semillas comparando particiones con `mclust::adjustedRandIndex`, y remuestreo de artistas.

**Segmentación (clases 08 y 09).** Derivada FIR sobre log1p suavizado para marcar meses de crecimiento anómalo (por ejemplo, mayor a la mediana más tres desvíos absolutos medianos del artista); tramos entre esos meses. Con `wavethresh`, coeficientes de detalle por nivel para ubicar cambios bruscos en el tiempo.

## 4. Detalles técnicos que el material deja implícitos

- `fft(..., inverse = TRUE)` en R no divide por N: hay que hacerlo a mano. Toda máscara debe anular también el espejo N-k, o la inversa deja de ser real.
- En R el índice 1 del espectro es la frecuencia cero; k ciclos en N muestras caen en el índice k+1.
- `fft` no admite NA: imputar antes de filtrar en frecuencia.
- `filter(..., circular = TRUE)` es correcto para las señales periódicas sintéticas de clase, no para trayectorias de artistas.
- En el caso 4 de interpolación (`06_interpolacion.ipynb`), `is.numeric(st1)` devuelve un solo TRUE y no filtra los NA; la máscara correcta es `!is.na(st1)`.
- Bajar la frecuencia de muestreo sin pasa-bajos previo introduce alias (clase 06, caso 2). La mensualización del dataset (último día de cada mes) es exactamente eso; para stocks que varían lento el efecto es menor, pero vale nombrarlo en el video.
- Ventanas pares en `frollmean` centrado quedan levemente asimétricas.

## 5. Conceptos teóricos para intercalar en el video

| Momento del video | Concepto | Clase |
|---|---|---|
| Datos | Serie temporal como señal a intervalos regulares; mensualización como muestreo y teorema del muestreo (nada más rápido que 2 meses es visible) | 01 |
| Datos | Faltantes como muestras perdidas; interpolación lineal vs spline | 06 |
| Preprocesamiento | No estacionaridad de los stocks: ADF y KPSS; `diff` como pasa-altos | 10, 08 |
| Preprocesamiento | Media móvil como FIR pasa-bajos; trade-off ventana vs distorsión | 03, 08 |
| Preprocesamiento | Filtrado en DF: FFT, máscara, inversa; por qué quitar la tendencia antes | 02, 04 |
| Métodos | Correlación cruzada como similaridad con desfase; dualidad convolución-producto | 05 |
| Métodos | Tiempo-frecuencia: Fourier no dice cuándo; wavelets para cambios localizados | 09 |
| Discusión | Correlación no es causalidad; Granger y contrafácticos como extensión | 07 |

## 6. Orden de trabajo acordado

1. Inventario del material (este archivo).
2. Preprocesamiento en `music_industry_ST_filtros.ipynb`: imputación de huecos, transformación (log1p con centrado por artista o alternativa justificada) y el suavizado ya probado, todo con herramientas de la materia y extendido al panel completo. El notebook todavía apunta al panel de 1041 artistas; el último exportado es `artistas_ST_1062de1454_top5000_22_26_na_wide.csv`.
3. Notebook nuevo de clusterización y segmentación con métodos de la materia y validación con más de una semilla.

Criterio de alcance: el análisis tiene que caber en un video de 12 a 15 minutos con teoría intercalada. Celdas de texto breves e informativas, escritas en primera persona del plural para un lector externo al proyecto.
