# Flow-R

Flow-R {cite}`horton_flowr_2013` (Evaluación de trayectorias de flujo de peligros gravitacionales a escala regional) fue desarrollado principalmente para la cartografía de susceptibilidad a flujos de detritos a escala regional. Sin embargo, también se ha utilizado para otros peligros gravitacionales como caídas de rocas, avalanchas de rocas, deslizamientos superficiales y avalanchas de nieve. Fue diseñado para proporcionar flexibilidad en términos de datos y algoritmos para que su uso pueda adaptarse a la disponibilidad de datos y al proceso de interés.

La primera versión, escrita en Matlab, fue publicada en 2009 y está disponible gratuitamente {cite}`horton_2022`. Una nueva versión, Flow-R v2, escrita en C++, fue publicada en 2020 y es comercial. Esta nueva versión abordó varias limitaciones de la primera. Proporciona un aumento sustancial en el rendimiento y un manejo eficiente de la memoria que facilita trabajar con extensiones espaciales sin restricciones. También es significativamente más amigable para el usuario y los datos (admite muchos formatos de datos).

### Componentes

Una identificación de las áreas fuente potenciales se proporciona en Flow-R v1, que consiste en una combinación de múltiples capas de datos con reglas de clasificación. Para la versión 2, las áreas fuente pueden prepararse en cualquier software GIS. Luego, Flow-R propaga la susceptibilidad a flujos de detritos desde cada área fuente. Aunque el valor inicial para propagar suele ser el valor normalizado 1, se pueden asignar valores de susceptibilidad inicial específicos a cada área fuente. Esto permite tener en cuenta diferentes valores iniciales de susceptibilidad a la falla o frecuencias en el mapa resultante {cite}`horton_flowr_2013`. Flow-R no se basa en un enfoque de caminata aleatoria sino que propaga la susceptibilidad desde cada área fuente a través de una única propagación, cuya extensión está destinada a cubrir todos los eventos posibles. El resultado principal es una susceptibilidad espacialmente distribuida que representa áreas donde es más probable que ocurran flujos de detritos.

El alcance calculado por Flow-R se basa en algoritmos de propagación que controlan la trayectoria y la dispersión de la susceptibilidad y en leyes de fricción simples que determinan la distancia de alcance. Los algoritmos de dirección de flujo y las funciones de persistencia controlan el enrutamiento y la dispersión. Se implementan varios algoritmos de dirección de flujo, como D8 {cite}`ocallaghan_2003`, D-infinity {cite}`tarboton_1997`, Rho8 {cite}`fairfield_drainage_1991`, dirección de flujo múltiple {cite}`quinn_prediction_1991`, Freeman {cite}`freeman_calculating_1991`, Gamma {cite}`gamma_dflow_2000` y Holmgren {cite}`holmgren_multiple_1994`, así como su versión modificada {cite}`horton_flowr_2013`. La versión modificada del algoritmo de Holmgren es la más utilizada, ya que permite reproducir la mayoría de los algoritmos mediante su parámetro que controla la dispersión. Hay disponibles diferentes esquemas de ponderación para la función de persistencia.

Dos algoritmos están disponibles para tener en cuenta la fricción: el modelo de fricción de dos parámetros de Perla et al. {cite}`perla_twoparameter_1980` y un modelo simplificado limitado por fricción {cite}`horton_flowr_2013` (SFLM), que consiste en un enfoque de línea de energía combinado con una limitación de la velocidad máxima. Esta limitación tiene como objetivo mantener la energía en valores razonables en cuencas empinadas. Con velocidades máximas observadas de flujos de detritos en Suiza de 13–14 m/s {cite}`rickenmann_zimmermann_1993`, a menudo se utiliza un límite de 15 m/s.

### Requisitos de Datos

