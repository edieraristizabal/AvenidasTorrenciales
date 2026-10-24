# Gravitational Process Path (GPP)

El software *Gravitational Process Path (GPP)* ha sido diseñado para simular la trayectoria del proceso y el área de runout de procesos gravitacionales basado en un modelo digital de elevación (*DEM*). 
Los primeros componentes del software se desarrollaron desde el año 2000 {cite}`wichmann_becht_2004` y una implementación completamente revisada y de código abierto se publicó en 2017 {cite}`wichmann_gpp_2017`. El software está implementado en *C++* como una herramienta para el *System of Automated Geoscientific Analyses (SAGA)* {cite}`conrad_2015`, lo que facilita la preparación de datos de entrada y el análisis de resultados del modelo.

**Modelo conceptual**. El modelo conceptual combina varios componentes (trayectoria del proceso, longitud de *runout*, relleno de depresiones y deposición de material) para simular el movimiento de un punto de masa desde la fuente hasta el área de depósito.

Desde la versión 1.1 {cite}`wichmann_2006`, el software también permite detectar objetos potencialmente en peligro, como infraestructuras, e informar desde qué trayectorias y áreas fuente podrían verse afectados. Se proporcionan varios enfoques de modelado para cada componente, lo que hace que el software sea configurable para diferentes procesos como caídas de rocas, flujos de escombros o avalanchas de nieve. El software ha sido desarrollado principalmente para estudios a escala regional, pero también contiene componentes para modelado basado en escenarios de eventos individuales.

**Componentes**. El diseño del GPP es modular, lo que permite combinar diferentes métodos para el cálculo de la trayectoria del proceso, la longitud del runout y la deposición del material (relleno de depresiones). Todos los componentes, excepto el cálculo de la trayectoria del proceso, son opcionales.

Se encuentran disponibles los algoritmos de dirección de flujo de *pendiente máxima* {cite}`ocallaghan_2003` y *mfdf* {cite}`gamma_dflow_2000` para el cálculo de trayectorias. Este último está implementado como un paseo aleatorio (*random walk*), según Gamma {cite}`gamma_dflow_2000`. La dispersión lateral del proceso se simula realizando varias iteraciones desde la misma celda de inicio (*simulación Monte Carlo*). Cuando se combina con el modelo de relleno de depresiones, las iteraciones posteriores pueden superar depresiones pequeñas en el DEM.

El *random walk* proporciona tres parámetros de calibración: un umbral de pendiente por encima del cual no se modela dispersión lateral, un exponente que controla la cantidad de dispersión lateral y un factor de persistencia que añade más peso a la dirección de flujo actual.

Se proporcionan tres enfoques basados en la línea de energía para determinar la longitud del *runout* (velocidad), así como un modelo de fricción de uno y dos parámetros. Los métodos disponibles basados en la línea de energía incluyen el gradiente geométrico {cite}`heim_bergsturz_1932`, el *Fahrböschung* {cite}`heim_bergsturz_1932` y el ángulo de sombra {cite}`hungr_1993`. El modelo de un parámetro {cite}`scheidegger_1975` simula la velocidad basada en fricción por deslizamiento o rodadura. El modelo de dos parámetros es el modelo PCM de Perla et al. {cite}`perla_twoparameter_1980`.

En GPP es posible usar coeficientes de fricción espacialmente distribuidos (además de constantes), lo que permite simular, por ejemplo, un aumento en el contenido de agua a lo largo del trayecto y la consiguiente reducción de la fricción. Para cálculos de runout de flujos de escombros, generalmente se usan el *Fahrböschung* o el modelo PCM.

GPP implementa varios enfoques de deposición, siendo el relleno de depresiones el más importante. Cuando la trayectoria llega a una depresión, el sumidero se rellena con el material disponible, utilizando un algoritmo que preserva cierta pendiente descendente. Esto permite superar la depresión en iteraciones posteriores. Otros enfoques depositan el material al detenerse o a lo largo de la trayectoria según umbrales de pendiente y velocidad, alterando el DEM e influyendo en las iteraciones posteriores (por ejemplo, simulando el taponamiento de un canal).

**Datos**. En su configuración básica, el modelo solo requiere un DEM y un ráster con las áreas fuente potenciales. Estos datos pueden prepararse directamente en SAGA. Si no se modela deposición, los sumideros del DEM deben rellenarse previamente, como en análisis hidrológicos.

Las áreas fuente pueden identificarse mediante diversos métodos. Si no se hace en SAGA, es posible importar un ráster con las fuentes en diferentes formatos.

Si se va a modelar deposición, se requiere un ráster con el material disponible en cada celda de inicio. Para el modelado del runout, se pueden usar coeficientes de fricción espacialmente distribuidos (o ángulos en el caso de enfoques basados en la línea de energía). Estos rásteres pueden derivarse, por ejemplo, de mapas de cobertura del suelo o acumulación de flujo (área de captación). Gamma {cite}`gamma_dflow_2000` derivó relaciones funcionales entre el área de captación y el coeficiente de fricción por deslizamiento para escenarios de mínima, probable y máxima distancia de runout.

**Salidas**. GPP genera resultados como capas ráster:

- **Área del proceso**: se reporta como un ráster que almacena la frecuencia de transición, es decir, el número de veces que una celda ha sido parte de la trayectoria del proceso.
- **Velocidad máxima**: se guarda en otro ráster.
- **Altura del depósito**: si se modela deposición, un ráster muestra la altura del material depositado. Sumado al DEM original, muestra la topografía final después de la simulación.
- **Posiciones de detención**: opcionalmente, un ráster muestra las celdas donde se alcanzó la distancia máxima de runout.
- **Objetos potencialmente impactados**: si se proporciona un ráster de objetos potencialmente en peligro, se genera un ráster que informa qué clases de objetos pueden ser afectadas desde cada celda.

**Calibración y evaluación**. Una configuración típica para cartografía regional de susceptibilidad utiliza el *random walk* para la trayectoria y el modelo PCM para el cálculo de distancia de runout.

Los parámetros se calibran probando diferentes valores dentro de un rango determinado. El resultado de cada combinación de parámetros se compara con eventos mapeados de flujos de escombros, y se elige el conjunto que muestra mejor ajuste con las áreas mapeadas. Un procedimiento automático de optimización y validación de parámetros para GPP fue desarrollado por Goetz et al. {cite}`goetz_2015`.
