
# DebrisFlow Predictor

*DebrisFlow Predictor* es un paquete de simulación de deslizamientos basado en agentes que modela el comportamiento complejo de flujos de detritos y avalanchas de detritos {cite}`guthrie_predictor_2021`. Fue desarrollado en lenguaje *C#* y *XAML*, y es un programa independiente con una interfaz de usuario completa que incluye la capacidad de importar archivos *shapefile* y un DEM (archivos ascii), y exportar datos a Excel o como Geotiffs y *shapefiles*.

### Componentes

Las áreas fuente pueden seleccionarse manualmente en el software y a menudo consisten en nueve agentes (15 m × 15 m) o más grandes. Alternativamente, las ubicaciones de inicio pueden seleccionarse aleatoriamente, de forma uniforme o manualmente (por escenario) e importarse como archivos de puntos en el software. A cada archivo de puntos se le puede asignar individualmente una ubicación de inicio de 15 m × 15 m y puede activarse o desactivarse para ejecutar diferentes escenarios.

Una vez iniciado, los agentes siguen un conjunto de reglas en cada paso de tiempo:

- **Erosionar y depositar:** los agentes consultan curvas de referencia que representan la probabilidad de que erosionen o depositen (de forma independiente) en la pendiente actual. Estas curvas se basan en un conjunto de datos empírico {cite}`guthrie_predictor_2021,guthrie_debris_2008`.
- **Pérdida de masa con cambios de dirección:** los agentes pierden masa con los cambios de dirección como un porcentaje del barrido neto (ponderado para que los cambios angulares mayores pierdan más masa).
- **Fusionar:** cuando un agente colisiona con otro agente durante un paso de tiempo, se fusionan en un solo agente y combinan sus masas.
- **Girar:** los agentes giran hacia las tres elevaciones más bajas y se mueven hacia el espacio desocupado más bajo con algunas reglas adicionales para escenarios ambiguos (sin espacios desocupados, elevaciones iguales, etc.).
- **Expandir:** los agentes se expanden según una función PDF:

$$
\sigma = \left(\left(\frac{m_{MAX} - m}{m_{MAX}}\right)^n \times \left((\sigma_L - \sigma_S) + \sigma_S\right)\right)
$$

donde: $m_{MAX}$ es la pendiente máxima del abanico, $m$ es la pendiente del DEM, $n$, $\sigma_L$ y $\sigma_S$ son los coeficientes de asimetría, pendiente baja y pendiente alta, respectivamente.
El comportamiento de expansión se controla mediante deslizadores dentro del programa y se calibra para las condiciones locales.

La propagación continua mientras algún agente tenga masa. 
Se crean nuevos agentes durante la función de expansión, y la propagación se detiene cuando todos los agentes terminan (masa = 0 para cada agente). 
Debido a que los resultados son estocásticos, generalmente se recomienda que el usuario realice múltiples ejecuciones por escenario.

### Requisitos de datos

*DebrisFlow Predictor* necesita, como mínimo, un DEM de 5 m importado como archivo ASCII y una ubicación de inicio del deslizamiento (esta última puede elegirse usando la herramienta dentro del programa). 
Otros autores han discutido las limitaciones del modelo con respecto al tamaño de celda del DEM {cite}`horton_flowr_2013`. 
**DebrisFlow Predictor** no está diseñado para DEM con resoluciones menores a 5 m. 
Para escalas regionales e incluso a escala de una sola cuenca, la resolución de 5 m proporciona un detalle considerable.

### Salidas

*DebrisFlow Predictor* produce resultados de cualquier combinación de ejecuciones únicas/múltiples y deslizamientos únicos/múltiples.
Cada píxel puede consultarse en el software para obtener información sobre la profundidad del depósito y el número del deslizamiento. 
Se proporcionan estadísticas básicas, incluyendo el número de veces que un píxel fue ocupado por un agente y las profundidades promedio, mínima y máxima del depósito a lo largo de todas las ejecuciones. 
La información específica de cada deslizamiento, incluyendo el área y volúmenes de deslizamientos individuales (para ejecuciones únicas), puede exportarse a Excel. 
Los escenarios de deslizamientos, los resultados de ejecuciones únicas o múltiples, pueden exportarse como *shapefiles*. 
Finalmente, los resultados también pueden exportarse como Geotiff para visualización en otros programas. 
El software no genera salidas de velocidad.

El usuario compara los resultados de las ejecuciones iniciales del modelo con deslizamientos observados o mapeados o con datos morfológicos proporcionados por el DEM. 
La evidencia de campo, la geología y la geomorfología, combinadas con el conocimiento experto de los procesos de flujos de detritos, son útiles para distinguir un resultado creíble de uno poco realista. 
La calibración también es posible mediante la comparación de relaciones magnitud-frecuencia y volumen-área con inventarios existentes de deslizamientos. 
Una vez calibrado, el modelo puede aplicarse a una región más amplia. 
Puede ser necesaria una recalibración si las condiciones locales resultan en un comportamiento de flujo diferente de una región a otra.

### Calibración y Evaluación

Al igual que otros modelos descritos en esta sección, la calibración de *DebrisFlow Predictor* se basa en el método de prueba y error. 
Se realiza de forma iterativa utilizando los controles dentro del programa.

*DebrisFlow Predictor* permite a los especialistas en geoamenazas modelar el alcance de flujos de detritos a escala regional y recopilar información importante sobre las características de los deslizamientos individuales dentro del dominio modelado. 
Esa información incluye la longitud del alcance, la probabilidad de inundación, y la profundidad de erosón y depósito a lo largo de las trayectorias del flujo de detritos.

*DebrisFlow Predictor* despliega agentes, programas subautónomos de 5 m por 5 m, sobre un modelo digital de elevación de la misma resolución. 
Cada agente aplica un conjunto de reglas físicas y empíricamente derivadas que describen su comportamiento en cada paso de tiempo basado en los datos de entrada proporcionados por el DEM y el vecindario de agentes en cada ejecución del modelo.
