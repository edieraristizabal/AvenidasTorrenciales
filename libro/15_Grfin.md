# Grfin Tools

### Componentes del modelo

Grfin Tools es un paquete de software desarrollado por el U.S. Geological Survey (USGS) diseñado para estimar la extensión potencial de los deslizamientos (*runout*) y la inundación por flujos (como flujos de detritos, lahares y avalanchas de rocas) a escala de paisaje. El nombre "Grfin" es un acrónimo de **Gr**owth + **f**low + **in**undation (Crecimiento + flujo + inundación).

A diferencia de los modelos dinámicos basados en la física, Grfin Tools utiliza una serie de modelos empíricos, computacionalmente eficientes y con parámetros sencillos. Esto permite realizar evaluaciones rápidas sobre grandes áreas, siendo una herramienta ideal para análisis de susceptibilidad a escala regional o como un primer paso para identificar zonas que requieran estudios más detallados.

El software está estructurado en un conjunto de herramientas que pueden usarse de forma individual o combinada para simular una cadena de procesos completa, desde la identificación de las áreas fuente hasta la inundación final. Las herramientas principales son:

* **SOURCE:** Identifica las áreas de origen potenciales de los deslizamientos.
* **HL:** Calcula el alcance (runout) de los deslizamientos en laderas abiertas.
* **DRAINAGE:** Delinea la red de drenaje a partir del MDE.
* **GROWTH:** Modela el crecimiento en volumen del flujo a medida que avanza por la red de drenaje.
* **INUNDATION:** Delimita el área de inundación final en los canales y abanicos aluviales.

Grfin Tools funciona como un software independiente en Windows y depende del paquete de software TauDEM para los análisis topográficos básicos.

### Métodos y fundamentos empíricos

El enfoque de Grfin Tools no se basa en resolver ecuaciones de momento y continuidad como `r.avaflow`, sino en aplicar relaciones empíricas bien documentadas y validadas en la literatura científica.

#### Identificación de Áreas Fuente (Herramienta SOURCE)

El primer paso en muchos análisis es definir de dónde partirá el material. La herramienta *SOURCE* permite hacerlo de tres maneras:
* **Atributos topográficos:** Utilizando umbrales de pendiente y/o curvatura del terreno extraídos directamente del MDE. Es el método más rápido y directo.
* **Mapas de susceptibilidad externos:** Se puede utilizar un mapa de susceptibilidad a deslizamientos generado por otro modelo (por ejemplo, un análisis de estabilidad de taludes como TRIGRS) como capa de entrada.
* **Deslizamientos cartografiados:** Para el retro-análisis de eventos pasados, se pueden usar las cicatrices de deslizamientos ya cartografiadas.

#### Alcance en Laderas Abiertas (Herramienta HL)

Para modelar el recorrido del material en laderas no canalizadas, la herramienta *HL* utiliza una relación empírica muy simple y robusta: el ángulo de alcance (*angle of reach*), también conocido como *Fahrböschung*.
Este ángulo se define como el ángulo de la línea que une el punto más alto de la cicatriz del deslizamiento con el punto más lejano del depósito ($\arctan(H/L)$, donde H es la caída vertical y L es la distancia horizontal).

El modelo asume que el material se detendrá cuando el ángulo de la línea que lo une con su punto de origen sea igual o menor al ángulo de alcance mínimo (`min_reach_angle`) especificado por el usuario. La herramienta utiliza el algoritmo D-Infinity de TauDEM para simular la trayectoria del flujo con una dispersión moderada. Este método es ideal para evaluar el alcance de deslizamientos que no necesariamente se canalizan.

#### Inundación por flujos canalizados (Herramienta *INUNDATION*)

Para los flujos que se mueven a través de la red de drenaje, la herramienta *INUNDATION* utiliza relaciones de escala entre volumen y área. Este método, popularizado por modelos como LAHARZ, se basa en la observación empírica de que el área inundada por un flujo está directamente relacionada con su volumen.

Se utilizan dos ecuaciones de ley de potencia que relacionan el volumen del flujo ($V$) con el área de la sección transversal del flujo ($A$) y con el área planimétrica total inundada ($B$):

$$A = a_1 V^{2/3}$$

$$B = a_2 V^{2/3}$$

Los coeficientes $a_1$ y $a_2$ varían según el tipo de flujo. Grfin Tools incluye valores predefinidos para flujos de detritos, lahares y avalanchas de rocas, basados en compilaciones de datos de eventos a nivel mundial. También permite al usuario definir coeficientes personalizados. Por jemplo, los lahares son muy móviles y tienen un coeficiente planimétrico ($a_2$) mucho mayor que los flujos de detritos.

