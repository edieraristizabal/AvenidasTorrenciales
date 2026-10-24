# r.avaflow

### Componentes del modelo

r.avaflow es un marco de simulación de código abierto, robusto y multifuncional, desarrollado sobre la base de un Sistema de Información Geográfica (SIG). Su propósito es modelar flujos de masa y, de manera crucial, las cadenas de procesos que estos pueden desencadenar. Su desarrollo inicial se fundamentó en el modelo bifásico de flujo de masa de Pudasaini {cite}`pudasaini_twophase_2012`, que consideraba una fase sólida y una fase fluida interactuando entre sí. Reconociendo la complejidad de muchos flujos de detritos reales, el modelo evolucionó hacia un sistema trifásico (sólido, sólido-fino y fluido), como se detalla en Pudasaini y Mergili {cite}`pudasaini_mergili_2019`, permitiendo una representación más matizada de la reología de la mezcla. La fase sólida se caracteriza por fricción interna y basal, la fase sólida-fina adicionalmente por viscosidad y esfuerzo de cedencia, y la fase fluida por viscosidad y esfuerzo de cedencia. Las interacciones entre las fases se consideran a través de efectos de flotabilidad, masa virtual y arrastre. Un modelo de mezcla tipo Voellmy está disponible como alternativa. Las ecuaciones de balance de masa y momento en Pudasaini y Mergili {cite}`pudasaini_mergili_2019` son promediadas en la profundidad, y se considera que las fases están mezcladas a lo largo de la altura del flujo, aunque la distribución vertical de la concentración de las fases individuales puede ser controlada. Se utiliza un esquema de diferencias centrales TVD-NOC en una cuadrícula escalonada (*staggered grid*) para el transporte de masa y momento {cite}`tai_kurganov_2002`. La cuadrícula se desplaza media celda en cada paso de tiempo {cite}`mergili_ravaflow_2017`.

Típicamente, la masa de liberación se define a través de mapas ráster que muestran las alturas de liberación para cada celda y cada fase, impuestas sobre un modelo digital del terreno. También se pueden utilizar hidrogramas (no solo para agua, sino para todos los tipos de material considerados en r.avaflow) para introducir masa y momento en el sistema. Las funcionalidades adicionales incluyen la incorporación de material (entrainment), la detención, el movimiento dispersivo no hidrostático, la variación espaciotemporal de varios parámetros y controles, y la evaluación empírica de los resultados. 

La versión más reciente, r.avaflow v4, representa un cambio de paradigma en la flexibilidad del modelo. Abandona la asignación rígida de reologías a fases predefinidas y, en su lugar, introduce un enfoque modular. El comportamiento de cada una de las hasta tres fases se define dinámicamente mediante la activación, desactivación y parametrización de diferentes términos de fuerza dentro de una ecuación de gobierno del momento unificada. Esta ecuación general es:

$$F_{t}+F_{x}+F_{y}=\alpha\cdot h\cdot(GA+DF+DR-EC-TF-FF-AD)$$

donde $F_{t}+F_{x}+F_{y}$ representa el cambio total del momento (la cantidad de movimiento) de una porción del flujo. $F_{t}$ es el cambio del momento en el tiempo (la derivada temporal). $F_{x}$ y $F_{y}$ son los flujos de momento en las direcciones x e y, respectivamente. Describen cómo el momento se transporta de un lugar a otro. En esencia, este lado izquierdo de la ecuación representa la inercia del flujo, es decir, cómo su movimiento cambia en el tiempo y en el espacio.

Por el oro lado, los términos de fuerza $\alpha$ es la fracción de volumen de la fase que se está analizando (sólida, sólida-fina o fluida) dentro de la mezcla. Este factor es clave porque asegura que cada fuerza se aplique solo a la porción correspondiente del flujo; y $h$ es la altura total del flujo. Multiplicar por $α⋅h$ convierte las fuerzas (que son aceleraciones) en una fuerza total por unidad de área para esa fase específica. Las fuerzas dentro del paréntesis que son positivas aceleran el flujo y las negativas lo frenan.

* GA (*Gravitational Acceleration* - **Aceleración Gravitacional**): Esta es la fuerza impulsora principal. Es la componente de la gravedad que actúa paralela a la pendiente, empujando la masa hacia abajo. Sin esta fuerza, no hay movimiento.

