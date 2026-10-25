<p style="font-size:11px;"><em><strong>Créditos</strong>: El contenido de este capítulo ha sido tomado de varias fuentes, pero especialmente de *Horton et al. {cite}`horton_regional_2024`* Regional debris-flow hazard assessments en Advances in Debris-flow science and practice Eds. Matias Jakob, Scott McDougall, Paul Santi. (2024).</em></p>

# Modelos de propagacion
El modelamiento de la propagación de flujos de escombros a escala regional suele incluir dos componentes principales: (i) Algoritmos que definen el camino (y la dispersión) de la propagación, y (ii) Algoritmos que controlan la distancia de alcance. Aunque estos componentes pueden interactuar de manera diferente según el modelo, los principios básicos suelen ser similares.  A continuación, se presentan los enfoques principales.

## Encauzamiento del flujo

### Algoritmos de dirección del flujo

Los algoritmos de dirección del flujo provienen de aplicaciones hidrológicas y determinan cómo se distribuye el flujo desde una celda determinada del modelo digital de elevación (**DEM**) hacia sus celdas vecinas, mientras se encauza a través del terreno de píxel en píxel. Estos algoritmos se basan en el gradiente de pendiente y siguen diferentes reglas, las cuales se describen a continuación para los métodos más populares.

---

* **D8 (ocho posibles direcciones de flujo), máxima pendiente o flujo unidireccional** {cite}`ocallaghan_2003`

Es el método más simple y sigue la pendiente más pronunciada. El principal problema de este enfoque es que no permite divergencia del flujo, forzándolo a seguir direcciones con pasos de 45° (cardinales y diagonales), lo cual produce patrones poco realistas en laderas con diferentes orientaciones. Además, no puede desviarse de la dirección de máxima pendiente, incluso en áreas menos inclinadas, por lo que no representa con precisión los procesos de flujo de escombros {cite}`huggel_2019`. Por lo tanto, tiene un potencial limitado para aplicaciones realistas, pero puede usarse en análisis rápidos {cite}`horton_flowr_2013,wichmann_gpp_2017`.

Huggel et al. {cite}`huggel_2019a` introdujeron una cierta capacidad de desviación del flujo en el enfoque D8, mediante una función que permite que el flujo se desvíe de la dirección de máxima pendiente hasta 45° a ambos lados, aplicando un factor de resistencia.

:::{figure-md} D8
<img src="https://i.pinimg.com/736x/4e/b3/c8/4eb3c89daa0238446c582a9c9df83b26.jpg" alt="Flow axonomy" width="200px">

Esquema del algoritmo de dirección de flujo D8, que distribuye el flujo hacia una de ocho direcciones posibles según la máxima pendiente.
:::
---

* **D-infinity** {cite}`tarboton_1997`

Este algoritmo distribuye el flujo hacia una o dos celdas vecinas, según el cálculo de los vectores de pendiente descendente en facetas triangulares planas. Aunque es popular en aplicaciones hidrológicas, no es comúnmente utilizado para la propagación de flujos de escombros.

:::{figure-md} Dinfinity
<img src="https://i.pinimg.com/1200x/ff/22/a4/ff22a4cb258c8a3a0ad5e56b35503a06.jpg" width="200px">

Esquema del algoritmo D-infinity, que distribuye el flujo entre una o dos celdas vecinas según los vectores de pendiente descendente en facetas triangulares planas.
:::

---

* **Dirección de flujo múltiple (MFD)** {cite}`quinn_prediction_1991`

Este enfoque distribuye el flujo entre todas las celdas vecinas con menor elevación, proporcionalmente al gradiente de pendiente. Incluye un factor de ponderación geométrico para calcular la fracción de flujo que drena hacia las celdas vecinas. Aunque es más realista que el D8 para representar la dispersión lateral del agua, genera una divergencia considerada excesiva para procesos de flujo de escombros {cite}`huggel_2019a`. Algunas variantes {cite}`freeman_calculating_1991` reducen ligeramente esta dispersión, pero no de forma significativa.

:::{figure-md} mfd
<img src="https://i.pinimg.com/736x/ea/70/e5/ea70e5dd786316ba7ef632470bd84480.jpg" width="300px">

Esquema del algoritmo de dirección de flujo múltiple (MFD), que distribuye el flujo entre todas las celdas vecinas de menor elevación proporcionalmente al gradiente de pendiente.
:::

---

* **Dirección de flujo múltiple ajustable** {cite}`holmgren_multiple_1994`

