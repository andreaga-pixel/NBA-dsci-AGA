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

**Limitaciones** (no se puede corregir)

- **No hay datos físicos**. Sin GPS, frecuencia cardiaca ni distacia recorrida, la carga se aproxima solo con minutos y calendario. 
- **El descanso no se registra de forma fiable**. Solo 418 ausencias se etiquetan como descanso. La mayoría quedan ocultas en "Coach's Decision" (120.262 casos), que no distigue descanso de decisión técnica. Además, la eyiqueta "Rest" cae a partir de 2018, coincidiendo con la normativa de la NBA contra el descanso de estrellas: **como se registra el dato depende de las reglas de la liga**.
- **Sesgo de selección**. Los entreadores ya descansan a quien ven cansado, así que los datos muestran a los jugadores que *ya* se gestionaron. La relación entre carga y rendimiento será una asociación, no una causa. 

**Efecto en mi decisión:** la herramienta ofrecerá recomendaciones de apoyo, no ordenes. El responsable de rendimiento debe combinarlas con la información médica y física que el club sí tiene y el dataset no. 

## 2. El problema de negocio

### 2.1 Organización, departamento y stakeholder

**Organización:** *AGA Analytics* (inventado) es una consultora de analítica deportiva que ofrece a los clubes de NBA una herramienta de gestión de carga de jugadores, construida con datos públicos de la liga. El proyecto se presenta con un club piloto, los **Celtics**, pero el sistema es aplicable a cualquier equipo cambiando un filtro. 

**Departamento:** El área de rendimiento y ciencia del deporte del club: un equipo reducido como por ejemplo; preparador físico, fisioterapeuta, nutricionista y un analista de datos. Estos se van a encargar de que los jugadores lleguen en buen estado a los partidos y de reducir el riesgo de lesión durante una temporada de 82 partidos en unos cinco meses y medio. 

**Srakeholder:** El respondable de rendimiento. Consulta la herramienta la víspera y la mañana de cada partido. Puede proponer que un jugador descanse, juegue con un límite de minutos o reduzca la carga en el entrenamiento. La decision final es siempre del entrenador, así que la herramienta debe dar argumentos claros que el responsable pueda defeder ante él. 

### 2.2 La decisión que apoya

**Decisión.** Con esta herramienta, el responsable de rendimiento decidirá, antes de cada partido, **qué jugadores juegan con normalidad, con un límite de minutos o descansan**, equilibrando las opciones de ganar ese partido con el desgaste acumulado de cada jugador.

**Por qué importa al club.** El objetivo estratégico de los Celtics no es ganar cada partido de temporada regular, sino llegar a los playoffs con la plantilla sana. Ahí están los ingresos y el prestigio. Cada partido perdido por un descanso cuesta poco; una lesión de un titular en marzo puede costar la temporada.

**Restricciones.**

- **Normativa de la NBA.** La *Player Participation Policy* (2023) impide descansar a más de una estrella en el mismo partido y obliga a que las estrellas jueguen los partidos televisados a nivel nacional, con multas si no se cumple. Además, los jugadores necesitan 65 partidos para optar a premios individuales.

- **Datos disponibles.** Solo datos públicos: minutos, calendario y estadísticas. No hay datos médicos ni físicos (GPS, frecuencia cardiaca), que el club sí tiene pero no forman parte de este proyecto.

- **Tiempo.** La recomendación tiene que estar lista la víspera del partido, a tiempo para planificar el entrenamiento y hablar con el entrenador.

- **Quién decide.** La herramienta recomienda; la decisión final es del entrenador.

**Alternativas sin esta herramienta.**

- **El criterio del cuerpo técnico:** rápido y con información que los datos no tienen, pero subjetivo y difícil de justificar.

- **Reglas fijas** (por ejemplo, descansar a todos los veteranos en los back-to-backs): simples, pero ignoran el rival y el momento de la temporada.

- **Sensores y GPS:** más precisos, pero caros y privados. Mi herramienta los complementa, no los sustituye.

**A quién más afecta.** A los **jugadores**, cuyos minutos influyen en sus estadísticas, contratos y opciones de premios; a la **afición y las televisiones**, que pagan por ver a las estrellas; y a la **liga**, que sanciona los descansos que perjudican al espectáculo.

### 2.3 Hipótesis

Despues de explorar el dataset la hipótesis del principio que era sobre back-to-backs, carga semanal y rival débil. Ahora al ver que `comment` contiene lesiones, me permite dar un giro a mi hipotesis hacia **¿la carga acumulada aumenta el riesgo de lesión?**. Que es un dato super importante para un responsable de rendimiento. 

Voy a ordenar las diferentes hipótesis por valor que tienen para la decisión.

**H1 - La carga acumulada aumenta el riesgo de lesión** *(prioridad alta)*