Flow-R es flexible en términos de requisitos de datos. El requisito mínimo es un DEM para la versión 1. Para la versión 2, también se requiere un archivo con las áreas fuente. Como Flow-R v1 proporciona una identificación de las áreas fuente potenciales, se puede proporcionar cualquier conjunto de datos considerado relevante, como mapas de uso del suelo o geológicos o cualquier producto derivado del DEM. Para la propagación, solo se requiere el DEM. Opcionalmente, se puede proporcionar un archivo raster que proporcione ángulos de recorrido espacialmente variables para tener en cuenta diferentes clases de cobertura del suelo o medidas de mitigación, por ejemplo.

### Salidas

La mayoría de los resultados de Flow-R se exportan como archivos raster en formato geotiff (Flow-R v2) o ascii (Flow-R v1). La salida principal es el valor de susceptibilidad para cada celda del área de estudio, expresado como el valor máximo y/o la suma de los valores de susceptibilidad de todas las propagaciones originadas desde diferentes áreas fuente. Para mayor comodidad, también se puede exportar la extensión general de todas las propagaciones, así como el número de propagaciones que pasan por una celda dada (solo v2). Además, se puede exportar un shapefile que contiene la extensión de cada propagación (solo v2). También se pueden exportar raster que contienen valores de energía o velocidad (valores máximos por celda) para fines informativos. Sin embargo, deben considerarse con precaución, ya que se basan en cálculos utilizando un enfoque de masa unitaria.

A menudo se aplica un enfoque multiparamétrico que consiste en calcular propagaciones con diferentes conjuntos de parámetros que representan escenarios más o menos conservadores, para proporcionar clases de susceptibilidad. Los resultados de estos escenarios se combinan mediante reclasificación para asignar una clase de susceptibilidad más alta a los escenarios menos conservadores y una clase más baja a los escenarios más conservadores.

### Calibración y Evaluación

La calibración de Flow-R se basa principalmente en un enfoque de prueba y error, utilizando un inventario de eventos observados como referencia. La calibración puede realizarse en un área más pequeña bien documentada que sea representativa de la región. Luego, los parámetros pueden aplicarse a toda la región, si es posible con eventos observados adicionales que pueden usarse para validación. El objetivo de la calibración es cubrir la extensión de los eventos observados mientras se minimizan las áreas donde es poco probable que ocurran flujos de detritos.

Alternativamente, también se ha utilizado un enfoque basado en una matriz de confusión propuesto por Scheidl y Rickenmann {cite}`scheidl_rickenmann_2010` para calibrar Flow-R (Pastorello et al., 2017). Consiste en calcular y maximizar indicadores de precisión utilizando la extensión de eventos pasados.

Los parámetros que controlan la propagación y los que gobiernan la distancia de alcance pueden calibrarse de forma independiente, ya que no interfieren en gran medida entre sí. Para flujos de detritos, el modelo de dos parámetros de Perla (PCM) y el SFLM proporcionan resultados similares. Sin embargo, el SFLM es más fácil de calibrar y, por lo tanto, se utiliza con mayor frecuencia. Cuando se utiliza el modelo PCM, se pueden usar parámetros clásicos para flujos de detritos.


| Elemento                      | Flow-R v1                            | Flow-R v2                              |
|-------------------------------|---------------------------------------|----------------------------------------|
| DEM                           | Obligatorio                           | Obligatorio                            |
| Áreas fuente                  | Generadas por el modelo               | Preparadas en SIG externo              |
| Capas adicionales             | Opcionales: uso del suelo, geología   | Opcionales: uso del suelo, geología    |
| Ángulo de recorrido variable  | No                                    | Opcional (raster adicional)            |

## Caso de estudio: susceptibilidad y exposición vial en South Titirangi (Nueva Zelanda)