Holmgren introdujo un exponente ajustable en el algoritmo MFD para controlar la convergencia/divergencia del flujo:

$$
f_i = \frac{(\tan \beta_i)^x}{\sum_{j=1}^{8} (\tan \beta_j)^x} \quad \text{para todos } \beta > 0
$$

donde: $i$, $j$: direcciones del flujo (1 a 8), $f_i$: proporción del flujo (entre 0 y 1) en la dirección $i$, $\tan \beta_i$: pendiente entre la celda fuente y la celda vecina en la dirección $i$, $x$: exponente de control de la divergencia. Valores más altos de $x$ producen patrones de flujo más estrechos y convergentes. Este algoritmo puede reproducir otros algoritmos: Con $x = 1$, el patrón es similar al MFD clásico. Con $x \to \infty$, se reproduce el comportamiento del D8.

Horton et al. {cite}`horton_flowr_2013` extendieron este algoritmo incorporando un ajuste a la celda fuente mediante un factor $dh$, que permite ignorar irregularidades menores del DEM y simular un cierto espesor del flujo de escombros.

---

* **Dirección de flujo múltiple para flujos de escombros {cite}`gamma_dflow_2000`**

Desarrollado específicamente para la simulación de flujos de escombros, generalmente implementado como un modelo de caminata aleatoria (*random walk*). Mientras que los algoritmos hidrológicos simulan la dispersión lateral sobre todas las pendientes, los flujos de escombros siguen predominantemente la línea de máxima pendiente.

Gamma {cite}`gamma_dflow_2000` introdujo un umbral de pendiente ($\beta_{\text{thres}}$) que controla cuándo comienza la dispersión lateral. En secciones muy empinadas, el flujo solo sigue celdas con pendientes pronunciadas. En pendientes moderadas, se permiten celdas vecinas más suaves. En zonas planas, se permiten todas las celdas vecinas más bajas. El valor $\gamma_i$ para cada celda vecina se calcula como:

$$
\gamma_i = \frac{\tan \beta_i}{\tan \beta_{\text{thres}}}
$$

La pendiente máxima entre los vecinos se define como $\gamma_{\text{max}} = \max(\gamma_i)$. Si $\gamma_{\text{max}} > 1$, se utiliza el enfoque D8. De lo contrario, se permite el flujo a celdas adicionales según: $
\gamma_i \geq \gamma_{\text{max}}^{\alpha}$, donde $\alpha$ es un exponente que controla el grado de divergencia ($\alpha \geq 1$). El conjunto $N$ de posibles celdas de destino se define como:

- Si $0 < \gamma_{\text{max}} \leq 1$:

$$
N = \{i \mid \gamma_i \geq \gamma_{\text{max}}^{\alpha}\}
$$

- Si $\gamma_{\text{max}} > 1$:

$$
N = \{i \mid \gamma_i = \gamma_{\text{max}}\}
$$

A partir de este conjunto N, se selecciona una celda al azar. La probabilidad de que cada celda sea seleccionada está dada por

$$
\text{prob}_i = \frac{f_i \cdot \tan \beta_i}{\sum_j f_j \cdot \tan \beta_j}
$$

donde *i* es la celda vecina que se está procesando actualmente (1–8), *j* representa todas las celdas vecinas en el conjunto *N*, y *f* es un factor de ponderación. Este último puede dar un peso mayor a la dirección de flujo previa. Debido al componente estocástico, un alto número de ejecuciones del modelo (simulación de Monte Carlo) desde la misma celda de inicio resultará en trayectorias de flujo (ligeramente) diferentes en cada iteración. Al agregar todas las ejecuciones del modelo, se reproduce la extensión completa del área del proceso. El ráster final del área del proceso almacena la frecuencia de transición para cada celda, es decir, el número de veces que cada celda fue seleccionada como parte de la trayectoria del proceso.

Las trayectorias de flujo resultantes de procedimientos de caminata aleatoria generalmente no representan líneas suaves, sino que pueden mostrar oscilaciones a pequeña escala, que a menudo son invisibles al visualizar la superposición de múltiples trayectorias de caminata aleatoria. Sin embargo, estas oscilaciones tienen un impacto en el cálculo de la distancia recorrida: por ejemplo, cuando se utilizan enfoques de línea de energía (ángulos de alcance), una trayectoria de flujo oscilante implicaría una mayor distancia recorrida y, por lo tanto, un ángulo de alcance menor, en comparación con una trayectoria de flujo suave. Por lo tanto, los criterios de detención se aplicarían demasiado pronto en el procedimiento de enrutamiento, y la distancia recorrida se subestimaría. Deben aplicarse funciones de suavizado para contrarrestar este problema {cite}`mergili_randomwalk_2015`.