| | |
|---|---|
| **Enunciado** | Los jugadores con una carga alta en los 14 días previos tienen más probabilidad de perderse partidos por lesión en las dos semanas siguientes. |
| **Métrica** | % de jugador-partidos seguidos de una ausencia por lesión (`comment` con DND/DNP + *Injury*, *Sprain*, *Sore*…), según la carga de los 14 días previos (minutos acumulados). |
| **Dirección esperada** | El % de lesión es mayor en el tramo de carga más alto (por ejemplo, el 20 % superior de cada jugador) que en el resto. |
| **Cómo la pruebo** | Variable de ventana móvil de minutos (lección 05), tasa de lesión por tramos de carga y gráfico de barras (lección 04). |
| **Si se cumple** | La herramienta marca en rojo a los jugadores en el tramo alto y propone un límite de minutos antes de que llegue la lesión. |
| **Si no se cumple** | La carga en minutos no basta para anticipar lesiones; el club debe apoyarse en sus datos físicos y la herramienta se centra en el rendimiento. |

**H2 - El rendimiento cae en el segundo partido de un back-to-back, y mas en los veteranos** *(prioridad media)*

| | |
|---|---|
| **Enunciado** | En el segundo de dos partidos en días consecutivos, la eficiencia en el tiro baja, sobre todo en jugadores mayores de 30 años. |
| **Métrica** | *True Shooting %* = puntos / (2 × (tiros de campo intentados + 0,44 × tiros libres intentados)), comparando al **mismo jugador** con 0 días de descanso frente a 2 o más. |
| **Dirección esperada** | Menor TS% con 0 días de descanso; diferencia mayor en los mayores de 30. |
| **Cómo la pruebo** | Variable de días de descanso (lección 05), boxplot por descanso y grupo de edad, y diferencia de medias (lección 04). |
| **Si se cumple** | Límite de minutos o descanso para los veteranos en el segundo partido de cada back-to-back. |
| **Si no se cumple** | Los back-to-backs no justifican descansos por sí solos; se descansa según la carga acumulada (H1). |

**H3 - Descansar a un titular cuesta poco contra rivales débiles** *(prioridad media)*

| | |
|---|---|
| **Enunciado** | La ausencia de un titular habitual reduce poco las victorias contra rivales de balance negativo, pero mucho contra los fuertes. |
| **Métrica** | % de victorias según el nivel del rival (balance de victorias, de `TeamStatistics`) y si falta o no un titular habitual. |
| **Dirección esperada** | Diferencia pequeña contra rivales débiles y grande contra rivales fuertes. |
| **Cómo la pruebo** | Tabla cruzada de % de victorias por nivel del rival y ausencia de titular (lecciones 03–04). |
| **Si se cumple** | Programar los descansos contra rivales débiles, respetando la normativa de la liga. |
| **Si no se cumple** | El rival no importa: los descansos se reparten según la carga, no según el calendario. |

**¿Por qué en este orden?** **H1** es la más valiosa porque una lesión de un titular puede costar la temporada y anticiparla es el objetivo principal del departamento. **H3** y **H2** ayudan a decidir *cuando* y *como* descansar, pero solo importan si desacansar evita algo: por eso dependen directamente de **H1**.



### 2.4 KPIs

_Pendiente_

## 3. La solución

### 3.1 Tipo de solución

_Pendiente_

### 3.2 Arquitectura

_Pendiente_

## Fuentes

## Fuentes

**Datos**

- Moore, E. A. *NBA Dataset: Box Scores and Stats (1947 - Today)*, versión 515. Kaggle. [https://www.kaggle.com/datasets/eoinamoore/historical-nba-data-and-player-box-scores](https://www.kaggle.com/datasets/eoinamoore/historical-nba-data-and-player-box-scores). Datos originales de NBA.com.
- Licencia CC0 1.0 (dominio público): [https://creativecommons.org/publicdomain/zero/1.0/](https://creativecommons.org/publicdomain/zero/1.0/)

**Normativa de la NBA sobre descansos**

- ESPN. *NBA Board of Governors approve tougher rest rule* (2023): [enlace](https://africa.espn.com/nba/story/_/id/38392750/source-nba-board-governors-approve-tougher-rest-rule)

- NBC Sports. *NBA owners approve stricter rules for resting multiple star players* (2023): [enlace](https://www.nbcsports.com/nba/news/nba-owners-approve-stricter-rules-for-resting-multiple-star-players-what-will-that-mean)

- Eurohoops. *NBPA slams Bucks over Giannis shutdown for 2025-26 NBA season*: [enlace](https://www.eurohoops.net/en/nba-news/1947670/giannis-antetokounmpo-update-nbpa-milwaukee-bucks-nba/amp/). Muestra que la normativa sigue aplicándose en la temporada 2025-26.

**Análisis propio**

- Exploración del dataset: [`notebooks/01-exploracion-datasetNBA.ipynb`](notebooks/01-exploracion-datasetNBA.ipynb)