{cite}`sachinthaka_flowr_2026` aplicaron Flow-R para evaluar la susceptibilidad a flujos de detritos y la exposición de la red vial en South Titirangi, un sector de 4.73 km² en las laderas orientales de los Waitākere Ranges (West Auckland, Nueva Zelanda). La zona fue severamente afectada a inicios de 2023 por la tormenta del Auckland Anniversary Weekend (27 de enero) y el ciclón Gabrielle (12–14 de febrero), que desencadenaron numerosos deslizamientos superficiales y flujos de detritos, bloquearon corredores viales y dañaron viviendas. El terreno es fuertemente disectado, con pendientes que comúnmente superan los 26°, morfología convergente y longitudes de flujo cortas que favorecen la concentración rápida de la escorrentía.

El caso es útil como ejemplo didáctico porque muestra un procedimiento completo y reproducible de calibración de Flow-R basado en un inventario de eventos y en métricas de clasificación, y porque explicita las limitaciones de un modelo que depende exclusivamente de la topografía.

### Datos y resolución del DEM

Se partió de un DEM LiDAR de 1 m (levantamiento de Auckland de 2024) remuestreado a 5 m mediante promedio por bloques. Los autores probaron tres resoluciones:

- **1 m**: identificaba muchas más áreas fuente, pues detectaba todas las pequeñas convergencias topográficas, pero excedía la memoria disponible y generaba fuentes de dudosa relevancia para flujos de detritos peligrosos.
- **10 m**: reducía el costo computacional, pero suavizaba en exceso el relieve, produciendo menos áreas fuente y alcances menores que los observados.
- **5 m**: ofreció el mejor compromiso, capturando las zonas convergentes geomorfológicamente significativas y un patrón de fuentes comparable con el inventario.

A partir del DEM de 5 m se derivaron la pendiente, la curvatura en planta y la acumulación de flujo. El inventario de validación se construyó por digitalización manual de los deslizamientos visibles en ortofotos post-evento de 5 cm de resolución (febrero–abril de 2023).

Un detalle práctico importante: las reglas de acumulación de flujo de Flow-R v1 están formuladas para un DEM de 10 m. Por ello, la acumulación de flujo calculada con celdas de 5 m (25 m² por celda) se multiplicó por 0.25 para expresarla en celdas equivalentes de 10 m (100 m² por celda), y se combinó con la regla `DF_extreme_events_for_10m_DEM`, basada en la relación empírica de {cite}`rickenmann_zimmermann_1993` para eventos extremos. Esta regla es más permisiva que la de eventos raros y permite que áreas contribuyentes pequeñas califiquen como zonas de inicio, lo cual es coherente con la ocurrencia documentada de flujos en cuencas de primer orden durante los eventos de 2023.

### Parámetros y rangos de calibración

La {numref}`tab-flowr-calibracion` resume los parámetros evaluados, sus rangos y la justificación de cada uno. Los parámetros de identificación de fuentes (pendiente, curvatura, acumulación) y los de propagación (algoritmo de dirección, inercia, fricción y límite de energía) se variaron de forma sistemática.