* DF (*Internal Deformation* - **Deformación Interna**): Representa los gradientes de presión interna dentro de la masa. Físicamente, es la fuerza que hace que un montón de material se esparza o se deforme por su propio peso. Para la fase sólida, se relaciona con el empuje de tierra (coeficiente de presión de tierra, K), mientras que para la fase fluida, se relaciona con la presión hidrostática.

* DR (*Drag* - **Arrastre**): Esta es la fricción interna entre las fases. Cuando una fase (por ejemplo, el agua) se mueve más rápido que otra (los sólidos), ejerce una fuerza de arrastre sobre la más lenta. Es una de las interacciones más importantes en los flujos bifásicos y es fundamental para modelar la mezcla y separación de los componentes.

* EC (*Extended Coulomb* - **Coulomb Extendido**): Esta es la principal fuerza de resistencia friccional en la base del flujo. Se basa en la ley de fricción de Coulomb, que establece que la resistencia es proporcional a la presión normal que ejerce la masa sobre el lecho. Este término también incluye la cohesión del material, que es una resistencia que no depende de la presión.

* TF (*Turbulent Friction* - **Fricción Turbulenta**): Esta es otra fuerza de resistencia basal. Se usa para modelar la disipación de energía debida a la turbulencia, especialmente en flujos rápidos y fluidos. Cuando se combina con el término de Coulomb (EC), permite simular una reología tipo Voellmy, muy utilizada en el modelado de avalanchas.

* FF (*Fluid Friction* - **Fricción del Fluido**): Es la resistencia en la base que afecta específicamente a la fase fluida. Se modela comúnmente con la ecuación de Manning, que relaciona la fricción con la rugosidad del lecho y la velocidad del flujo.

* AD (*Ambient Drag* - **Arrastre Ambiental**): Representa la resistencia que el fluido circundante (aire o agua) ejerce sobre la superficie del flujo. Aunque suele ser pequeña para flujos de detritos subaéreos, puede ser muy significativa si el deslizamiento se mueve a través de un cuerpo de agua (como un lago o el mar).

En resumen, el lado derecho de la ecuación es un balance de fuerzas: la gravedad y la deformación interna intentan acelerar el flujo, mientras que las distintas formas de fricción (en la base, entre fases, con el aire) se oponen al movimiento. La dinámica resultante que observamos en la simulación es el resultado neto de esta compleja interacción.

Una característica inteligente del software es su capacidad para decidir automáticamente si un material se comporta como un sólido (si su ángulo de fricción interna $\varphi > 0$) o como un fluido, simplificando la configuración para el usuario. Esta arquitectura modular es la base para las nuevas y potentes funcionalidades de r.avaflow v4, como el Modelo por capas (*Layered Model*), el Control de deformación (*Sliding Model*) y el Modelo de flujo lento (*Slow-flow Model*).

La solución numérica de las ecuaciones se realiza mediante un esquema de diferencias finitas TVD-NOC (Total Variation Diminishing - Non-Oscillatory Central) sobre una cuadrícula escalonada (*staggered grid*).

### Física y Reología del Modelo

La principal fortaleza de r.avaflow reside en su robusto fundamento físico, basado en la teoría de flujos multifásicos. A diferencia de los modelos monofásicos que tratan el flujo de detritos como un único material "equivalente", r.avaflow modela explícitamente las distintas fases y, lo que es más importante, las complejas interacciones entre ellas.

El núcleo original de r.avaflow se basa en el modelo general bifásico de Pudasaini {cite}`pudasaini_twophase_2012`. Este modelo fue pionero al incluir de forma unificada varios fenómenos físicos esenciales:
* **Fase Sólida:** Tratada como un material plástico regido por el criterio de Mohr-Coulomb.
* **Fase Fluida:** Modelada como un fluido viscoso no newtoniano.
* **Interacciones:** Se incluyeron de forma explícita la fuerza de arrastre (*drag*), la flotabilidad (*buoyancy*) y la masa virtual (*virtual mass*).

Este enfoque bifásico ya permitía simular la separación de fases y la dinámica interna de la mezcla de una manera mucho más realista. Sin embargo, los flujos de detritos naturales a menudo contienen tres componentes reológicamente distintos: cantos y gravas (sólidos), arenas y limos (que pueden tener un comportamiento intermedio) y una matriz de agua y arcillas (fluido viscoso).