El modelo traza secciones transversales perpendiculares a la dirección del flujo en cada celda del canal. La inundación se expande lateralmente hasta que el área de la sección transversal alcanza el valor objetivo $A$. El límite aguas abajo se alcanza cuando el área planimétrica acumulada iguala el valor objetivo $B$.

#### Crecimiento del Flujo (Herramienta GROWTH)

Una de las características más potentes de Grfin Tools es su capacidad para simular el crecimiento en volumen del flujo a medida que avanza. Esto es crucial, ya que muchos flujos de detritos crecen significativamente por la incorporación de material del lecho y de las laderas.

* **Zonas de Crecimiento:** Primero, el usuario define las "zonas de crecimiento" a lo largo de la red de drenaje, típicamente los tramos con mayor pendiente, usando umbrales de pendiente, curvatura, orden del cauce, etc.
* **Factores de Crecimiento:** El volumen se calcula mediante dos métodos principales, que pueden usarse solos o combinados:
    * **Crecimiento por Área (`area_growth`):** El volumen añadido es proporcional al área de las zonas fuente que drenan a ese punto. Es ideal para modelar la contribución de la erosión superficial o de múltiples deslizamientos pequeños. El parámetro clave es el `area_growth_factor` (en m³/m²).
    * **Crecimiento por Longitud (`length_growth`):** El volumen añadido es proporcional a la longitud del canal dentro de la zona de crecimiento. Es más representativo de la erosión del lecho y de las márgenes del canal. El parámetro clave es el `length_growth_factor` (en m³/m).

### Parámetros

A diferencia de los modelos basados en la física, los parámetros de Grfin Tools son empíricos y se relacionan con la geometría y la escala del fenómeno.

| Componente del modelo | Parámetro | Símbolo / Nombre | Unidad |
| :--- | :--- | :--- | :--- |
| **Fuente** | Pendiente mínima/máxima | `min/max_source_value` | Grados |
| | Curvatura mínima | `min_curvature_source` | 1/m |
| | Área mínima de la fuente | `min_source_area` | m² |
| **Runout (HL)** | Ángulo de alcance mínimo | `min_reach_angle` | Grados |
| **Inundación** | Tipo de flujo | `flowtype` | Texto |
| | Coeficiente de área transversal | $a_1$ | – |
| | Coeficiente de área planimétrica | $a_2$ | – |
| **Crecimiento** | Pendiente mínima del cauce | `min_stream_slope` | Grados |
| | Factor de crecimiento por área | `area_growth_factor` | m³/m² |
| | Factor de crecimiento por longitud | `length_growth_factor` | m³/m |
| | Volumen máximo | `max_volume` | m³ |

### Datos

La simplicidad del modelo se refleja en sus requisitos de datos, que son considerablemente menores que los de un modelo dinámico complejo:

* **Requisito Central:** Un DEM en formato GeoTIFF y en un sistema de coordenadas proyectadas (ej. UTM) es el único dato absolutamente indispensable.
* **Datos Opcionales:**
    * **Capa de áreas fuente:** Si no se usan los atributos topográficos, se puede proporcionar un ráster con las áreas de inicio (ej. un mapa de susceptibilidad).
    * **Puntos de inicio y volúmenes:** Para la herramienta *INUNDATION* sin crecimiento, se necesita un archivo de texto (`.csv`) con las coordenadas y volúmenes de los flujos.
    * **Puntos de aporte de volumen:** Para la herramienta *GROWTH*, se pueden añadir fuentes de volumen puntuales (ej. la ruptura de una presa de detritos) mediante un archivo `.csv`.

### Salidas

Grfin Tools genera una serie de archivos ráster en formato GeoTIFF y archivos de texto que pueden ser fácilmente visualizados y analizados en cualquier software SIG. Los productos principales son:

* **Mapa de áreas fuente (`_sourcearea.tif`):** Un ráster binario que muestra las áreas de inicio definidas.
* **Mapa de alcance de runout (`_hlrunoutdistance.tif`):** Muestra la distancia de recorrido para los deslizamientos en laderas abiertas.
* **Mapa de inundación (`_inundation.tif`):** El producto final más común, que muestra la extensión espacial de la inundación por el flujo.
* **Mapa de zonas de crecimiento (`_grzone.tif`):** Muestra los tramos de la red de drenaje donde el modelo aplica el crecimiento de volumen.
* **Archivos de volúmenes (`_volumes.shp` y `.csv`):** Archivos vectoriales y de texto que detallan los volúmenes calculados a lo largo de las zonas de crecimiento.

### Calibración y Evaluación

La calibración en Grfin Tools no busca ajustar parámetros físicos, sino encontrar los parámetros empíricos que mejor reproducen eventos conocidos. El proceso es inherentemente un retro-análisis (*back-analysis*).