```{list-table} Rangos de calibración de los parámetros de Flow-R en South Titirangi. Adaptado de Sachinthaka et al. (2026).
:header-rows: 1
:name: tab-flowr-calibracion
:widths: 18 22 60

* - Parámetro
  - Valores evaluados
  - Justificación
* - DEM (cota mínima)
  - 0 m s.n.m.
  - Incluye toda el área de estudio; no se aplican exclusiones por elevación.
* - Pendiente
  - 15°, 20°, 26°, 30°
  - El rango reportado en la literatura para el inicio de flujos de detritos es 15°–30°. Los valores bajos favorecen la sensibilidad y los altos la especificidad; 26° es un valor intermedio {cite}`horton_flowr_2013,rickenmann_zimmermann_1993,blais-stevens_debris_2016`.
* - Curvatura en planta
  - −1/100 m⁻¹; −2/100 m⁻¹
  - La concavidad ayuda a detectar cárcavas, pero no existe un umbral universal: se reporta −2/100 m⁻¹ (DEM de 10 m, Suiza) y rangos entre 1.5/100 y −0.5/100 m⁻¹ en otros estudios. Para el DEM de 5 m se probaron ambos valores y se retuvo el óptimo frente al inventario {cite}`horton_flowr_2013,blais-stevens_debris_2016`.
* - Acumulación de flujo
  - `DF_extreme_events_for_10m_DEM`
  - Relación empírica de eventos extremos de {cite}`rickenmann_zimmermann_1993`, adecuada para fuentes en cuencas pequeñas y empinadas tras lluvias extremas; más permisiva que la regla de eventos raros.
* - Tratamiento de las fuentes
  - Binario (0/1), fijo
  - Todas las celdas que cumplen los criterios morfométricos se tratan por igual, sin ponderación adicional.
* - Algoritmo de dirección
  - Holmgren modificado; $dh = 1.5$ m; $x = 3$ y $4$
  - El exponente $x$ controla la divergencia: valores bajos dispersan lateralmente y valores altos concentran el flujo en los cauces. En flujos de detritos se suelen probar $x = 4$–$6$; aquí se evaluaron $x = 3$ (divergente) y $x = 4$ (canalizado). El parámetro $dh$ eleva la celda central para atenuar la rugosidad de DEMs de alta resolución {cite}`holmgren_multiple_1994,horton_flowr_2013`.
* - Algoritmo inercial
  - Gamma (2000)
  - Opción estándar de Flow-R para flujos de detritos; conserva el momento direccional {cite}`gamma_dflow_2000,horton_flowr_2013`.
* - Función de pérdida por fricción (ángulo de recorrido)
  - 9°, 11°, 13°
  - Rango típico para flujos de detritos de 8°–15°; ángulos menores implican mayor movilidad y mayor alcance. Representa el arcotangente del coeficiente de fricción efectivo {cite}`corominas_angle_1996`.
* - Limitación de energía (velocidad máxima)
  - 5 m/s, 10 m/s
  - Evita energías irreales en trayectorias empinadas. En Suiza se han observado máximos de 13–14 m/s y suele usarse 15 m/s; aquí se aplicaron límites menores como escenarios conservadores de sensibilidad {cite}`horton_flowr_2013,rickenmann_empirical_1999`.
```

La combinación de estos valores generó **96 escenarios**. Para cada uno, Flow-R produjo un índice continuo de susceptibilidad (raster *ProbSum*) que se convirtió en un mapa binario y se comparó, celda a celda, con el inventario de celdas afectadas. Con ello se calcularon los verdaderos positivos (TP), falsos positivos (FP), falsos negativos (FN) y verdaderos negativos (TN), la tasa de verdaderos positivos ($TPR = TP/(TP+FN)$), la tasa de verdaderos negativos ($TNR = TN/(TN+FP)$), el área bajo la curva ROC (AUC) y el puntaje F1:

$$
F_1 = \frac{2\,P\,R}{P + R}, \qquad P = \frac{TP}{TP + FP}, \qquad R = TPR
$$

### Escenarios y métricas de desempeño

La {numref}`tab-flowr-escenarios` presenta los diez mejores escenarios ordenados por AUC. En todos ellos la cota mínima del DEM fue 0 m, la regla de acumulación fue `DF_extreme_events_for_10m_DEM`, $dh = 1.5$ m y el algoritmo inercial Gamma (2000).

