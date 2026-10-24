
# Flow-Py

*Flow-Py* es una herramienta de modelado que proporciona estimaciones numéricas del alcance de flujos gravitacionales sobre la topografía. 
Fue desarrollada en el Centro Austríaco de Investigación Forestal y la Universidad de Innsbruck, y publicada en 2022. 
El software se clasifica como un modelo empírico basado en la topografía, utilizando el enfoque de la línea α {cite}`lied_empirical_1980`, el criterio de flujo mínimo para la propagación máxima del flujo, el algoritmo de Holmgren {cite}`holmgren_multiple_1994` para el enrutamiento de trayectorias y el enfoque de persistencia de Gamma {cite}`gamma_dflow_2000`. 
La filosofía general del modelado se basa en el concepto de línea de energía, donde la línea de energía conecta la zona de inicio con el extremo del flujo, derivada del principio de conservación de la energía. 
Sin embargo, la derivación del contenido energético local del flujo solo produciría resultados plausibles para superficies de escorrentía perfectamente uniformes con parámetros de fricción distribuidos de manera homogénea.

El software fue codificado en Python y su código fuente está disponible de forma gratuita {cite}`lombardo_2022`. 
Ha sido aplicado a avalanchas, caídas de rocas y deslizamientos superficiales para la identificación de peligros a escala regional e indicativa. 
Los autores sugieren que los flujos de detritos y las avalanchas húmedas también podrían evaluarse basándose en la experiencia con herramientas de modelado similares.

### Componentes

Se utilizan tres enfoques para calcular la propagación del flujo, considerando el principio de conservación de la masa, la identificación del trayecto asociado a la topografía {cite}`holmgren_multiple_1994` y la consideración del momento de los elementos de masa {cite}`gamma_dflow_2000`. 
La conservación de la masa se satisface equilibrando los volúmenes que se detienen en las celdas según los criterios de detención y el flujo de volumen en cada celda que experimenta contacto con el flujo de masa.

Las pruebas preliminares confirman que el enfoque de persistencia seleccionado {cite}`gamma_dflow_2000` proporciona una solución alternativa para representar el principio de conservación del momento.

Se utilizan dos criterios de detención. 
Primero, para limitar la propagación máxima del flujo, el software utiliza una aproximación de la disipación de energía del flujo de masa, representada por la línea α {cite}`lied_empirical_1980,heim_bergsturz_1932,corominas_angle_1996`. 
Segundo, se utiliza un criterio de flujo mínimo para considerar un contenido energético mínimo requerido del flujo que permita la propagación adicional.

### Requisitos de datos
Solo se requieren dos conjuntos de datos para realizar los cálculos de alcance: un DEM y un conjunto de datos raster de la zona de inicio, como archivo ascii (.asc) o geotiff (.tiff).

### Salidas

*Flow-Py* produce un conjunto de archivos raster con el siguiente contenido: 
- La energía máxima local del flujo.
- Flujo máximo de enrutamiento local para una celda considerando todas las liberaciones y trayectorias calculadas, respectivamente.
- Suma de las alturas de energía residual detectadas localmente de todos - los flujos de masa que pasan por una celda.
- Conteo de celdas que representa el número de trayectorias que enrutan flujo a través de una ubicación, donde una trayectoria es producida por una - liberación (una celda raster de inicio) en las zonas de inicio.
- Ángulo máximo de recorrido del trayecto del flujo detectado en una celda, producido por todas las trayectorias de flujo que pasan por esa celda. Este valor proporciona la distribución espacial de las alturas máximas de energía residual.