Para abordar esta complejidad, Pudasaini y Mergili {cite}`pudasaini_mergili_2019` extendieron el marco a un **modelo multifásico** que puede manejar hasta tres fases, cada una con su propia reología y propiedades:

1.  **Fase Sólida (s):** Representa los componentes más gruesos (bloques, cantos, gravas). Se rige por una reología **plástica de Mohr-Coulomb**, donde la resistencia depende de la fricción y no de la tasa de deformación. Es un material puramente friccional.

2.  **Fase Sólida-Fina (fs):** Representa las partículas de tamaño intermedio como las arenas. Esta fase se modela con una reología **Coulomb-viscoplástica**, dependiente tanto de la presión como de la tasa de cizalla. Esto significa que tiene un componente de resistencia friccional (como la fase sólida) y un componente de resistencia viscosa (como la fase fluida). Su esfuerzo de cedencia depende de la presión y de la fricción del material.

3.  **Fase Fluida (f):** Representa la matriz de agua con partículas muy finas en suspensión (arcillas, limos). Se trata como un fluido **viscoplástico**, cuya resistencia depende principalmente de la viscosidad y de un posible esfuerzo de cedencia (modelo de Bingham o Herschel-Bulkley).

#### Interacciones Clave entre Fases

El realismo del modelo no proviene solo de la reología de cada fase, sino de cómo interactúan. r.avaflow incorpora tres interacciones fundamentales:

* **Flotabilidad (*Buoyancy*):** La fase fluida (y la mezcla de fluido y sólidos finos) ejerce una fuerza de flotación sobre las partículas más grandes, reduciendo la presión efectiva y, por tanto, la resistencia friccional en la base. Esto se modela reduciendo la carga normal de las fases sólida y sólida-fina en función de la densidad de las fases que las rodean. Este efecto es crucial para explicar la alta movilidad de muchos flujos de detritos.

* **Fuerza de Arrastre (*Drag*):** Cuando existe una velocidad relativa entre las fases, la fase más rápida "arrastra" a la más lenta. r.avaflow utiliza una formulación de arrastre generalizado que es válida para un amplio rango de concentraciones, desde flujos diluidos (donde las partículas se mueven a través del fluido) hasta flujos densos (donde el fluido se mueve a través del esqueleto de granos). El coeficiente de arrastre no es una constante, sino que depende de las propiedades físicas como las fracciones de volumen, las densidades, el diámetro de las partículas y la viscosidad del fluido.

* **Masa Virtual (*Virtual Mass*):** Este es un concepto más sutil pero físicamente importante. Cuando una fase acelera con respecto a otra, no solo debe mover su propia masa, sino también una parte del fluido circundante que es "empujado". Este efecto de inercia añadido se conoce como masa virtual. Es particularmente relevante en situaciones de rápida aceleración o desaceleración, como el impacto de un deslizamiento en un embalse, y acopla fuertemente las ecuaciones de momento de las diferentes fases.

Además de estas interacciones, el modelo de Pudasaini {cite}`pudasaini_twophase_2012` introduce un esfuerzo viscoso no newtoniano mejorado, donde la viscosidad aparente del fluido se ve afectada por el gradiente de concentración de sólidos. Esto significa que la resistencia viscosa cambia si el flujo se está volviendo más o menos concentrado en la dirección del movimiento, un fenómeno observado en flujos de alta concentración.

### Datos

La siguiente tabla resume los parámetros de entrada más importantes que el usuario debe definir para controlar la reología y el comportamiento del flujo en r.avaflow. Se distinguen los parámetros reológicos clásicos de aquellos asociados a las nuevas funcionalidades de la versión v4.

| **Componente del modelo** | **Parámetro** | **Símbolo** | **Unidad** |
| :--- | :--- | :--- | :--- |
| Reología / Flujo | Ángulo de fricción interna | $\varphi$ | ° |
| | Ángulo de fricción basal | $\delta$ | ° |
| | Cohesión | $c$ | Pa |
| | Número de fricción turbulenta (Voellmy) | $\zeta$ | m/s² |
| | Coeficiente de Manning | $n$ | s/m^(1/3) |
| | Coeficiente de arrastre entre fases | $c_{DRAG}$ | – |
| | Densidad del material | $\rho$ | kg/m³ |
| | Altura inicial de la fase | $h$ | m |
| | Coeficiente de entrainment | $C_E$ | kg⁻¹ |
| Modelo de Deslizamiento | Factor de deformación (bruto) | $f_{d}^{*}$ | – |
| | Fracción de deslizamiento global | $f_{g}$ | – |
| Modelo de Flujo Lento | Viscosidad cinemática | $\nu$ | m²/s |
| | Término de viscosidad de la capa basal | $\xi$ | 1/s |