* **Calibración del Runout (HL):** Se ajusta el `min_reach_angle` hasta que la extensión simulada coincida con la observada en deslizamientos pasados.
* **Calibración de la Inundación y Crecimiento:** Se ajustan los `growth_factors` (de área o longitud) y el `max_volume` hasta que el área de inundación simulada se ajuste a la cartografía de un evento conocido.

Dado que el modelo es muy rápido, es factible ejecutar múltiples escenarios ("*what if*") para evaluar la sensibilidad a diferentes parámetros. Esto permite generar mapas de susceptibilidad que no solo muestran una única predicción, sino un rango de posibles resultados, lo cual es muy útil para la gestión del riesgo en condiciones de incertidumbre. La evaluación de los resultados es principalmente visual, comparando los polígonos simulados con los observados.


## Modelo Vinculado de Movilidad Topográfica


El "Modelo Vinculado" (*Linked-Model Approach*) descrito por Brien et al. {cite}`obrien_linkedmodel_2003` no es un software independiente, sino un marco conceptual y metodológico que se implementa utilizando el paquete de herramientas Grfin Tools. Su principal innovación es que permite que la topografía local controle de forma automática qué tipo de modelo de alcance (*runout*) se aplica, diferenciando explícitamente entre el recorrido de un deslizamiento en una ladera abierta y su transformación en un flujo de detritos canalizado.

El marco se basa en la definición de tres **"zonas de movilidad"** distintas dentro del paisaje:

1.  **Zonas Fuente (*Source Zones*):** Áreas donde se inician los deslizamientos. A diferencia de un análisis simple, este enfoque utiliza mapas de susceptibilidad detallados (por ejemplo, basados en el Factor de Seguridad de modelos como TRIGRS o Slabs3D) como dato de entrada principal.
2.  **Zonas de Runout No Canalizado (*Non-channelized Runout Zones*):** Corresponden al recorrido del deslizamiento en laderas abiertas, antes de alcanzar un canal definido. Este tramo se modela utilizando un método simple de alcance.
3.  **Zonas de Inundación Canalizada (*Channelized Runout / Debris-Flow Inundation Zones*):** Una vez que el material alcanza la red de drenaje, se modela como un flujo de detritos que puede crecer en volumen e inundar los canales y abanicos aluviales.

La "vinculación" del modelo consiste en que las Zonas Fuente actúan como el origen común para los dos tipos de modelos de runout, y la elección de cuál aplicar depende de la posición del flujo en el paisaje (ladera o canal).

### Métodos y fundamentos empíricos

El enfoque utiliza una combinación de las herramientas de `Grfin Tools` para modelar cada zona de movilidad.

#### Zonas Fuente
El insumo principal es un mapa de susceptibilidad (por ejemplo, un mapa de Factor de Seguridad), del cual se extraen las áreas fuente usando umbrales específicos (ej. FS < 0.87 para susceptibilidad "muy alta"). Esto permite una definición de las fuentes mucho más robusta y físicamente fundamentada.

#### Runout No canalizado (Modelo H/L)
Para las laderas abiertas, el modelo aplica la herramienta **HL** de `Grfin Tools`.
* **Concepto:** Se utiliza el **ángulo de alcance** ($\alpha = \arctan(H/L)$) como índice de movilidad. Se define un ángulo de alcance mínimo (`min_reach_angle`) y el material se propaga pendiente abajo hasta que se alcanza dicho ángulo.
* **Propósito:** Este método es ideal para modelar el recorrido inicial de los deslizamientos y para definir la zona de transición entre la ladera y el canal, así como para simular el runout en topografías donde no existe una red de drenaje bien definida (ej. laderas abiertas o escarpes). En el estudio de Puerto Rico, se seleccionó un valor de $\alpha = 20^{\circ}$ para evitar brechas entre el runout no canalizado y la inundación canalizada.

#### Inundación por flujos de detritos (Modelo de crecimiento e inundación)
Una vez que el flujo alcanza un canal, el modelo cambia para simular un flujo de detritos que crece en volumen. Esto se logra con las herramientas *GROWTH* e *INUNDATION* de `Grfin Tools`.
* **Definición de Zonas de Crecimiento:** El crecimiento del flujo no ocurre en toda la red de drenaje. Se delimitan "zonas de crecimiento" utilizando una combinación de criterios topográficos para identificar los tramos con mayor potencial de erosión y transporte. En el caso de Puerto Rico, se usó una combinación de:
    * **Pendiente del cauce** (`min_stream_slope`).
    * **Orden del cauce** (`max_stream_order`).
    * **Curvatura planimétrica** (`min_curvature_growth`).
    * **Ratio de área fuente** (`min_sourcearea_ratio`), que es la proporción del área de cuenca susceptible a deslizamientos.
