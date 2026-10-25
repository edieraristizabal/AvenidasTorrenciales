# OpenLISEM

### Componentes del modelo
El software Limburg Soil Erosion Model (LISEM) es una herramienta de simulación físicamente basada para el análisis multi-amenaza que presenta amplias capacidades para la simulación, calibración y análisis de movimientos en masa como los flujos de detritos.

Las funciones de propagación de flujo de detritos en el software LISEM son una adaptación de las ecuaciones generalizadas bifásicas de flujo de detritos de Pudasaini {cite}`pudasaini_twophase_2012`. Este conjunto de ecuaciones considera de manera distinta la conservación de la masa y del momento para los fluidos y los sólidos. Varias fuerzas actúan sobre estas fases durante el flujo, resultando en un flujo acoplado de manera realista. Las fuerzas implementadas originalmente son: gravedad, presión, flotabilidad, fricción de Mohr–Coulomb, viscosidad, viscosidad no newtoniana y masa virtual.  

Dentro de LISEM, el objetivo es simular una amplia gama de tipos de flujo, desde el flujo de agua clara hasta flujos de detritos, deslizamientos de tierra y avalanchas de rocas. Aunque las ecuaciones generalizadas bifásicas capturan de manera efectiva múltiples tipos de movimientos en masa, el flujo de agua clara carecía de una fuerza de fricción en la interfaz entre el flujo y el terreno. Esto resultaba en velocidades de flujo poco realistas en terrenos inclinados, un problema que ha sido abordado en softwares como r.avaflow por Pudasaini y Mergili {cite}`pudasaini_mergili_2019`. Dentro de LISEM, se utilizó el enfoque del campo hidrológico e hidráulico, donde la ley de fricción de Darcy-Weisbach, un estándar industrial para modelos de inundación {cite}`delestre_fullswof_2017`, fue implementada proporcionalmente a la presencia de fluidos en el flujo {cite}`vandenbout_openLISEM_2018`.

Como LISEM se enfoca fuertemente en la investigación multi-amenaza, las ecuaciones de flujo están integradas dentro de un modelo hidrológico a escala de cuenca que es capaz de simular infiltración, aguas subterráneas, estabilidad y falla de laderas, así como flujos en ríos, erosión, propagación de tsunamis, entre otros {cite}`vandenbout_openLISEM_2018`. Esto permite configuraciones más complejas que implican flujos de detritos impulsados por escorrentía a través de la erosión, o flujos de detritos que evolucionan a partir de deslizamientos, donde el material del deslizamiento se combina con el agua de escorrentía superficial. Debido a que se consideran detalles subpíxel para canales, edificaciones y carreteras, las simulaciones hidrológicas, de flujo y propagación pueden realizarse desde la escala de parcelas (espaciado de celdas de varios cm, con un área total de varios metros cuadrados) hasta la escala nacional (espaciado de celdas de 200 m, con un área total de decenas de miles de kilómetros cuadrados). Los resultados óptimos de las simulaciones de flujo de detritos suelen obtenerse con un espaciado de celdas de 10 m o menos, ya que los detalles relevantes en el modelo de elevación capturan la escala común de propagación de los flujos de detritos.

Las soluciones numéricas a las ecuaciones se resuelven utilizando un esquema de diferencias finitas de segundo orden en una cuadrícula cuadrada. Para capturar las dinámicas de onda y mantener un mayor paso de tiempo, se utilizan limitadores de flujo en combinación con *solvers Riemann* adaptados {cite}`delestre_fullswof_2017`. Los *solvers Riemann* proporcionan soluciones semi-analíticas a problemas de discontinuidad en las interfaces de las celdas de la cuadrícula. El flujo efectivo de masa y momento puede calcularse resolviendo dominios de influencia utilizando velocidades características.
Mientras que las ecuaciones de Saint–Venant para el flujo de agua poco profunda permiten tal solución, las ecuaciones generalizadas de flujo de detritos no permiten la derivación de velocidades características. En su lugar, se emplea el *solver Riemann* de Harten-Lax-van Leer para el agua clara, mientras que un cálculo directo del flujo se activa con concentraciones más altas de sólidos. Todos los cálculos pueden realizarse en paralelo tanto en CPU como en GPU.

