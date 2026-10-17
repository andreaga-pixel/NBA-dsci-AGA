# Gestión de carga de jugadores en la NBA

**Project brief · Entrega 1 · Andrea Gutiérrez**

## 1. El dataset

### 1.1 Ficha del dataset

| Campo | Respuesta |
|---|---|
| **Nombre** | NBA Dataset: Box Scores and Stats (1947 - Today) |
| **Autor y URL** | Eoin A. Moore, en Kaggle: https://www.kaggle.com/datasets/eoinamoore/historical-nba-data-and-player-box-scores. Datos originales de NBA.com |
| **Licencia** | CC0 1.0, dominio público: https://creativecommons.org/publicdomain/zero/1.0/ |
| **Fecha de descarga y versión** | Descargado el 14/10/2026. Versión 515, actualizada el 15/06/2026 (fin de la temporada 2025-26) |
| **Grano: ¿qué es una fila?** | En `PlayerStatistics.csv`, un jugador en un partido. Comprobado: 0 parejas jugador-partido repetidas |
| **Periodo cubierto** | Del 26/11/1946 al 13/06/2026. Uso desde la temporada 2015-16 (partidos desde el 01/10/2015): 396.530 filas |
| **Tamaño** | `PlayerStatistics.csv`: 1.669.922 filas × 40 columnas, 390 MB. Además: `Games.csv` (11 MB), `TeamStatistics.csv` (36 MB) y `Players.csv` (0,5 MB) |
| **Columnas clave** | **Fechas:** `gameDate`. **Dimensiones:** jugador (`personId`), equipo, rival, local/visitante (`home`), tipo de partido (`gameType`), titular (`startingPosition`). **Medidas:** `numMinutes`, `points`, `plusMinusPoints`, tiros, rebotes, asistencias. **Objetivo candidato:** rendimiento en el siguiente partido, o ausencia por lesión (derivada de `comment`) |
| **Problemas de calidad detectados** | `numMinutes` se lee como texto (2.097 filas en formato `mm:ss`). Fechas e IDs con tipos incorrectos. `comment` y `gameType` con el mismo valor escrito de varias formas (7.891 variantes en `comment`). Nulos reales de minutos: 3,7 % de mi ventana. Al 10,4 % de los jugadores recientes le falta la fecha de nacimiento (0,2 % de las filas) |
| **Otras fuentes que uniré** | Dentro del mismo dataset: `Players.csv` (edad y posición, unida por `personId`) y `TeamStatistics.csv` (balance de victorias del rival). No necesito fuentes externas |

### 1.2 Calidad y limitaciones de los datos

El análisis completo está en [`notebooks/01-exploracion-datasetNBA.ipynb`](notebooks/01-exploracion-datasetNBA.ipynb). Los puntos principales son los siguientes:

**Problemas de calidad** (se corrigen en la limpieza)

- Los **minutos están en dos formatos** `numMinutes`, la variable principal de carga, se lee como texto porque 2.097 filas usan `mm:ss` en lugar de un número decimal. Los tendré que convertir en minutos decimales, sin eliminar las filas. 
- **Nulos estructurales** Vs **nulos reales**. El 10% de las filas no tienen minutos, pero el 89% de esos casos el jugador no jugó, lo sé porque tiene un comentario de ausencia. Los nulos realesson el 3,7% de mi ventana. El 59% de ellos son de pretemporada (que voy a excluir) y el resto tiene todas las estadísticas a 0 y se tratará como "no jugó, motivo no registrado".
- **Texto sin normalizar** `comment`tiene 7.891 variantes y `gameType` escribe el mismo tipo de  partido de varias formas (por ejemplo,``Preseason` y `Pre Season`).

**Limitaciones**(no se puede corregir)

- **No hay datos físicos**. Sin GPS, frecuencia cardiaca ni distacia recorrida, la carga se aproxima solo con minutos y calendario. 
- **El descanso no se registra de forma fiable**. Solo 418 ausencias se etiquetan como descanso. La mayoría quedan ocultas en "Coach's Decision" (120.262 casos), que no distigue descanso de decisión técnica. Además, la eyiqueta "Rest" cae a partir de 2018, coincidiendo con la normativa de la NBA contra el descanso de estrellas: **como se registra el dato depende de las reglas de la liga**.
- **Sesgo de selección**. Los entreadores ya descansan a quien ven cansado, así que los datos muestran a los jugadores que *ya* se gestionaron. La relación entre carga y rendimiento será una asociación, no una causa. 

**Efecto en mi decisión:**la herramienta ofrecerá recomendaciones de apoyo, no ordenes. El responsable de rendimiento debe combinarlas con la información médica y física que el club sí tiene y el dataset no. 

## 2. El problema de negocio

### 2.1 Organización, departamento y stakeholder

_Pendiente_

### 2.2 La decisión que apoya

_Pendiente_

### 2.3 Hipótesis

_Pendiente_

### 2.4 KPIs

_Pendiente_

## 3. La solución

### 3.1 Tipo de solución

_Pendiente_

### 3.2 Arquitectura

_Pendiente_

## Fuentes

_Pendiente_