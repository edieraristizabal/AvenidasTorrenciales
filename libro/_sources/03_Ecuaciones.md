<p style="font-size:11px;"><em><strong>Créditos</strong>: El contenido de este capítulo ha sido tomado de varias fuentes, pero especialmente de Iverson & George {cite}`iverson_george_2024` en Advances in Debris-flow science and practice Eds. Matias Jakob, Scott McDougall, Paul Santi. (2024).</em></p>

# Ecuaciones de Saint-Venant y técnicas numéricas de solución

Para modelar flujos de escombros (*debris flows*) y otros flujos, se emplean un conjunto de ecuaciones de conservación que derivan de la mecánica de fluidos y medios continuos. Las ecuaciones de Saint-Venant son una forma simplificada y promediada en profundidad de las ecuaciones de Navier–Stokes. Se utilizan para modelar flujos superficiales como ríos, avalanchas, lahares y *debris flows*. Estas ecuaciones resuelven la dinámica del flujo considerando solo las variaciones en el plano horizontal (x,y), y promediando las variables a lo largo de la vertical (z), lo que simplifica mucho el problema sin perder lo esencial. Las ecuaciones de flujo de aguas someras (o de aguas poco profundas) resuelven simultáneamente las ecuaciones de conservación de masa y de momento para calcular la cota del agua y la velocidad. 

Las fuerzas de fricción entre el fluido y el contorno sólido son las principales fuerzas de resistencia en las ecuaciones hidráulicas estándar para agua clara newtoniana. Comparado con aguas limpias, los flujos de lodo y detritos generan fuerzas resistentes adicionales. El aumento del contenido de sólidos incrementa la viscosidad de los flujos no newtonianos, generando fuerzas resistentes internas dentro del fluido. A concentraciones más altas, particularmente con partículas gruesas, la colisión y fricción entre partículas introducen fuerzas resistentes internas adicionales. La mayoría de las modificaciones teóricas y numéricas implican la integración de las nuevas fuerzas internas del fluido en la ecuación de momento. 

La aplicación del transporte no newtoniano en un modelo de aguas someras (o de aguas poco profundas) requiere calcular las pérdidas internas añadiendo un término de pendiente a la pendiente de fricción ($S_f$) en la ecuación de momento y aumentando el flujo para tener en cuenta el volumen de los sólidos. La pendiente de fricción ($S_f$) del modelo de aguas someras representa las fuerzas que actúan contra el flujo en el contorno del fluido (por ejemplo, el canal), mientras que la pendiente de lodo y detritos ($S_{MD}$) representa las pérdidas internas debidas a la viscosidad, la turbulencia y/o la dispersión dentro del fluido.

Los términos utilizados en las ecuaciones de conservación de masa y momento de Saint-Venant para modelar flujos de escombros se traducen al español de la siguiente manera:

| **Término en inglés**               | **Símbolo**   | **Traducción al español**                            |
|--------------------------------------|---------------|------------------------------------------------------|
| Newtonian friction slope             | $𝑆_𝑓$         | Pendiente de fricción newtoniana                     |
| Mud and debris friction slope        | $𝑆_{𝑀D}$       | Pendiente de fricción para lodos y escombros         |


- **Pendiente de fricción newtoniana:** Representa la resistencia al flujo generada por la fricción basal y la viscosidad interna en materiales con comportamiento newtoniano, como el agua o fluidos con viscosidad constante. En hidráulica clásica, es análoga a la pendiente de energía perdida por fricción (e.g., fórmulas de Manning o Chezy).

- **Pendiente de fricción para lodos y escombros:** Representa la resistencia basal e interna característica de flujos no newtonianos, como mezclas de lodo y escombros, que pueden mostrar comportamiento viscoplástico (por ejemplo, tipo Bingham) o hiperconcentrado.