---

* **Funciones de persistencia**

Gamma {cite}`gamma_dflow_2000` introdujo el concepto de persistencia para representar la inercia del flujo.  Esta persistencia consiste en un peso aplicado según el cambio en la dirección del flujo, de forma que el flujo tiende a mantener su dirección previa:

$$
p_i = w_{\alpha(i)}
$$

donde:  $p_i$ es la persistencia en la dirección $i$,  $\alpha(i)$ es el ángulo entre la dirección previa y la dirección desde la celda fuente hacia la celda $i$.

Un aspecto clave es definir el peso $w_{\alpha(i)}$ para cada dirección. En los flujos de escombros, es poco probable que el flujo cambie abruptamente de dirección, por lo que tiende a mantener su trayectoria. El esquema de pesos propuesto por Gamma {cite}`gamma_dflow_2000` asigna un 50% más de peso en la misma dirección previa. Horton et al. {cite}`horton_flowr_2013` propusieron esquemas alternativos de ponderación, resumidos en la siguiente tabla.

| Cambio en la dirección | Gamma {cite}`gamma_dflow_2000` | Horton et al. {cite}`horton_flowr_2013` - Proporcional | Horton et al. {cite}`horton_flowr_2013` - Coseno | Han et al. (2017) <10º | Han et al. (2017) 10º–17º | Han et al. (2017) 17º–23º | Han et al. (2017) >23º |
|------------------------|---------------|-------------------------------------|-------------------------------|------------------------|--------------------------|--------------------------|------------------------|
| **0° (sin cambio)**    | 0.22          | 0.28                                | 0.42                          | 0.30                   | 0.40                     | 0.50                     | 0.60                   |
| **45°**                | 0.13          | 0.24                                | 0.29                          | 0.24                   | 0.20                     | 0.18                     | 0.15                   |
| **90°**                | 0.13          | 0.12                                | 0.00                          | 0.11                   | 0.10                     | 0.07                     | 0.05                   |
| **135°**               | 0.13          | 0.00                                | 0.00                          | 0.00                   | 0.00                     | 0.00                     | 0.00                   |
| **180° (reversa)**     | 0.00          | 0.00                                | 0.00                          | 0.00                   | 0.00                     | 0.00                     | 0.00                   |



Este factor de persistencia se combina con el algoritmo de dirección del flujo para generar una distribución de flujo modificada (o de susceptibilidad).

La variación de los esquemas de ponderación puede estar relacionada con la topografía local, que controla la velocidad del flujo. Han et al. (2017), mediante experimentos en canales, propusieron esquemas basados en el ángulo de la pendiente, demostrando que cuando el ángulo de la pendiente en la dirección previa es mayor a 10°, el esquema se asemeja al esquema de coseno de Horton et al. {cite}`horton_flowr_2013`. Cuando la pendiente es menor a 10°, el comportamiento es más similar a los esquemas proporcionales de Gamma {cite}`gamma_dflow_2000` y Horton et al. {cite}`horton_flowr_2013`.

Aunque estas oscilaciones locales suelen ser invisibles al visualizar la superposición de múltiples trayectorias de caminata aleatoria, sí tienen un impacto importante en el cálculo de la distancia recorrida. Por ejemplo, al utilizar enfoques basados en líneas de energía (o ángulos de alcance), una trayectoria oscilante implica una distancia recorrida mayor, lo que a su vez genera un ángulo de alcance menor, en comparación con una trayectoria suave. Como consecuencia, los criterios de detención del flujo se aplicarían prematuramente en el procedimiento de encauzamiento (routing), lo que conduciría a una subestimación de la distancia recorrida por el flujo. Para corregir este problema, se deben aplicar funciones de suavizado, tal como lo proponen Mergili et al. {cite}`mergili_randomwalk_2015`.

* **Enfoques Basados en Agentes (*Agent-Based Approaches*)**
  
Los modelos basados en agentes (ABM) fueron concebidos hace varias décadas {cite}`schelling_1950,wolfram_1984`, pero su uso en el modelamiento regional de deslizamientos ha sido limitado {cite}`guthrie_predictor_2021,guthrie_debris_2008,turcotte_2002`. No existe una definición universal del término *agente* {cite}`macal_2009`. Bonabeau (2002) describe los ABM de la siguiente manera:

