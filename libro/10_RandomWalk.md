<p style="font-size:11px;"><em><strong>Créditos</strong>: El contenido de este capítulo ha sido tomado de varias fuentes, pero especialmente de Advances in Debris-flow science and practice Eds. Matias Jakob, Scott McDougall, Paul Santi. (2024), {cite}`mergili_2015b`[https://www.landslidemodels.org/r.randomwalk/manual.php].</em></p>

# r.randomwalk

*r.randomwalk* {cite}`mergili_2022b,mergili_randomwalk_2015` representa una herramienta multifuncional para el enrutamiento de procesos de movimientos en masa. Puede utilizarse como una extensión del software de código abierto *GRASS GIS*. La arquitectura de la herramienta consiste en un núcleo programado en *C*, con un script en *Python* envolviéndolo para el pre- y postprocesamiento de los datos. Los procedimientos estadísticos automáticos, visualización y funcionalidades de evaluación están incluidos a través de scripts en *R*, que son llamados directamente desde el script en Python.

### Componentes

El núcleo de *r.randomwalk* es un algoritmo de enrutamiento de caminata aleatoria restringida acoplado a una selección de criterios de detención, incluyendo valores simples y funciones de ángulos de recorrido, así como un modelo físicamente basado simple (modelo de fricción de 2 parámetros). Las caminatas aleatorias pueden iniciarse desde un número arbitrario de puntos individuales o áreas de liberación de deslizamientos, o desde un mapa de susceptibilidad de liberación de deslizamientos, y se enrutan a través del terreno hasta que se cumplen todos los criterios de detención aplicados. Puede utilizarse un número arbitrario de diferentes criterios de detención de diferentes tipos para derivar medidas de probabilidad de que un píxel objetivo sea alcanzado (puntaje del indicador de impacto).

Las simulaciones también pueden ejecutarse con grandes cantidades de combinaciones de parámetros, y la fracción de combinaciones de parámetros que predicen un impacto en un píxel objetivo se denomina *índice del indicador de impacto*. Finalmente, puede estimarse una probabilidad de impacto basada en ángulos de recorrido calculados a partir de deslizamientos observados.

### Datos

La pieza central de datos necesaria para todos los tipos de uso de *r.randomwalk* es un DEM. Aparte de esto, los datos requeridos dependen del tipo de uso. Los píxeles fuente pueden definirse mediante un archivo de texto que proporciona coordenadas y, opcionalmente, volúmenes y otros parámetros que caracterizan la liberación de un número arbitrario de eventos (un evento por línea). Opcionalmente, el archivo de liberación puede vincularse a un mapa raster de liberación, o puede proporcionarse el mapa raster de liberación sin un archivo de liberación. También es posible definir la liberación a través de un mapa de susceptibilidad de liberación de deslizamientos, lo cual es importante para acoplar la liberación y el alcance. En aquellos casos donde se involucre calibración o evaluación, se requiere un mapa raster del área de impacto observada como entrada.

Además, el modelo necesita conjuntos de parámetros que controlan las caminatas aleatorias y los criterios de detención, así como varias otras funcionalidades, dependiendo de su uso.

### Salidas

Además de los mapas raster (como raster ASCII y visualizaciones cartográficas a través de gráficos generados en *R*), *r.randomwalk* proporciona archivos de texto que caracterizan los parámetros de enrutamiento para cada evento bajo investigación, con parámetros como el tamaño del área de impacto, distancias de recorrido horizontal y vertical, y ángulos de recorrido.

También se generan archivos y gráficos adicionales (como gráficos ROC o mapas de calor), que indican la sensibilidad de la salida a la parametrización de las ecuaciones empíricas y el procedimiento de caminata aleatoria, dependiendo de qué funcionalidades del software se utilicen.

### Calibración y evaluación

*r.randomwalk* permite el retrocálculo (*back-calculation*) del ángulo de recorrido y la distancia de desplazamiento de los eventos representados en el mapa del área de impacto. Este retrocálculo puede utilizarse para producir automáticamente una función de densidad de probabilidad de los ángulos de recorrido o distancias de desplazamiento, que luego —nuevamente automáticamente— se puede utilizar para producir un mapa de probabilidad de impacto basado en las mismas áreas fuente u otras áreas fuente arbitrarias.

El mapa de probabilidad de impacto puede evaluarse contra el mapa del área de impacto, resultando en un gráfico ROC que indica el rendimiento del modelo. Si es aplicable al flujo de trabajo específico, es posible dividir los datos en un conjunto de entrenamiento (para la derivación de la función de densidad de probabilidad) y un conjunto de evaluación (para el gráfico ROC).

### Software

`r.randomwalk 2.0` fue desarrollado en entorno LINUX especificamente **Ubuntu 20.04 LTS**, y se basa en **GRASS GIS 7** (Versión 7.8) y **Python 3**. La instalación de `r.randomwalk 2.0` requiere el paquete `grass-dev`. La visualización y evaluación de los resultados del modelo utiliza lenguaje *R* (versión recomendada: 3.6.3 o superior). Se requieren los siguientes paquetes de R para explorar completamente las funcionalidades que ofrece `r.randomwalk 2.0`: `maptools`, `stats`, `sp`, `rgeos`, `rgdal`, `ROCR` y `raster`. 

La herramienta de *r.randomwalk* no viene por defecto en GRASS SIG por lo cual se debe desacargar e instalar. El script `grass7.install.sh` proporcionado con `r.randomwalk 2.0` se puede utilizar para instalar automáticamente todo el software necesario en instalaciones nuevas de Ubuntu 20.04 LTS, y probablemente también en instalaciones de otros entornos LINUX. La descarga se realiza desde la página del profesor Martin Mergili donde se encuentran todos los modelos que ha desarrollado (www.landslidemodels.org)[https://www.landslidemodels.org/r.randomwalk/software.php]. Desde alli se puede descargar un archivo comprimido con la última versión de *r.randomwalk*. Para us instalación se debe abrir GRASS y en la consola, asumiendo que el archivo descomprimido se encuentra en la ruta */home/user1/*, se ejecuta:
```
g.extension extension=r.randomwalk url=/home/user1/r.randomwalk/
```
Tenga en cuenta que si ha instalado versiones anteriores de `r.randomwalk` utilizando el script `r.randomwalk.install.sh`, debe eliminar manualmente los archivos antiguos instalados, por ejemplo, en las carpetas `/usr/lib/grass78/scripts` y `/usr/lib/grass78/bin` para asegurarse de que el comando `r.randomwalk` realmente ejecute la versión recién instalada.

La forma más eficiente de operar *`r.randomwalk 2.0`* es a través de *scripts de shell*. Primero, debe iniciar *GRASS* con la `Location` y el `Mapset` deseados. No utilizaremos la *GUI* de GRASS, sino que trabajará en la terminal. El *prompt* de la línea de comandos debería comenzar con la versión de GRASS y, entre paréntesis, la *location*. Esto significa que realmente se encuentra dentro de GRASS. Ahora puede iniciar `r.randomwalk 2.0` escribiendo los argumentos directamente en la línea de comandos:
```
r.randomwalk -q -v prefix=test elevation=test_elev releasefile=test_releasefile.txt models=1,1,4,-9999,-9999 mparams=1,500,500,100,20,5,2
```
En este caso, `r.randomwalk 2.0` modelaría la trayectoria de uno o más deslizamientos de tierra definidos en un archivo de texto llamado `test_releasefile.txt` sobre el mapa de elevación llamado `test_elev`. Esto lo haría de acuerdo con las reglas definidas mediante las opciones `model` y `mparams`, para luego calcular un puntaje de indicador de impacto y producir un conjunto de diseños de mapa. Los resultados se recopilarían en una carpeta llamada `test_results`, que se almacenara en el directorio actual. El mapa ráster de entrada `test_elev` tendra que estar disponible como un ráster de GRASS en el *mapset* actual.

También es posible escribir el comando anterior en la terminal dentro de un archivo de texto ejecutable y, en su lugar, ejecutar este archivo. Aunque esto no parezca muy directo a primera vista, aumenta drásticamente nuestra flexibilidad: ahora podemos escribir varios comandos en el mismo script; por ejemplo, ejecutar `r.randomwalk` varias veces con diferentes datos de entrada.

Para la creación del *script*, inicialmente se recomienda crear una carpeta para cada proyecto en el que estemos trabajando. Así que, creemos la carpeta `projects/test` en nuestro directorio de inicio (*home directory*). Dentro de esta carpeta, creemos un archivo de texto con el nombre `test.randomwalk.start.sh`. El sufijo `.sh` significa *shell script* (script de shell), que es el tipo más común de archivo ejecutable en entornos LINUX. Ahora copiamos el texto que hemos ejecutado desde la línea de comandos en este script y guardamos los cambios. Luego, de vuelta en la terminal, debemos cambiarnos al directorio donde está almacenado el script y ejecutarlo escribiendo `sh` y su nombre:
```
sh test.randomwalk.start.sh
```
Los resultados del cálculo se guardan en el directorio `projects/test`.