Generalmente los modelos entonces calculan un esfuerzo cortante no newtoniano basándose en la clasificación del flujo (p. ej., flujo de lodo, flujo de detritos, etc.) y el enfoque reológico apropiado (es decir, el modelo de esfuerzo-deformación). Luego, se integra el cortante viscoso, turbulento y de dispersión del modelo de esfuerzo-deformación en la ecuación de momento, convirtiendo el esfuerzo cortante en una pendiente ($𝑆_{𝑀D}$) y sumando esta pendiente a la pendiente de fricción ($S_f$). Adicionalmente, debido a que estos flujos pueden contener entre un 5% y un 70% de sólidos por volumen, un modelo de lecho fijo debe aumentar el volumen del flujo para tener en cuenta el impacto del sedimento en la masa y la profundidad del flujo.

En este sentido, los modelos numéricos para flujos de ladera suelen usar las ecuaciones de Saint-Venant adaptadas a flujos no newtonianos, en forma de conservación de masa y momento en 1D o 2D. A continuación, se detalla su estructura para flujo de detritos.

##### 1. Ecuación de conservación de masa (Continuidad)
Esta ecuación garantiza que la materia no se crea ni se destruye; solo se desplaza o cambia de volumen ("abultamiento" o *bulking*). En la modelización de procesos de ladera, describe cómo cambian el espesor (o nivel) y la masa de la mezcla a medida que desciende por un canal o ladera.

Para un flujo en una dimensión horizontal (1D), la ecuación de continuidad se puede presentar de esta forma (asumiendo ancho unitario):

$$\frac{\partial h}{\partial t} + \frac{\partial (uh)}{\partial x} = S_m$$

Desglose de términos:
- **Variación temporal de la profundidad ($\frac{\partial h}{\partial t}$):** Representa cómo sube o baja el nivel hidrodinámico ($h$) de la onda en un punto fijo a medida que pasa el tiempo.
- **Gradiente de flujo o de masa ($\frac{\partial (uh)}{\partial x}$):** Refleja el cambio espacial del caudal. Describe cómo el movimiento del fluido (con velocidad media $u$) transporta la masa a lo largo de la coordenada $x$. En 2D, se suma la contribución ortogonal ($v$) en dirección $y$: $\frac{\partial (uh)}{\partial x} + \frac{\partial (vh)}{\partial y}$.
- **Término fuente/sumidero ("Bulking") ($S_m$):** Es vital para flujos de lodo/detritos. Consiste en la incorporación de sedimentos del lecho (erosión local) o la pérdida de masa/agua, alterando significativamente el volumen total en tránsito.

##### 2. Ecuación de conservación de cantidad de movimiento (momento lineal)
Es el derivado de la Segunda Ley de Newton para un fluido superficial. Relaciona las aceleraciones tridimensionales del evento con las fuerzas que lo impulsan (como el plano de gravedad) contra las que lo frenan (el esfuerzo material y las reologías).

En 1D, su presentación analítica se formula como:

$$\frac{\partial (uh)}{\partial t} + \frac{\partial}{\partial x} \left( u^2h + \frac{1}{2}gh^2 \right) = gh(S_0 - S_f - S_{MD})$$

**A. Lado Izquierdo (Aceleraciones e Inercia)**
- **Aceleración local ($\frac{\partial (uh)}{\partial t}$):** El cambio neto de la cantidad de movimiento local únicamente por el avance del tiempo (muy crítico a la hora de procesar o suavizar el arribo del frente brusco en flujos no estacionarios).
- **Aceleración convectiva ($\frac{\partial (u^2h)}{\partial x}$):** Transporte de cantidad de movimiento por la traslación del fluido. Explica los cambios o cuellos de botella por variaciones en la sección o velocidad topográfica.
- **Gradiente de presión hidrostática ($\frac{\partial (\frac{1}{2}gh^2)}{\partial x}$):** Fuerza que actúa empujando aguas abajo generada en la propia dinámica del terreno, yendo desde las crestas transitorias engrosadas hacia porciones menos profundas.

