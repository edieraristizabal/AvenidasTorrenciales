<p style="font-size:11px;"><em><strong>Créditos</strong>: El contenido de este capítulo se apoya en el manual de usuario de O'Brien {cite}`obrien_flo2d_2006` y en estudios comparativos recientes de aplicación de FLO-2D a flujos de detritos y avenidas torrenciales {cite}`srk_tailings_2022,hinzmann_2023_stsophia,pacheco_2025_esteroalfonso,lin_2011_songher`.</em></p>

# FLO-2D

**FLO-2D** es un modelo hidrodinámico de conservación de volumen, de código propietario, desarrollado originalmente para el tránsito de crecientes en llanuras de inundación y abanicos aluviales, y posteriormente extendido para la simulación de flujos hiperconcentrados, flujos de lodo y flujos de detritos mediante su componente de **flujo de detritos y lodo** (*Mudflow/Debris Flow component*, basado en el modelo reológico cuadrático de O'Brien y Julien {cite}`obrien_julien_1988`). Es una de las herramientas comerciales más utilizadas en la práctica profesional de ingeniería para la delimitación de amenaza por avenidas torrenciales, en buena parte gracias a su interfaz gráfica GDS (*Grid Developer System*) y a la amplia disponibilidad de literatura técnica y casos de calibración publicados.

### Componentes

FLO-2D resuelve las ecuaciones de aguas someras en su forma de **onda difusiva bidimensional** sobre una malla estructurada de celdas cuadradas, mediante un esquema de diferencias finitas centradas de ocho direcciones. A diferencia de un esquema de conservación completa de cantidad de movimiento, la componente de onda difusiva desprecia los términos de aceleración local y convectiva, lo que simplifica el cálculo pero limita su capacidad para representar apropiadamente los frentes de flujo muy rápidos o los resaltos hidráulicos pronunciados.

Para flujos hiperconcentrados y de detritos, FLO-2D incorpora el **modelo reológico cuadrático** de O'Brien y Julien {cite}`obrien_julien_1988`, que expresa el esfuerzo cortante total como la suma de un esfuerzo de fluencia (*yield stress*), un término viscoso lineal y un término turbulento-inercial cuadrático dependiente de la velocidad. Los coeficientes de fluencia y viscosidad se relacionan empíricamente con la concentración volumétrica de sedimentos ($C_v$) mediante ecuaciones exponenciales calibradas en laboratorio, lo que permite a FLO-2D **variar dinámicamente la concentración de sedimentos en el tiempo**, acoplada al hidrograma de entrada, una ventaja operativa exclusiva frente a otras herramientas que requieren una concentración constante durante toda la simulación {cite}`srk_tailings_2022`.

El módulo de canal (*channel routing*) permite combinar el tránsito en el cauce principal con el desbordamiento hacia la llanura de inundación, y dispone de componentes adicionales para simulación de rotura de presas y diques, y para obstrucción de puentes y alcantarillas.

### Requisitos de Datos

- Modelo digital de elevación (DEM) para construir la malla regular de celdas (típicamente de 5 a 30 m de lado, según la resolución disponible y la escala del análisis).
- Coeficiente de rugosidad de Manning ($n$) para el componente de agua clara.
- Parámetros reológicos del modelo cuadrático: esfuerzo de fluencia ($\tau_y$), viscosidad dinámica o plástica ($\eta$), y los coeficientes empíricos que los relacionan con $C_v$, o bien valores de laboratorio específicos del sitio si están disponibles.
- Hidrograma de entrada de caudal líquido y, opcionalmente, un hidrograma de concentración de sedimentos variable en el tiempo.
- Gravedad específica de los sólidos, necesaria para el balance de masa de la mezcla.

### Salidas

FLO-2D exporta como resultado principal rásteres o grillas de profundidad máxima de flujo, velocidad máxima y tiempo de llegada del frente para cada celda del dominio. También permite generar secciones transversales del canal e hidrogramas de salida en puntos de control. La visualización de resultados se realiza mediante el módulo **Mapper**, que, a diferencia de RAS Mapper de HEC-RAS, restringe la visualización a un único plano de simulación a la vez, dificultando la comparación directa entre distintos escenarios {cite}`srk_tailings_2022`.

### Calibración y Evaluación

La calibración de FLO-2D se realiza típicamente ajustando los parámetros reológicos y el coeficiente de Manning para reproducir la extensión, profundidad y volumen de depósito observados en campo. Estudios recientes documentan su desempeño en distintos contextos:

- **Cuenca de Song-Her, Taiwán**: Lin, Lee y Chang {cite}`lin_2011_songher` calibraron FLO-2D para un flujo de detritos real con un esfuerzo de fluencia de 2500 Pa, viscosidad plástica de 10 Pa·s y coeficiente de Manning de 0,0312, logrando reproducir el espesor máximo de depósito, el área de deposición y el volumen final con diferencias inferiores al 6 % respecto a las mediciones de campo.
- **Rotura de presas de relaves**: SRK Consulting {cite}`srk_tailings_2022` encontró que, al estar restringido a la ecuación de onda difusiva, FLO-2D completa simulaciones considerablemente más rápido que HEC-RAS (0,33 h frente a 1,15–4,58 h en un escenario de 24 h de tránsito), aunque a costa de una malla estructurada menos flexible para representar bermas y diques con precisión.
- **Flujo de detritos de St. Sophia, California (2003)**: Hinzmann {cite}`hinzmann_2023_stsophia` encontró que FLO-2D sobreestimó marcadamente las profundidades y velocidades de flujo frente a la evidencia de campo en este evento particular, en contraste con HEC-RAS-MDF (véase el capítulo de [HEC-RAS](18_HECRAS.md)).
- **Aluvión del Estero San Alfonso, Chile (2017)**: Pacheco, Martínez y Cuevas {cite}`pacheco_2025_esteroalfonso` reconstruyeron este evento con FLO-2D y HEC-RAS 2D, obteniendo velocidades consistentes entre ambos modelos (diferencias menores al 10 %), aunque FLO-2D estimó profundidades de flujo menores que HEC-RAS, especialmente en la sección de salida aguas abajo.

En conjunto, estos casos sugieren que FLO-2D ofrece un buen equilibrio entre costo computacional y desempeño para flujos de detritos moderadamente rápidos con buena disponibilidad de datos de calibración de laboratorio, pero puede requerir una validación cuidadosa cuando se aplica fuera del rango de condiciones para el que fue calibrado su modelo reológico cuadrático.
