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

_Pendiente_

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