**B. Lado Derecho (Esfuerzos propulsores y resistivos)**
Aquí es donde radica la precisión del modelo y sus diferencias sustanciales con aguas claras. Se utilizan convencionalmente tres pendientes ("Slope") bien diferenciables:
- **Pendiente topográfica o del lecho ($S_0$):** Equivale a $\sin \theta$. Es la fuerza netamente impulsora producida por el peso topográfico que proyecta la masa pendiente abajo.
- **Pendiente de fricción basal ($S_f$):** Representa la resistencia externa en el contorno del valle (suelo). Para un flujo newtoniano clásico, se usaría la hidráulica tradicional (como la fricción de pérdida Manning $S_f = \frac{n^2 u^2}{h^{4/3}}$).
- **Pendiente de lodo y detritos ($S_{MD}$):** Representa las fundamentales *pérdidas internas* del perfil del flujo (viscosidades de la suspensión de arcillas, colisiones macrogranulares y resistencias inerciales inelásticas).

Normalmente, los modelos aplican la regla de transformar analíticamente el esfuerzo interno reológico ($\tau_{MD}$) en su representativo "pérdida de pendiente":
$$S_{MD} = \frac{\tau_{MD}}{\rho_m g R}$$
donde $\rho_m$ es la densidad de la mezcla en rotación de sedimentos y agua ($kg/m^3$) y $R$ es el perímetro y radio hidráulico ($h$ de tirante principal puro en flujos lateralizados anchos).

---