El pilar fundamental para cualquier simulación en r.avaflow es un DEM. La calidad y resolución de este MDE son críticas, ya que determinan directamente la precisión de las pendientes, las rutas de flujo y, en consecuencia, las velocidades y la extensión final del depósito. La masa inicial del deslizamiento se define típicamente mediante mapas ráster que especifican la altura de liberación para cada una de las fases en cada celda. Alternativamente, para simular la entrada de flujo desde un punto, como la ruptura de una presa, se pueden utilizar hidrogramas.

Los parámetros de los materiales, que definen su comportamiento reológico (ángulos de fricción, cohesión, viscosidad, densidad), deben ser definidos por el usuario basándose en datos de campo, ensayos de laboratorio o literatura de casos análogos. Las nuevas funcionalidades requieren parámetros específicos, como el factor de deformación para el *Sliding Model* o los parámetros de viscosidad para el *Slow-flow Model*.

### Salidas
r.avaflow está diseñado para proporcionar una rica variedad de salidas que facilitan un análisis exhaustivo de los resultados de la simulación:

* **Salidas dinámicas:** El resultado principal son series temporales de mapas ráster (en formato ASCII o GeoTIFF) que muestran la evolución de variables clave como la altura de flujo, la velocidad, la presión dinámica y la energía cinética. Estos mapas se generan para cada fase, permitiendo un análisis detallado de la dinámica interna del flujo.
* **Archivos de resumen:** Se generan archivos de texto que contienen un resumen global de la simulación, incluyendo balances de masa y otros datos agregados.
* **Gráficos y animaciones:** El software tiene la capacidad de generar automáticamente gráficos y animaciones básicas del proceso, útiles para una primera evaluación rápida de los resultados.
* **Interfaz para 3D y Realidad Virtual (RV):** Una de las innovaciones más destacadas de la v4 es su capacidad para tender un puente hacia herramientas de visualización avanzadas. Genera automáticamente series temporales de archivos CSV y scripts de Python que facilitan la importación de los resultados en software como **Paraview**, **Blender 3** y el motor de juegos **Unreal Engine 5**. Esta funcionalidad abre la puerta a la creación de productos de comunicación de alto impacto, desde videos 3D y anaglifos (para ver con gafas rojo-cian) hasta experiencias de realidad virtual totalmente inmersivas, donde el usuario puede "caminar" por el escenario e incluso interactuar con él, ofreciendo un potencial educativo.

### Calibración y Evaluación
La calibración de los parámetros es un desafío significativo, especialmente para cadenas de procesos complejas. El enfoque estándar es un procedimiento manual de prueba y error. En este proceso, el modelador ajusta iterativamente los parámetros de entrada (como los ángulos de fricción o la viscosidad) y compara los resultados de la simulación (la extensión y altura del depósito, las velocidades máximas, etc.) con observaciones de eventos reales bien documentados.

Este proceso manual es intensivo y se enfrenta al problema de la equifinalidad, un concepto bien conocido en modelización ambiental donde diferentes combinaciones de parámetros pueden producir resultados finales muy similares. Esto hace que sea difícil identificar un único conjunto de parámetros "correctos" sin una cantidad suficiente de datos de campo de alta calidad para la validación.

Ya desde la versión 1, r.avaflow incluyó herramientas para facilitar este proceso, como la capacidad de ejecutar múltiples simulaciones en paralelo variando los parámetros de forma controlada. Esto permite realizar análisis de sensibilidad y optimización de manera más sistemática. La v1 también introdujo métricas de validación incorporadas, como el Índice de Éxito Crítico (CSI) y el Área Bajo la Curva ROC (AUROC), para comparar cuantitativamente los resultados simulados con las áreas de impacto observadas. Aunque el proceso sigue siendo un desafío, estas herramientas proporcionan un marco robusto para la evaluación del modelo.