```{list-table} Diez mejores escenarios de Flow-R ordenados por el área bajo la curva ROC. Pend.: pendiente mínima de inicio; Curv.: curvatura en planta; x: exponente de Holmgren; α: ángulo de recorrido; v_max: velocidad máxima. Adaptado de Sachinthaka et al. (2026).
:header-rows: 1
:name: tab-flowr-escenarios

* - Rango
  - ID
  - Pend.
  - Curv. (m⁻¹)
  - $x$
  - $\alpha$
  - $v_{max}$ (m/s)
  - TP
  - FP
  - FN
  - TN
  - TPR
  - TNR
  - AUC
  - F1
* - 1
  - **N4**
  - 15°
  - −1/100
  - 3
  - 11°
  - 10
  - 73
  - 1862
  - 26
  - 431 097
  - 0.74
  - 1.00
  - **0.65**
  - 0.072
* - 2
  - N16
  - 15°
  - −2/100
  - 3
  - 11°
  - 10
  - 64
  - 1794
  - 25
  - 431 175
  - 0.72
  - 1.00
  - 0.65
  - 0.066
* - 3
  - N1
  - 15°
  - −1/100
  - 3
  - 9°
  - 5
  - 68
  - 1252
  - 27
  - 431 711
  - 0.72
  - 1.00
  - 0.64
  - 0.096
* - 4
  - N13
  - 15°
  - −2/100
  - 3
  - 9°
  - 5
  - 59
  - 1209
  - 26
  - 431 764
  - 0.69
  - 1.00
  - 0.64
  - 0.087
* - 5
  - N7
  - 15°
  - −1/100
  - 4
  - 9°
  - 5
  - 52
  - 1131
  - 27
  - 431 848
  - 0.66
  - 1.00
  - 0.63
  - 0.082
* - 6
  - N14
  - 15°
  - −2/100
  - 3
  - 9°
  - 10
  - 66
  - 2251
  - 27
  - 430 714
  - 0.71
  - 0.99
  - 0.63
  - 0.055
* - 7
  - N6
  - 15°
  - −1/100
  - 3
  - 13°
  - 10
  - 73
  - 1294
  - 26
  - 431 665
  - 0.74
  - 1.00
  - 0.61
  - 0.100
* - 8
  - N73
  - 30°
  - −1/100
  - 3
  - 9°
  - 5
  - 22
  - 226
  - 44
  - 432 766
  - 0.33
  - 1.00
  - 0.61
  - 0.140
* - 9
  - N76
  - 30°
  - −1/100
  - 3
  - 11°
  - 10
  - 30
  - 353
  - 50
  - 432 625
  - 0.38
  - 1.00
  - 0.61
  - 0.130
* - 10
  - N85
  - 30°
  - −2/100
  - 3
  - 9°
  - 5
  - 22
  - 205
  - 44
  - 432 787
  - 0.33
  - 1.00
  - 0.61
  - 0.150
```

### Interpretación de la calibración

**Pendiente de inicio.** Los siete mejores escenarios usan el umbral más bajo (15°). Con 15° el modelo alcanzó tasas de verdaderos positivos de hasta 74 %, mientras que con 30° la TPR cayó a 33–38 %, pues se omitía la mayoría de las zonas de inicio conocidas. Esto es coherente con la observación empírica de que los flujos de detritos pueden iniciarse en pendientes moderadas cuando existe suficiente área contribuyente {cite}`horton_flowr_2013`. Los escenarios de 30° obtienen F1 más altos (≈0.14) por su mayor precisión, pero a costa de dejar de detectar dos tercios de los eventos.

**Curvatura en planta.** Su influencia fue menor. El umbral menos cóncavo (−0.01 m⁻¹) produjo un AUC ligeramente superior, lo que sugiere que incluir concavidades suaves (hondonadas amplias) mejora la detección; un umbral más restrictivo (−0.02 m⁻¹, el óptimo reportado para los Alpes suizos con DEM de 10 m) puede excluir canales incipientes que sí generaron flujos. El valor óptimo depende de la región y de la resolución del DEM.

**Exponente de Holmgren.** El exponente $x = 3$ superó de forma pequeña pero consistente a $x = 4$, a pesar de que en la literatura se recomienda $x \geq 4$ para flujos de detritos. Una mayor dispersión lateral reprodujo mejor la depositación observada, que en los abanicos no siempre quedó confinada a un único cauce.