El cálculo específico de $S_{MD}$ (o la adaptación de $S_f$) se determinará según el **modelo reológico** seleccionado. Una vez definido el tipo de flujo y el mecanismo de disipación de energía, cada comportamiento hidrodinámico particular (Bingham, O'Brien, Voellmy, etc.) tendrá su sub-traducción en esta pérdida de pendiente. Para un detalle riguroso de cada modelo reológico, sus parámetros y correspondencia matemática, consulte los capítulos [Tipos de flujos](04_TiposFlujos.md) y [Modelos reológicos](04_ModelosReologicos.md).

---

Existen a su vez simplificaciones para predicciones globales en tiempo rápido usadas analíticamente para proyecciones de alcances de depositación a nivel de diseño preliminar de infraestructuras (como el método del bloque de masas integradas o "box model" referenciado en RAMMS y correlativos del RMB). Suprimiendo de manera integral la hidrodinámica promediada de Saint-Venant por un cálculo netamente acelerativo 1D clásico a todo el volumen inercial movilizado de manera fija (sintetizado localmente en Fuerza Neta = masa ⋅ aceleración):

$$𝑚\frac{\partial\vec{u}}{\partial t} = 𝑚𝑔\sin\theta−\tau_b$$

Por esto, la simulacion computacional integral que permita comprender frentes bifásicos con engrosamiento y *bulking* o el flujo transversal adaptativo a los conos y los cambios abanicos del aluvión (que prevengan cuellos de botella no previstos), exigen modelos de cálculo con integradores sobre ambas ecuaciones conjuntas acopladas de masa y momento de Saint-Venant en alta iteración temporal.

#### Modelos numéricos de flujos de escombros: conservación de masa, momento y reología

| Modelo        | Conservación de masa                             | Conservación de momento                                     | Reología utilizada                                | Dimensiones | Comentarios principales                                                 |
|---------------|--------------------------------------------------|--------------------------------------------------------------|---------------------------------------------------|--------------|-------------------------------------------------------------------------|
| **HEC-RAS**   | $\displaystyle \frac{\partial h}{\partial t} + \frac{\partial (hu)}{\partial x} = S_m$ | $\displaystyle \frac{\partial (hu)}{\partial t} + \frac{\partial (hu^2)}{\partial x} = g h \sin\theta - \frac{\tau_b}{\rho}$ | Bingham, Herschel–Bulkley, lineal                 | 1D / 2D       | Hidráulica no newtoniana en cauces, incluye módulo sedimentológico     |
| **RAMMS::DF** | $\displaystyle \frac{\partial h}{\partial t} + \nabla \cdot (h \vec{u}) = 0$             | $\displaystyle \frac{\partial (h \vec{u})}{\partial t} + \nabla \cdot (h \vec{u} \otimes \vec{u}) = -g h \nabla z - \mu g h \cos\theta - \frac{\vec{u}^2}{\xi}$ | Voellmy–Salm                                      | 2D            | Simulación de alta resolución sobre DEM, calibración con runout        |
| **FLO-2D**    | $\displaystyle \frac{\partial h}{\partial t} + \nabla \cdot (h \vec{u}) = 0$             | $\displaystyle \frac{\partial (h \vec{u})}{\partial t} + \nabla \cdot (h \vec{u}^2) = g h \sin\theta - \tau_y - \mu \frac{u}{h} - k_d \left( \frac{u}{h} \right)^2$ | O’Brien–Julien (Bingham + dispersivo)             | 2D            | Permite erosión, deposición y flujos urbanos                           |
| **DAN3D**     | $\displaystyle \frac{\partial h}{\partial t} + \nabla \cdot (h \vec{u}) = 0$             | $\displaystyle \frac{\partial (h \vec{u})}{\partial t} + \nabla \cdot (h \vec{u} \otimes \vec{u}) = -g h \nabla z - \frac{\tau_b}{\rho}$ | Voellmy, Coulomb, viscoplástico                   | 3D            | Simula caída, transformación y flujo tridimensional                    |
| **r.avaflow** | $\displaystyle \frac{\partial h}{\partial t} + \nabla \cdot (h \vec{u}) = S_m$           | Ecuaciones de mezcla bifásica {cite}`pudasaini_hutter_2007`      | Mezcla sólido-líquido no newtoniana               | 2D (raster)   | Modelo acoplado sólido-fluido, erosión dinámica                        |
| **Flow-R**    | —                                                | $\displaystyle E_{\text{kin}} = E_{\text{pot}} - E_{\text{fricción}}$ | Fricción basal (ángulo de talud)                  | 2D (raster)   | Modelo empírico-geométrico para simulación de trayectoria y runout     |
| **Box models**| —                                                | $\displaystyle m \frac{du}{dt} = m g \sin\theta - \tau_b$     | Coulombiano                                        | 1D / simplificado | Estimación rápida de distancia de detención                           |
| **RMB**       | —                                                | $\displaystyle E_k + E_p = \text{constante} - \int \tau_b dx$ | Voellmy (simplificado energético)                 | 1D            | Balance energético para flujos sin cambio de masa                      |

## Técnicas numéricas de solución

El núcleo matemático de casi todos los modelos prácticos de flujo de escombros basados en física consiste en ecuaciones diferenciales parciales que representan leyes de conservación de masa y momento, promediadas en profundidad, de la mecánica del continuo para flujos que son poco profundos {cite}`iverson_george_2014`.  

Mientras que los modelos 3-D calculan flujos de momento en evolución en tres direcciones coordenadas, la mayoría de los modelos promediados en profundidad asumen que los flujos de momento normales al lecho pueden ser despreciados, lo que implica que las tensiones normales basales equilibran el peso estático del material suprayacente. 
Iverson {cite}`iverson_2005` proporcionó una evaluación cuantitativa de esta suposición y de las formas en que puede relajarse. 
Aunque la suposición de una tensión basal normal estática podría parecer muy restrictiva, la experiencia con modelos promediados en profundidad de diversos fenómenos, que van desde tsunamis e inundaciones hasta avalanchas granulares, ha mostrado que estos modelos comúnmente producen predicciones prácticas adecuadas incluso para flujos en los que la suposición se viola local o transitoriamente {cite}`george_augmented_2010,gray_tai_2003,leveque_clawpack_2011`.

Los modelos de flujo de escombros promediados en profundidad también asumen generalmente que las variaciones dependientes de la profundidad en el momento aguas abajo son despreciables.
La idea central del promediado en profundidad es simplificar la realidad 3D a un problema 2D. En lugar de calcular la velocidad y la densidad en cada punto de la columna de flujo, el modelo calcula un único valor promedio para la velocidad y la densidad en cada celda del mapa. 
sin embargo, el momento de un flujo (su "cantidad de movimiento") es una propiedad clave para saber cuán lejos llegará y con qué fuerza impactará. El momento no depende de la velocidad (v), sino del flujo de momento, que es proporcional a la velocidad al cuadrado ($v^2$).
El problema matemático es que el promedio de los cuadrados no es igual al cuadrado del promedio.
Esto significa que al tomar la velocidad promedio que calcula el modelo y la elevamos al cuadrado para estimar el flujo de momento se subestima su valor.
Como en los modelos de flujo de agua poco profunda, se pueden hacer correcciones para los efectos de estas variaciones introduciendo coeficientes de distribución del momento {cite}`vreugdenhil_swe_1994`. 
Sin embargo, los valores de los coeficientes de distribución dependen de la forma de los perfiles de velocidad y densidad del flujo, que están poco restringidos para los flujos de escombros. 
Como consecuencia, la mayoría de los modelos de flujo de escombros promediados en profundidad excluyen los coeficientes por considerarlos una complicación injustificada. 
En su lugar, asumen que el momento del flujo de escombros aguas abajo puede aproximarse como uniforme a todas las profundidades.

Las ecuaciones de conservación promediadas en profundidad se derivan integrando las ecuaciones de conservación 3-D a través del espesor del flujo de escombros en la dirección coordenada que denotamos como z {cite}`iverson_2005`. 
Sin embargo, la dirección de z no es la misma en todos los modelos. 
Las coordenadas curvilíneas adaptadas al terreno, con z rotado de modo que sea normal al lecho en todas partes, son las más rigurosas desde el punto de vista matemático y facilitan la consideración del efecto de las aceleraciones centrípetas sobre las tensiones basales {cite}`gray_granular_1999,denlinger_iverson_2004,savage_hutter_1991b`. 
Sin embargo, las coordenadas curvilíneas pueden ser difíciles de emplear en simulaciones numéricas de flujo sobre terrenos tridimensionales accidentados, en parte porque las celdas computacionales adyacentes pueden tener diferencias bruscas en las direcciones z normales al lecho, lo que podría generar definiciones conflictivas de las profundidades del flujo.
Además, si la geometría del lecho cambia significativamente debido a erosión o deposición, las coordenadas curvilíneas adaptadas al terreno original pierden al menos parte de su relevancia. 
El uso de coordenadas cartesianas centradas en la Tierra, con z vertical de manera uniforme, es más simple, pero requiere cuidado en el cálculo de flujos de momento y tensiones basales, porque la orientación de los lechos inclinados no es normal a una coordenada z vertical {cite}`denlinger_iverson_2004,iverson_2005,torresluquis_2019`.

#### Marco Euleriano o Lagrangiano

Los métodos numéricos estándar, como los métodos clásicos de diferencias finitas, son poco adecuados para estos problemas porque pueden producir soluciones físicamente espurias, dispersión numérica o inestabilidades numéricas. 
Estas deficiencias han motivado el desarrollo de clases especializadas de técnicas numéricas de solución, incluyendo los métodos de volúmenes finitos con captura de choques proporcionados por el proyecto de software de código abierto *Clawpack* {cite}`leveque_numerical_2002`.

Los flujos de escombros son fenómenos multiescala. El cuerpo principal del flujo puede tener cientos de metros de largo y moverse de forma relativamente uniforme. 
Pero el frente del flujo es una zona muy dinámica, con un borde muy definido, altas velocidades y gradientes de altura muy pronunciados. 
También puede haber ondas de choque o pulsos dentro del flujo. Si quisiéramos simular esto con precisión, necesitaríamos una resolución muy alta (celdas de menos de un metro) para capturar el frente, pero usar esa misma resolución para toda la cuenca sería computacionalmente carísimo, incluso imposible. 

El Refinamiento de Malla Adaptativo (AMR) {cite}`berger_oliger_1984` es una estrategia numérica diseñada precisamente para resolver este problema. 
En lugar de usar una única malla computacional con un tamaño de celda fijo para todo el dominio, AMR utiliza una jerarquía de mallas anidadas. 
Se empieza con una malla base gruesa que cubre toda el área. Luego, durante la simulación, el software identifica automáticamente las zonas "interesantes" (donde hay altos gradientes, como el frente del flujo) y coloca sobre ellas "parches" de mallas más finas. Estos parches se mueven y cambian de tamaño dinámicamente, siguiendo al flujo. 
Existen diversas implementaciones de AMR, pero el objetivo de todas las técnicas AMR es proporcionar resoluciones de malla óptimas para lograr precisión numérica y eficiencia computacional durante toda la simulación.

Las técnicas estándar de AMR desarrolladas para ecuaciones generales de conservación hiperbólica no son adecuadas para modelar flujos poco profundos promediados en profundidad que se mueven sobre topografía variable, porque estas técnicas no pueden preservar simultáneamente estados estacionarios balanceados (como estados estáticos) y conservar masa, momento y energía. 
Se desarrollaron técnicas AMR especializadas para superar este problema en el contexto de la modelación de tsunamis {cite}`george_leveque_2006,leveque_clawpack_2011`. 
Posteriormente, estas técnicas se extendieron a la modelación de flujos promediados en profundidad sobre topografía, como inundaciones superficiales {cite}`berger_debris_2011,george_augmented_2010` y flujos de escombros {cite}`george_iverson_2014`.

En el enfoque Euleriano (malla fija) el sistema de coordenadas o la malla computacional está fija en el espacio. El material (el flujo de escombros) se mueve a través de las celdas de esta malla fija. Es como una red de estaciones meteorológicas fijas en un mapa. Miden el viento y la lluvia a medida que las tormentas pasan por encima de ellas. Las estaciones no se mueven. El enfoque Euleriano es el que necesita AMR. Como la malla es fija, la única manera de tener alta resolución en unas zonas y baja en otras es refinando adaptativamente la malla. D-Claw es el ejemplo perfecto que da el texto: usa AMR para poner celdas de alta resolución solo en el frente del flujo, permitiéndole usar MDEs de muy alta calidad sin que el costo computacional sea prohibitivo. r.avaflow, por otro lado, usa un enfoque Euleriano pero con una malla uniforme (sin AMR), lo que lo hace computacionalmente más intensivo si se requiere alta resolución en todo el dominio.
Sin embargo, utiliza esquemas numéricos de solución de diferencias centrales no oscilatorias con disminución de la variación total (*TVD-NOC*, por sus siglas en inglés) {cite}`nessyahu_tadmor_1990`, que se han aplicado con éxito a problemas de flujos de masa en general {cite}`mergili_ravaflow_2017`.

Un método más distintivo para resolver el sistema hiperbólico de EDP en modelos promediados en profundidad de flujos de escombros es el enfoque lagrangiano sin malla conocido como hidrodinámica de partículas suavizadas (SPH, por sus siglas en inglés) {cite}`mcdougall_hungr_2004`. 
En el enfoque Lagrangiano (partículas móviles) no hay una malla fija. En su lugar, el modelo sigue a un conjunto de "partículas" o puntos computacionales que se mueven junto con el material. Es como soltar miles de boyas en un río. Para saber cómo se mueve el agua, sigues la trayectoria de cada boya. Las boyas se mueven con el flujo. 
El enfoque Lagrangiano es inherentemente adaptativo. 
La resolución espacial no la da una malla fija, sino la densidad de las partículas.
Donde el flujo se concentra o comprime, las partículas se juntan, y la resolución aumenta automáticamente.
Donde el flujo se expande, las partículas se separan, y la resolución disminuye.
Por esta razón, los métodos lagrangianos no necesitan AMR de la misma manera que los eulerianos. Su "malla" es el conjunto de partículas y ya es, por naturaleza, adaptativa.
A diferencia de los métodos eulerianos, los métodos lagrangianos formulan las EDP del modelo en un marco de referencia móvil que se traslada con el flujo. 
En las aplicaciones de SPH a flujos de escombros, la representación mecánica continua del material del flujo de escombros se reemplaza efectivamente por un conjunto de “partículas” columnares que interactúan y que abarcan el espesor del flujo a medida que se mueven aguas abajo {cite}`mcdougall_hungr_2004`. 
Además, SPH elimina los efectos de las discontinuidades en las soluciones de las EDP que gobiernan mediante el uso de una fórmula de suavizado para promediar la mecánica de interacción entre partículas vecinas.

:::{figure-md} euleriano o lagrangiano
<img src="https://i.pinimg.com/1200x/e2/38/d6/e238d69b696c047d37561b24f912ae79.jpg" width="700px">

Comparación esquemática entre los enfoques Euleriano (malla fija) y Lagrangiano (partículas móviles) para la simulación de flujos de escombros.
:::

#### Marcos de referencia en modelos eulerianos
Los modelos Eulerianos (r.avaflow, D-Claw, SHALTOP, TITAN2D, RAMMS) operan sobre una malla o grilla fija y observan cómo el material del flujo pasa a través de ella. La gran diferencia entre ellos radica en el sistema de coordenadas que usan para resolver las ecuaciones de la física.

*r.avaflow* resuelve las ecuaciones en coordenadas locales adaptadas al terreno. 
El modelo, en cada celda de la malla, "se para" sobre la pendiente y alinea su propio sistema de coordenadas con la topografía local. 
Su eje x siempre apunta en la dirección de máxima pendiente, su eje y es horizontal (a lo largo de la curva de nivel) y su eje z es perpendicular (normal) a la superficie del terreno.
Esto confiere ventajas en términos de fidelidad física. 
Porque la fuerza de la gravedad se descompone de forma natural y directa en dos componentes.
Pero puede presentar desafíos prácticos para modelar flujos sobre topografías complejas del mundo real. 
En general, es necesaria una transformación de las variables calculadas en coordenadas adaptadas al terreno a coordenadas cartesianas uniformes de MDE, y dicha transformación debe ser aproximada {cite}`mergili_ravaflow_2017`.
Esta transformación puede introducir pequeñas imprecisiones numéricas.

El Enfoque de Coordenadas Cartesianas Globales utilizado por D-Claw, SHALTOP, TITAN2D, RAMMS, trabaja directamente en el mismo sistema de coordenadas que el MDE (X, Y, Z). La malla computacional está fija y alineada, por ejemplo, con los ejes Norte-Sur y Este-Oeste.
Perto la física se vuelve más complicada de formular. 
La fuerza de la gravedad ahora debe descomponerse en componentes X, Y y Z, lo cual no es tan intuitivo para un flujo que se mueve sobre una superficie inclinada.
Estos esquemas deben ser bien balanceados para entender que en una ladera inclinada pero sin flujo (un lago en reposo, por ejemplo), la fuerza de la gravedad debe ser perfectamente balanceada por el gradiente de presión del agua, para que no se genere un movimiento artificial. D-Claw y SHALTOP han desarrollado esquemas numéricos muy avanzados para lograr este balance adecuado.

Otro enfoque para abordar la dificultad de modelar con precisión flujos poco profundos sobre topografía accidentada está implementado en el modelo *SHALTOP* {cite}`bouchut_model_2003,fernandeznieto_2008`, que resuelve las ecuaciones del modelo en coordenadas cartesianas globales pero corrige las ecuaciones para satisfacer el supuesto de flujo poco profundo aplicado en la dirección normal al lecho local. Este enfoque puede capturar aceleraciones normales al lecho debidas a la curvatura del terreno {cite}`peruzzetto_simplified_2021`, pero aún requiere que las variables normales al lecho se transformen a un sistema de referencia cartesiano representado por un MDE estándar. Al igual que D-Claw, *SHALTOP* utiliza esquemas numéricos de volúmenes finitos bien balanceados para resolver sistemas hiperbólicos de ecuaciones diferenciales {cite}`bouchut_2004`.

| Modelo | Marco Numérico | Característica Clave |
| :--- | :--- | :--- |
| **HEC-RAS** | Euleriano | Resuelve las ecuaciones sobre una malla o grilla fija. |
| **RAMMS::DF** | Euleriano | Resuelve las ecuaciones sobre una malla fija (el DEM). |
| **FLO-2D** | Euleriano | Resuelve las ecuaciones sobre una malla fija. |
| **DAN3D** | Lagrangiano | Generalmente implementado con SPH (Smoothed Particle Hydrodynamics), sigue partículas que se mueven con el flujo. |
| **r.avaflow** | Euleriano | Resuelve las ecuaciones sobre una grilla ráster fija. |
| **Flow-R** | Euleriano | Opera sobre una grilla ráster fija, calculando trayectorias de flujo desde cada celda. |
| **Box models** | Lagrangiano | Trata el deslizamiento como un único objeto (un "bloque") y sigue su centro de masa. |
| **RMB** | Lagrangiano | Realiza un balance de energía sobre la masa total a medida que se desplaza, siguiendo al objeto. |
| **Iber-NNF** | Euleriano | Volúmenes finitos (FVM) con solucionador de Riemann tipo Roe. |

#### El método de volúmenes finitos y los esquemas de captura de choques

Dentro de los esquemas Eulerianos, el **método de volúmenes finitos (FVM)** se ha convertido en el estándar para conservar estrictamente la masa y la cantidad de movimiento en flujos de montaña con discontinuidades (frentes secos, resaltos hidráulicos). Tanto *Iber* como *r.avaflow* emplean FVM {cite}`blade_iber_2014,mergili_ravaflow_2017`. En particular, *Iber-NNF* utiliza un solucionador de Riemann de tipo **Roe** (*upwind*), acoplado con reconstructores lineales **MUSCL** de segundo orden espacial y limitadores de flujo de variación total decreciente (**TVD**, como Minmod, Superbee o Van Leer) para propagar el flujo sobre lechos secos e irregulares de forma numéricamente estable y sin oscilaciones espurias. No obstante, estos esquemas de diferencias/volúmenes finitos siguen sujetos a cierta difusividad numérica en los frentes de flujo muy pronunciados.

#### Esquemas híbridos recientes: acoplamiento SPH-DEM y MPM

Más allá de la dicotomía estricta Euleriano/Lagrangiano, algunas herramientas recientes combinan ambos marcos para representar mejor la heterogeneidad de los flujos de detritos naturales:

- **Acoplamiento SPH-DEM**: combina la hidrodinámica de partículas suavizadas (SPH), eficiente para representar grandes deformaciones de la matriz fluidizada, con el método de elementos discretos (DEM), que resuelve el movimiento de bloques rocosos individuales (*boulders*) como cuerpos rígidos que interactúan por colisión dentro de la matriz. Este esquema acoplado permite representar simultáneamente el comportamiento continuo del lodo y el comportamiento granular discreto de los bloques más gruesos {cite}`trujillo_vela_sphdem_2020`.
- **Método del punto material (MPM) / FEM-MPM**: utilizado en herramientas avanzadas de dos fases como *OpenLISEM 2.0a*, discretiza el continuo del flujo en puntos materiales que se desplazan sobre una malla de fondo auxiliar, combinando la robustez del cálculo en malla (FEM) con la capacidad del enfoque Lagrangiano para manejar grandes deformaciones sin distorsión de la malla {cite}`trujillo_vela_2022`.