> “En la modelación basada en agentes, un sistema se modela como un conjunto de entidades autónomas que toman decisiones, llamadas agentes. Cada agente evalúa individualmente su situación y toma decisiones basadas en un conjunto de reglas.”

Los agentes (o subrutinas autónomas) evolucionan a través de una serie de pasos de tiempo, considerando los valores de sus agentes vecinos (que también están evolucionando) y del medio a través del cual se desplazan (por ejemplo, un modelo digital de elevación - DEM).  Cada evolución del agente está determinada por:  un conjunto de reglas, y  el comportamiento del entorno local.

Los modelos basados en agentes pueden incorporar cualquiera de los enfoques descritos anteriormente para definir la trayectoria y la dispersión del flujo, incluyendo:  algoritmos simples de dirección del flujo,  funciones de persistencia,  caminatas aleatorias,  e incluso enfoques basados en líneas de energía. Un enfoque habitual para seleccionar rutas consiste en redistribuir la masa del deslizamiento contenida en cada agente hacia nuevas celdas, creando nuevos agentes. Esta redistribución se basa en una función de densidad de probabilidad, cuya media está centrada en la dirección de desplazamiento del agente y cuya desviación estándar determina la dispersión {cite}`guthrie_predictor_2021`.

La incorporación de probabilidades en la dispersión de agentes produce trayectorias más realistas y reduce la convergencia forzada del flujo. 
Sin embargo, algunos problemas de selección de trayectoria observados en el modelo D8 pueden persistir, especialmente en laderas empinadas.

### Enfoques de Línea de Energía (*Energy Line Approaches*)

Los enfoques basados en la línea de energía son populares por su simplicidad y su capacidad de vincularse con datos de campo.   Se basan en las distancias vertical y horizontal entre el área fuente y el punto más distante alcanzado por el flujo de escombros. La versión más común para flujos de escombros es el ángulo de recorrido, también conocido como *Fahrböschung* {cite}`heim_bergsturz_1932`,  ángulo de alcance {cite}`corominas_angle_1996` o línea α, donde la distancia horizontal se mide a lo largo del recorrido del flujo (no en línea recta). Este ángulo se calcula como:

$$
\tan \alpha = \frac{d_v}{d_h}
$$

donde: $\alpha$ es el ángulo respecto a la horizontal, $d_v$ es el desplazamiento vertical, $d_h$ es el desplazamiento horizontal. A lo largo de una línea de energía recta desde el inicio hasta la posición final, la velocidad $v_i$ puede calcularse como {cite}`korner_1980`:

$$
v_i = \sqrt{2 g h_v}
$$

donde:  $v_i$ es la velocidad en la celda actual $i$ [m/s],  $g$ es la aceleración de la gravedad [m/s²],  $h_v$ es la diferencia de altura entre la línea de energía y la celda $i$.

:::{figure-md} linea de energia
<img src="https://i.pinimg.com/1200x/03/5a/59/035a59d6cf09315ff4c40183f1967ee4.jpg" width="500px">

Esquema del enfoque de línea de energía (ángulo de alcance o *Fahrböschung*) utilizado para estimar la distancia de recorrido de un flujo de escombros.
:::

El ángulo $\alpha$ presenta valores característicos según el tipo de movimiento gravitacional. Para flujos de escombros con alta proporción de sedimentos finos, el ángulo mínimo observado es de aproximadamente 4° ($\tan \alpha \approx 0.07$). Para flujos con material más grueso, el ángulo es de **10–11°** ($\tan \alpha \approx 0.19$) {cite}`rickenmann_runout_2005`.

El ángulo $\alpha$ también puede expresarse como función del volumen del flujo o del área de la cuenca de aporte. Con base en datos de campo, Zimmermann et al. {cite}`zimmermann_2020` propusieron la siguiente relación para el límite inferior del ángulo $\alpha$ en función del área de cuenca $A$ [km²]:

$$
\tan \alpha_{\text{min}} = 0.20 A^{-0.26}
$$

El modelo ACS (*Average Channel Slope Model*) propuesto por Prochaska et al. {cite}`prochaska_debris_2008` fue desarrollado para flujos de escombros de tamaño moderado.
Este modelo se basa en el cálculo del ángulo entre la elevación media del canal de drenaje y el final de la zona de alcance del flujo. Dicho ángulo puede estimarse a partir de otra relación angular: la que existe entre la elevación media del canal y el inicio del abanico aluvial (*fanhead*).