* **Cálculo del Volumen "Autorregulado":** El volumen del flujo de detritos ($V$) en cualquier punto de la zona de crecimiento se calcula en función del área de las zonas fuente aguas arriba ($A_{src}$) y un factor de crecimiento empírico ($c_1$):
    $$V = c_1 \cdot A_{src}$$
    Este volumen está limitado por un volumen máximo ($V_{max}$). Se le llama "autorregulado" porque depende directamente de la susceptibilidad a deslizamientos de la cuenca de aporte: cuencas con más áreas fuente generarán flujos más voluminosos.
* **Mapeo de la Inundación:** Con el volumen ya calculado para cada punto, se usan las relaciones de escala de `Grfin Tools` para mapear el área de inundación transversal ($A = 0.1 V^{2/3}$) y planimétrica ($B = 20 V^{2/3}$) para flujos de detritos.

### Parámetros comunes

Los parámetros clave para este enfoque son una combinación específica de los disponibles en `Grfin Tools`, ajustados para reflejar la física del proceso en cada zona.

| **Componente del modelo** | **Parámetro** | **Símbolo / Nombre** | **Unidad** |
| :--- | :--- | :--- | :--- |
| **Fuente** | Umbral de Factor de Seguridad | `max_source_value` | – |
| **Runout No Canalizado** | Ángulo de alcance mínimo | `min_reach_angle` | Grados |
| **Zonas de Crecimiento**| Pendiente mínima del cauce | `min_stream_slope` | Grados |
| | Orden máximo del cauce | `max_stream_order` | – |
| | Curvatura mínima | `min_curvature_growth`| 1/m |
| | Ratio de área fuente mínimo | `min_sourcearea_ratio`| – |
| **Volumen de Crecimiento**| Factor de crecimiento por área | $c_1$ | m³/m² |
| | Volumen máximo | $V_{max}$ | m³ |

### Necesidades de datos

Este modelo es más exigente en cuanto a datos de entrada que un uso simple de `Grfin Tools`:

* **MDE de alta resolución:** Es fundamental para que los parámetros topográficos (pendiente, curvatura) sean representativos.
* **Mapa de susceptibilidad a deslizamientos:** Este es un requisito clave. Se necesita un ráster que cuantifique la propensión al inicio de deslizamientos (ej. un mapa de Factor de Seguridad) para definir las áreas fuente.
* **Inventario de deslizamientos (para calibración):** Para calibrar y validar el modelo, es indispensable contar con una cartografía detallada de eventos pasados, que incluya las áreas fuente y la extensión del runout.

### Salidas

El producto final de este enfoque es un mapa de susceptibilidad combinado que integra las tres zonas de movilidad. Típicamente, se visualiza con las diferentes zonas superpuestas para mostrar la cadena de procesos completa: las áreas fuente (ej. en rojo), sobre ellas las zonas de runout no canalizado (ej. en amarillo), y finalmente las zonas de inundación por flujos de detritos (ej. en púrpura), que pueden ocultar a las otras dos en los valles. Esto proporciona una visión integral de la amenaza, desde la cabecera hasta el abanico aluvial.

### Calibración y Evaluación

La calibración de este modelo es un proceso multifacético que busca ajustar los parámetros empíricos para que los resultados coincidan con los datos de eventos observados. El estudio de Brien et al. {cite}`obrien_linkedmodel_2003` detalla este proceso:

1.  **Análisis de los eventos observados:** Se utiliza un inventario de deslizamientos (como el del Huracán María) para extraer las características de los flujos más móviles y así definir rangos plausibles para los parámetros de las zonas de crecimiento (pendiente, orden del cauce, etc.).
2.  **Definición de escenarios:** Se crean múltiples escenarios de simulación variando sistemáticamente los parámetros más influyentes, como los umbrales para las zonas de crecimiento, el factor de crecimiento $c_1$ y el volumen máximo $V_{max}$.
3.  **Evaluación cuantitativa (análisis ROC):** Cada escenario se evalúa cuantitativamente comparando el área de inundación simulada con las áreas observadas de los flujos más móviles. Se utilizan métricas de tablas de contingencia como la Tasa de Verdaderos Positivos (TPR) y la Tasa de Falsos Positivos (FPR).
4.  **Selección de sscenarios finales:** Se seleccionan los escenarios que ofrecen el mejor compromiso entre predecir correctamente los flujos observados (alto TPR) y no sobreestimar en exceso el peligro (bajo FPR). A menudo, se eligen dos escenarios para los mapas finales: uno "más probable" (con un buen balance TPR/FPR) y uno "más peligroso" o de "peor caso" (con un TPR más alto, aceptando un mayor FPR).