**Ángulo de recorrido y velocidad máxima.** Estos dos parámetros actúan de forma acoplada sobre la distancia de alcance. Un ángulo de 9° extendía el flujo sobre las pendientes suaves distales, aumentando los falsos positivos; 13° detenía el flujo prematuramente. El ángulo de 11°, dentro del rango típico de 10°–15° para flujos de largo alcance {cite}`vonfischer_flowr_2016`, dio el mejor ajuste. Para ángulos de 11°–13°, aumentar $v_{max}$ de 5 a 10 m/s mejoró notablemente la reproducción del alcance: con 11°, el límite de 10 m/s (N4) reprodujo todo el alcance observado, mientras que 5 m/s subestimaba el área inundada. La calibración conjunta de fricción y velocidad es, por tanto, necesaria.

### Desempeño del mejor escenario (N4)

El escenario N4 (15°, −1/100 m⁻¹, $x = 3$, 11°, 10 m/s) alcanzó AUC = 0.65, TPR = 0.74 y TNR = 99.6 %, con 1862 falsos positivos y una precisión de apenas ≈3.8 % (F1 = 0.072). Estos números ilustran un rasgo general de la cartografía de susceptibilidad regional: el **fuerte desbalance de clases**. Las celdas estables (≈431 000) superan en más de tres órdenes de magnitud a las afectadas (≈100), de modo que la TNR resulta casi perfecta para cualquier escenario y no discrimina entre ellos, mientras que la precisión y el F1 son bajos aun cuando el modelo detecta la mayoría de los eventos. Por ello conviene reportar varias métricas simultáneamente y no depender solo de la exactitud global.

La baja precisión se explica por tres factores: (1) el umbral permisivo de 15° marca terreno morfométricamente similar que no falló en 2023; (2) la ausencia de lluvia y de propiedades del material impide distinguir terreno condicionalmente susceptible; y (3) un inventario de un solo evento puede no capturar todos los sitios susceptibles. Algunos "falsos positivos" en cuencas de segundo orden y márgenes de abanicos podrían corresponder a depósitos antiguos no cartografiados. Valores de AUC entre 0.6 y 0.7 son comparables a los reportados en otros estudios con métodos empíricos y DEMs de resolución moderada {cite}`blais-stevens_debris_2016`. Para aplicaciones de tamizaje, la sobrepredicción conservadora es preferible a la subpredicción, pues reduce el riesgo de omitir zonas realmente peligrosas.

### Exposición de la red vial

Finalmente, las zonas susceptibles del escenario N4 se superpusieron con la red vial. Varios tramos intersectan trayectorias probables de flujo; en particular, una vía arterial principal atraviesa dos abanicos aluviales identificados como susceptibles. Este análisis constituye una evaluación de **exposición**, no de riesgo: para estimar el riesgo se requeriría además cuantificar la vulnerabilidad (fragilidad estructural, volúmenes de tráfico) y las consecuencias. Aun así, identificar los tramos expuestos es valioso para la preparación ante desastres (rutas alternas, redes de contención, alcantarillas de mayor capacidad) y para priorizar estudios detallados.

```{admonition} Lecciones para la aplicación de Flow-R
:class: tip

- La resolución del DEM condiciona fuertemente la identificación de fuentes; 5 m fue un buen compromiso entre detalle y estabilidad computacional. Si se usa un DEM distinto de 10 m, las reglas de acumulación de flujo de Flow-R v1 deben reescalarse.
- La pendiente mínima de inicio es el parámetro más sensible; umbrales altos mejoran la precisión pero omiten la mayoría de los eventos.
- El ángulo de recorrido y la velocidad máxima deben calibrarse conjuntamente.
- En problemas con fuerte desbalance de clases, la TNR y la exactitud global son poco informativas; conviene analizar AUC, TPR, precisión y F1 en conjunto.
- Los resultados de Flow-R son indicadores de **susceptibilidad** basados en el terreno, no predicciones de amenaza: no incorporan umbrales de lluvia, humedad antecedente ni propiedades geotécnicas.
```