### Parámetros comunes 
| Componente del modelo | Parámetro                          | Unidad       |
|------------------------|------------------------------------|--------------|
| Flujo                  | Rugosidad de Manning               | –            |
|                        | Coeficiente de arrastre            | –            |
|                        | Altura inicial de sólidos          | m            |
|                        | Altura inicial de agua             | m            |
|                        | Ángulo basal de fricción           | radianes     |
|                        | Densidad del material              | kg/m³        |
|                        | Tamaño de grano del material       | m            |
| Hidrología             | Conductividad hidráulica saturada  | mm/h         |
|                        | Porosidad                          | m³/m³        |
|                        | Contenido de humedad               | m³/m³        |
|                        | Succión matricial                  | kPa          |
| Estabilidad de laderas | Profundidad del suelo              | m            |
|                        | Cohesión del suelo                 | kPa          |
|                        | Ángulo de fricción interna         | radianes     |
| Erosión                | Tamaño del grano                   | m            |
|                        | Cohesión del suelo                 | kPa          |
|                        | Coeficiente de entrainment         | –            |

---

### Necesidades de datos
El requisito de datos central es el DEM, que determina en gran medida la calidad de las simulaciones. Para el modelado del flujo, deben estar disponibles las propiedades del material, a saber: densidad, tamaño de grano, ángulo basal de fricción del material, y las cantidades de fluidos y sólidos. Si los materiales iniciales se estiman mediante el modelado de la estabilidad de laderas, se requieren la densidad, el tamaño de grano y el ángulo de fricción interna del suelo, así como la profundidad del suelo.El contenido de agua del suelo puede predecirse mediante los componentes hidrológicos del software.
Finalmente, el coeficiente de rugosidad superficial de Manning se utiliza para estimar la fuerza de fricción de la fase líquida.

Dependiendo de los procesos adicionales que se simulen, se requieren datos adicionales sobre las propiedades superficiales (cobertura vegetal, cohesión de raíces, y presencia de edificaciones), condiciones de borde (caudal en ríos, condición de borde para altura o velocidad del flujo, precipitación o deshielo), o información sobre canales (red de canales, dimensiones del canal).
13.6.4.3 Salidas

El software puede generar una variedad de variables del modelo en formato GeoTIFF o en cualquier otro formato soportado por GDAL.

Las posibles salidas para cada paso temporal (dinámicas) incluyen: alturas de flujo, velocidades, alturas de fase, velocidades de fase, fallas de laderas, contenidos de agua, cantidades de infiltración, factores de seguridad, y muchas más, dependiendo de los procesos que estén activados.

Para toda la simulación, se pueden proporcionar: altura máxima del flujo, velocidades máximas, y factores de seguridad mínimos.

Dado que toda la funcionalidad del software LISEM está disponible desde el entorno de scripting relacionado, el usuario tiene acceso completo a todos los parámetros internos del modelo y puede especificar salidas personalizadas.
Un ejemplo de una salida típica se muestra en la Fig. 13.12.

### Calibración y Evaluación

El software LISEM puede calibrarse de manera similar a muchos otros métodos de modelación físicamente basada. 
Siempre que se disponga de datos de observación sobre algunos parámetros, el usuario puede especificar una métrica de error de simulación personalizada y ejecutar una secuencia de calibración automática. 
LISEM implementa tanto métodos de fuerza bruta (*brute-force*) como métodos de descenso por gradiente.

El enfoque de fuerza bruta varía los parámetros de entrada de forma regular para intentar cubrir todos los valores de entrada posibles y encontrar el mejor rendimiento del modelo.
Aunque este método es fiable, puede aumentar exponencialmente los costos computacionales a medida que crece el número de parámetros.

El método de descenso por gradiente intenta avanzar hacia un menor error del modelo determinando repetidamente el gradiente de la función de error del modelo (dependiendo de los valores de los parámetros) y ajustando los parámetros para obtener menores errores.
LISEM utiliza el método *Armijo-backtracking* para determinar los tamaños de paso, garantizando una búsqueda estable de los mínimos errores del modelo.

El enfoque más común es la calibración utilizando un inventario de las extensiones de los flujos de detritos.
Aquí, los contornos de los flujos de detritos se mapean a partir de imágenes satelitales o aéreas.
Los resultados del modelo se comparan como dos clasificadores binarios, utilizando una métrica como *Kappa de Cohen* o *Precisión-Recall*.

El segundo método más común de calibración utiliza series temporales de altura de flujo o caudal.
Las observaciones de estas variables pueden compararse con la salida del modelo utilizando, por ejemplo, el Error Cuadrático Medio (RMSE) o las métricas de error de *Nash–Sutcliffe*.