El modelo ACS no requiere conocer el punto de inicio del flujo, que en muchos casos es desconocido o difícil de determinar con precisión. El punto de referencia (elevación media del canal) es objetivo y reproducible, lo que facilita su aplicación en diferentes estudios.

El método mostró buenos resultados, pero fue desarrollado y probado en un número limitado de casos de estudio,  por lo que su aplicabilidad puede depender de las condiciones locales de los sitios analizados. A pesar de estas limitaciones, el modelo ACS ha mostrado resultados prometedores al compararse con métodos tradicionales basados en el ángulo de alcance (*reach angle*).

## Modelos empíricos basados en morfometría de cuenca

Los modelos basados en física descritos en este capítulo requieren una cantidad considerable de parámetros de entrada. Como alternativa rápida y de bajo costo computacional para estimar la susceptibilidad y el tipo de proceso dominante esperado en el ápice de un abanico aluvial, se han desarrollado modelos empíricos basados en índices morfométricos calculados directamente a partir de un modelo digital de terreno (MDT).

### El índice de Melton

El **índice de Melton** ($M_r$), o relación de relieve de la cuenca, se define como:

$$
M_r = \frac{H}{\sqrt{A}}
$$

donde $H$ es el relieve total de la cuenca (diferencia de altura entre el punto más alto de la divisoria de aguas y el punto de salida) y $A$ es el área de la cuenca drenada. Este índice sirve para clasificar y discriminar la susceptibilidad de una cuenca a generar diferentes tipos de flujos torrenciales: cuencas pequeñas y de relieve muy pronunciado (índices de Melton altos) favorecen flujos de detritos, mientras que cuencas más extensas y de menor relieve relativo favorecen inundaciones ordinarias {cite}`ramos_avenidas_2021`.

Wilford et al. {cite}`wilford_2004` propusieron una metodología que cruza el índice de Melton con la longitud del canal principal y la pendiente del cauce o del abanico aluvial para predecir el proceso dominante esperado en el punto de entrega de sedimentos:

- **Cuencas propensas a flujo de detritos**: típicamente cuencas pequeñas, escarpadas y confinadas, con $M_r > 0{,}6$ y pendiente del canal o del abanico superior a 30 %.
- **Cuencas propensas a inundación de escombros (*debris flood*)**: cuencas transicionales, con $M_r$ entre 0,3 y 0,6 y pendientes de abanico intermedias.
- **Cuencas propensas a inundaciones ordinarias o crecientes súbitas**: cuencas de mayor tamaño y menor relieve relativo, con $M_r < 0{,}3$ y abanicos de pendiente muy suave.

Para las cuencas de los Andes colombianos, Arango, Aristizábal y Gómez {cite}`arango_morphometrical_2021` realizaron un análisis morfométrico de cuencas propensas a avenidas torrenciales, aportando umbrales de referencia adaptados a las condiciones tropicales de alta montaña del país.

### Limitaciones del enfoque morfométrico frente a eventos de alta magnitud

Investigaciones geomorfológicas forenses recientes —como el estudio del impacto de la tormenta Vaia (2018) en los Dolomitas italianos— han identificado que los umbrales puramente morfométricos tienden a fallar y a subestimar la ocurrencia de inundaciones de escombros durante eventos hidrológicos de muy alta magnitud (períodos de retorno superiores a 200 años) {cite}`brenna_geomorphic_2023`. Bajo lluvias extremas, la inyección masiva de sedimento desde laderas inestables y la superación de umbrales de potencia de corriente unitaria del orden de 5000–5500 W/m² pueden forzar transiciones reológicas dinámicas incluso en tramos de pendiente suave que los índices puramente estáticos (calculados una sola vez a partir de la topografía) no logran anticipar. Esto sugiere que los modelos morfométricos son más confiables como herramienta de tamizaje regional (*screening*) que como predictor único de la amenaza ante eventos extremos, y deben complementarse con los modelos hidrológicos e hidráulico-reológicos presentados en los capítulos anteriores.

## Herramientas comerciales de propagación: FLO-2D y HEC-RAS

Además de las herramientas de código abierto descritas en los capítulos de esta parte, dos programas comerciales/gratuitos ampliamente usados en la práctica profesional para la propagación de avenidas torrenciales son **FLO-2D** y **HEC-RAS**, descritos en detalle en los capítulos [FLO-2D](17_FLO2D.md) y [HEC-RAS](18_HECRAS.md), que incluyen una comparación de su desempeño en varios casos de estudio recientes (rotura de presas de relaves, flujos de detritos y aluviones en Taiwán, California y Chile).
