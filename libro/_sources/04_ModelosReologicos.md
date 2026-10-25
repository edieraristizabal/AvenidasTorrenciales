<p style="font-size:11px;"><em><strong>Créditos</strong>: El contenido de este capítulo ha sido adaptado del [HEC-RAS Non-Newtonian / Mud and Debris Flow User's Manual](https://www.hec.usace.army.mil/confluence/rasdocs/rasmuddebris/non-newtonian-user-s-manual/introduction-taxonomy-and-rheology-of-debris-flows) y de la literatura clásica de reología de flujos de escombros (Bagnold, O'Brien y Julien, Coussot, entre otros).</em></p>

# Modelos reológicos
Modelar la dinámica de flujos de detritos y lahares es un paso crítico en la evaluación de amenazas, el manejo del riesgo y el diseño de obras de mitigación. Sin embargo, dada la complejidad de estos materiales de dos fases (sólido-líquido), no existe un modelo único que describa perfectamente todos los regímenes de flujo. Los enfoques modernos emplean herramientas numéricas promediadas en profundidad, donde la resistencia al flujo se incorpora como términos friccionales a través de **modelos reológicos** que tratan a la mezcla como un "fluido equivalente" para capturar su comportamiento macroscópico general.

Estos modelos, aunque simplificados, permiten integrar la física dominante (como cohesión plástica, fricción de Coulomb o turbulencia). El desafío actual en la modelación radica en la incertidumbre intrínseca de los parámetros (como el coeficiente de fricción de Coulomb $\mu$, viscosidad dinámica $\mu_B$, límite de cedencia $\tau_y$, coeficiente de turbulencia $\xi$ y esfuerzos granulares), la escasez de datos de calibración histórica y las variabilidades de una topografía compleja. La elección apropiada del modelo determina si el fluido podrá simular procesos de detención en pendientes suaves o continuará fluyendo indefinidamente. A continuación se presentan los principales modelos reológicos aplicables a flujos de escombros, junto con sus fundamentos y el consiguiente mapeo de la *pendiente por pérdida de lodo y detritos ($S_{MD}$ o $S_f$)*.

:::{figure-md} rheology
<img src="https://www.mdpi.com/symmetry/symmetry-10-00094/article_deploy/html/images/symmetry-10-00094-g001.png" alt="rheology" width="500px">

Reologia de flujos. Tomado de [Wang et al. (2018)](https://www.mdpi.com/2073-8994/10/4/94/htm).
:::

Un rasgo distintivo de los flujos de escombros es su comportamiento viscoplástico y altamente no lineal. 
A concentraciones volumétricas de sedimento elevadas (por ejemplo, >40–50% de sólidos), la mezcla presenta un esfuerzo de fluencia (o esfuerzo de cedencia) por debajo del cual el material se comporta prácticamente como un sólido estático, y por encima del cual fluye rápidamente como un fluido pesado. 
Esto se atribuye a una red interconectada de partículas finas (arcillas, limos) y el entrelazamiento de granos que confiere una resistencia cohesiva al inicio del movimiento. 

Asimismo, una vez iniciada la fluencia, la relación entre el esfuerzo cortante ($\tau$) y la tasa de deformación ($\dot\gamma$) puede no ser constante: muchos flujos presentan comportamiento pseudoplástico (shear-thinning), donde la viscosidad aparente disminuye a mayores tasas de corte (debido a la alineación de partículas o rotura de estructuras interparticulares), mientras que otros podrían mostrar comportamiento dilatante (shear-thickening) si la agitación genera más choques entre granos a alta velocidad. 

Además, en flujos rápidos y de gran escala, pueden coexistir zonas laminares (en el núcleo más denso, de deformación lenta) y zonas turbulentas o dispersivas (en los bordes o en la cabeza del flujo, donde los granos chocan violentamente y el fluido se agita). El dominio reológico de un flujo de escombros depende en gran medida de su composición y del régimen de movimiento: por ejemplo, en flujos muy ricos en finos cohesivos (típicamente asociados a ambientes tropicales con suelos arcillosos o erupciones volcánicas que generan lahares), la cohesión interna domina y el material se asemeja a un lodo espeso con un claro umbral de fluencia, bien representado por modelos viscoplásticos como Bingham o Herschel-Bulkley. 
En cambio, en flujos con alto contenido de granos gruesos (típicos de cuencas montañosas empinadas con depósitos detríticos y bloques), la fricción granular y los choques entre partículas pueden predominar, pudiendo modelarse con esquemas de fricción tipo Coulomb o con términos turbulentos cuadráticos (e.g. modelo de Voellmy o modelos “dispersivos” de tipo Bagnold). 
Muchos flujos naturales ocupan un punto intermedio de este espectro, por lo que algunos enfoques combinan múltiples contribuciones reológicas (p. ej., el modelo “cuadrático” de O’Brien & Julien incluye simultáneamente un término cohesivo, uno viscoso lineal y uno cuadrático dispersivo). 

:::{figure-md} flow taxonomy
<img src="https://i.pinimg.com/736x/b0/85/c7/b085c7b4549b1401c87c3d79752032d5.jpg" alt="Flow axonomy" width="700px">

Taxonomía de flujos no Newtonianos con los modelos reológicos y ecuaciones utilizadas para modelar según Gibson et al., 2020. Tomado de [HEC RAS Non-Newtonian User's Manual](https://www.hec.usace.army.mil/confluence/rasdocs/rasmuddebris/non-newtonian-user-s-manual/introduction-taxonomy-and-rheology-of-debris-flows).
:::

La reología de los flujos de escombros es un campo complejo que integra conceptos de mecánica de fluidos no newtonianos, mecánica de suelos y dinámica de granulares. En contextos montañosos y tropicales, entender y modelar esta reología es crucial para predecir el comportamiento de estos flujos destructivos. Los modelos – Bingham, Herschel–Bulkley, Voellmy–Salm, entre otros – proporcionan marcos teóricos simplificados para representar distintos aspectos del flujo: cohesión de finos y viscosidad laminar en Bingham/HB, no linealidad pseudoplástica en HB, fricción basal y turbulencia en Voellmy, e interacciones granulares dispersivas en el modelo cuadrático de O’Brien. Cada modelo tiene fundamentos físicos bien establecidos (p. ej., $\tau_y$ ligado a la estructura de partículas, término $V^2$ ligado a colisiones) pero también supuestos limitantes (homogeneidad, parámetros constantes, ausencia de erosión). Por ello, no existe un modelo universal; la elección debe adaptarse al tipo de flujo y a los objetivos del análisis. 

A continuación se detalla cada modelo principal, con sus ecuaciones constitutivas, supuestos y aplicabilidad.

### Modelo de Bingham
El modelo de fluido de Bingham {cite}`coussot_ancey_2000` es uno de los más sencillos y empleados para describir flujos de detritos con alto contenido de finos. Se trata de un modelo viscoplástico: asume que el material posee un esfuerzo de fluencia $\tau_y$ (también llamado tensión umbral o cedente) que debe superarse para que ocurra deformación continua, y una vez superado ese umbral, el material fluye con un comportamiento plástico perfecto caracterizado por una viscosidad plástica aproximadamente constante $\mu_p$. Matemáticamente, la relación esfuerzo-deformación cortante se expresa como:

$\tau = \tau_y + \mu_p \dot\gamma$  

En esta formulación lineal con intercepto, $\tau_y$ representa el valor de esfuerzo cortante en el cual comienza el flujo (intercepto no nulo), mientras que $\mu_p$ es la pendiente de la relación tensión vs. tasa de corte, es decir, la viscosidad (constante) de la mezcla una vez movilizada. Por debajo de $\tau_y$, el material se comporta como un sólido rígido (no hay deformación irreversible), y por encima se comporta como un fluido newtoniano de alta viscosidad.

**Traducción a pérdida reológica (Ecuación de Momento):** 
En los códigos numéricos se divide inercialmente en estática y dinámica sumando los términos: $S_{MD} = S_{yield} + S_{viscous}$
* **$S_{yield}$ (Término de Cedencia):** Equivale a $\frac{\tau_y}{\rho_m g h}$. Es el límite de estática. Si el empuje base de la pendiente no excede este valor constante, el flujo se parará automáticamente. Depende directamente de la matriz de cohesión.
* **$S_{viscous}$ (Término Viscoso):** El rozamiento microscópico puramente intergranular, que crece de la mano a la velocidad relativa local de forma lineal. Un ejemplo cotidiano de fluido tipo Bingham es la mayonesa, que no fluye hasta que la tensión aplicada excede cierto valor, tras lo cual se comporta como un líquido espeso. 

La ecuación de Bingham se aplica a menudo a flujos hiperconcentrados y flujos de lodo. En teoría, estos flujos de menor concentración se ajustan mejor al modelo lineal. Sin embargo, su formulación relativamente simple hace que sea más fácil de calibrar. Menos parámetros libres lo hacen menos vulnerable a problemas de equifinalidad. Por lo tanto, se ha aplicado con éxito a flujos de detritos de mayor concentración en aplicaciones de laboratorio y de campo.

El modelo de Bingham solo requiere dos datos de entrada por parte del usuario: el esfuerzo de cedencia (el intercepto de la relación esfuerzo-deformación) y la viscosidad del flujo cargado de sedimento (la pendiente de la relación esfuerzo-deformación). en el modelo HEC RAS Las opciones para estos se describen en las secciones de Esfuerzo de Cedencia y Viscosidad de la siguiente forma.

:::{figure-md} Bingham model
<img src="https://i.pinimg.com/736x/00/a8/97/00a897f9269263100dd73a4b25d9857b.jpg" width="700px">

Modelo de Bingham. Tomado de [HEC RAS Non-Newtonian User's Manual](https://www.hec.usace.army.mil/confluence/rasdocs/rasmuddebris/non-newtonian-user-s-manual/non-newtonian-transport-editor/non-newtonian-methods).
:::

El comportamiento Bingham en flujos de escombros se asocia a la presencia de una fase fina cohesiva (arcilla, limo) que genera enlaces inter-partícula y cohesión estática. Este esfuerzo cohesivo debe romperse para iniciar el flujo, de forma análoga a superar la resistencia al corte de un suelo cohesivo. Luego, la resistencia viscosa durante el flujo proviene de la fricción interna del fluido mezclado con partículas. Estudios experimentales con lodos naturales han demostrado que suspensiones de alta concentración (por ejemplo, material <2 mm de un flujo de escombros) se aproximan bien a un comportamiento de Bingham a tasas de corte moderadas y altas. Major y Pierson {cite}`major_pierson_1990` midieron reologías de un flujo de escombros natural y encontraron que, a tasas de deformación mayores de ~5 $s^-1$, la mezcla presentaba un límite de fluencia claro y viscosidad plástica definidas (comportándose como un Bingham plástico), con $\tau_y$ y $\mu_p$ fuertemente dependientes de la concentración de sedimento. 

El modelo de Bingham asume flujo laminar y homogéneo, con propiedades reológicas constantes durante el movimiento. Es más aplicable a flujos lentos o intermedios donde la turbulencia es despreciable y donde la cohesión de finos domina la resistencia (por eso, se recomienda para flujos con elevado contenido de finos arcillosos o limo). Por ejemplo, en flujos híper-concentrados o flujos de lodo iniciales, típicos en cuencas tropicales tras lluvias intensas, la matriz arcillosa induce un claro umbral de fluencia y altos valores de viscosidad, lo cual encaja en la idealización de Bingham. De hecho, en flujos híper-concentrados naturales se han reportado $\tau_y$ del orden de cientos de Pascales y viscosidades plásticas de $10^{-1}–10^1$ Pa·s o mayores, dependiendo de la concentración y mineralogía (valores que exceden por mucho la viscosidad del agua, 0.001 Pa·s). El modelo de Bingham se ha utilizado con éxito para simular flujos de detritos con altas concentraciones de sedimento tanto en laboratorio como en campo.

A pesar de su simplicidad, el modelo de Bingham tiene limitaciones importantes. En primer lugar, impone una relación lineal entre esfuerzo y tasa de deformación una vez iniciado el flujo, lo cual no siempre concuerda con materiales naturales: experimentos han mostrado que la relación puede ser no lineal (concavidades en la curva esfuerzo vs. deformación) debido a efectos de estructura interna o dispersión granular. Asimismo, la hipótesis de viscosidad constante solo es razonable a altas tasas de corte; a bajas tasas, muchos lodos exhiben tixotropía o variación de viscosidad (por ejemplo, la mezcla puede comportarse más rígida de lo predicho, endureciéndose al reposo). Major y Pierson notaron que por debajo de 5 $s^{-1}$ las suspensiones desviaban su comportamiento del modelo Bingham, indicando que otros procesos (formación de puente de partículas, etc.) entraban en juego. Otra limitación es que el modelo no incluye efectos granular-inerciales: si el flujo contiene una fracción importante de grava o bloques que interactúan por colisiones, el Bingham puro no capturará el aumento de resistencia a altas velocidades debido a esos choques. En tales casos, un término cuadrático (como veremos en otros modelos) sería necesario. Finalmente, la determinación de los parámetros $\tau_y$ y $\mu_p$ requiere datos experimentales (reometría, ensayos de flujo inclinado, etc.), y estos parámetros pueden variar dinámicamente si cambia la concentración (por erosión/dilución durante el recorrido), algo que el modelo base no contempla (se asume constantes). Aun con estas limitaciones, el modelo de Bingham proporciona una primera aproximación robusta para flujos donde la cohesión de finos y la viscosidad laminar son los controles primarios de la dinámica.

### Modelo de Herschel–Bulkley

El método de Herschel-Bulkley {cite}`herschel_bulkley_1926` es un enfoque no lineal de dos términos para la reología de flujos. Este método eleva la deformación a una potencia, la cual puede ser mayor o menor que 1. A diferencia del método de Bingham que eleva la deformación a la potencia de 1, o el de O'Brien que utiliza un modelo cuadrático (elevando la deformación a las potencias de 1 y 2), el de Herschel-Bulkley puede elevar la deformación a potencias no enteras, ya sean mayores o menores que 1. El modelo de Herschel–Bulkley (HB) es una generalización del modelo de Bingham que permite capturar relaciones no lineales entre el esfuerzo cortante y la tasa de deformación, manteniendo a la vez la noción de un esfuerzo de fluencia. Su ecuación constitutiva se expresa típicamente como:

$\tau=\tau_y+ 𝐾 \dot\gamma^n$  

​
donde $\tau_y$ es de nuevo el esfuerzo umbral de fluencia, $K$ es el índice de consistencia (una especie de viscosidad generalizada) y $n$ es el índice de comportamiento (exponente de la ley de potencia). Cuando $n = 1$, el modelo de Herschel–Bulkley se reduce al caso Bingham simple. En todos los casos, persiste la condición de umbral: por debajo de $\tau_y$ no hay deformación permanente, análogo a un sólido.

**Traducción a pérdida reológica (Ecuación de Momento):**
Asume alteraciones dinámicas (ablandamientos o endurecimientos) sobre el perfil principal base en función de las deformaciones: $S_{MD} = \frac{\tau_y}{\rho_m g h} + \frac{K}{\rho_m g h} \left( \frac{V}{h} \right)^n$
* **Si $n < 1$ (Shear-thinning):** El fluido es pseudoplástico (reóctipo adelgazante). El barro y la masa lodosa sufren una relajación del campo macroscópico al avanzar. Crecimiento logarítmico del arrastre que se suaviza con mayor velocidad.
* **Si $n > 1$ (Shear-thickening):** Comportamiento dilatante o espesante. Propio de mezclas altamente concentradas saturadas. Si el evento sube por un pico temporal de velocidad se resistirá a sí mismo frenando drásticamente, incrementando las pérdidas exponencialmente sobre esa cresta inercial.

:::{figure-md} Herschel-Bulckley model
<img src="https://i.pinimg.com/736x/2c/fb/9f/2cfb9f97792edddde512299d557b591c.jpg" width="700px">

Modelo de Herschel-Bulckley. Tomado de [HEC RAS Non-Newtonian User's Manual](https://www.hec.usace.army.mil/confluence/rasdocs/rasmuddebris/non-newtonian-user-s-manual/introduction-taxonomy-and-rheology-of-debris-flows).
:::


El modelo de Herschel-Bulkley requiere tres parámetros: El Esfuerzo de Cedencia en el modelo de Herschel-Bulkley es el mismo que en Bingham. Sin embargo, el parámetro lineal que acompaña al término de Deformación pierde sus unidades de viscosidad si la deformación se eleva a una potencia distinta de 1. Por lo tanto, K ya no es la viscosidad cuando el modelo de Herschel-Bulkley se desvía del modelo de Bingham (n≠1). Tanto $K$ como $n$ son parámetros empíricos definidos por el usuario.

:::{figure-md} Herschel-Bulckley model
<img src="https://i.pinimg.com/736x/58/ad/45/58ad45847d713f041cf494e1f1d6c2ab.jpg" width="700px">

Implementación del modelo de Herschel-Bulckley en HEC-RAS. Tomado de [HEC RAS Non-Newtonian User's Manual](https://www.hec.usace.army.mil/confluence/rasdocs/rasmuddebris/non-newtonian-user-s-manual/introduction-taxonomy-and-rheology-of-debris-flows).
:::


El modelo Herschel–Bulkley fue introducido para reflejar observaciones experimentales ya que muchos lodos naturales y sedimentos no siguen la relación lineal de Bingham, sino que presentan curvatura en la curva esfuerzo-deformación. Por ejemplo, Major y Pierson {cite}`major_pierson_1992` hallaron que, tras exceder $\tau_y$, los lodos podían mostrar inicialmente una pendiente alta (alta viscosidad aparente) que luego disminuía con mayor tasa de corte, sugiriendo comportamiento pseudoplástico (debido posiblemente a la destrucción progresiva de la estructura floculada). En esos casos, un exponente $n < 1$ en la fórmula HB brindaría un mejor ajuste que $n=1$. Este modelo es útil para suspensiones de finos bajo altas tasas de deformación donde la no linealidad es pronunciada. También se ha utilizado en modelación de flujos de escombros para incorporar cierta dependencia de la viscosidad con la velocidad de deformación, aportando mayor realismo que Bingham. Muchos códigos numéricos modernos (e.g. HEC-RAS 6.0) ofrecen la opción de Herschel–Bulkley generalizado justamente para permitir ajustar mejor la reología con datos de laboratorio. Típicamente, se encuentran valores de $n$ entre ~0.2 y 0.8 para lodos volcanogénicos o arcillosos (shear-thinning marcado), mientras que mezclas con arena gruesa pueden tender a $n$ cercano a 1 o incluso >1 si hay interacciones dilatantes. Por su parte, $K$ (consistencia) refleja la resistencia viscosa de la mezcla; sus unidades dependen del valor de $n$ (lo cual dificulta la interpretación física directa de $K$). Para dar una idea, un flujo con alta fracción de arcilla podría tener $\tau_y$ de varios cientos de Pa, $n \approx 0.5$ y $K$ correspondiente a viscosidades aparentes del orden de 1–10 Pa·s a tasas de corte típicas, mientras que un flujo más diluido podría tener $\tau_y$ bajo (cercano a 0) y comportamiento más Newtoniano ($n \to 1$). 

Al igual que Bingham, Herschel–Bulkley sigue siendo un modelo monofásico continuo que trata la mezcla heterogénea como un fluido efectivo. No incorpora explícitamente la física granular (colisiones, rozamiento inter-grano) ni efectos dependientes del tiempo como la tixotropía. Se asume también flujo laminar o lentamente variado, donde la tensión total se puede partir en un componente cohesivo (*yield*) más uno viscoso general. 

El modelo HB añade flexibilidad a costa de introducir más parámetros que requieren calibración. Determinar $\tau_y$, $K$ y $n$ simultáneamente exige datos reológicos detallados; frecuentemente $\tau_y$ se mide mediante pruebas de fluencia o reómetros de torsión a bajo corte, y luego $K, n$ se ajustan a curvas esfuerzo-$\dot\gamma$. La presencia de $n \neq 1$ conlleva que $K$ tenga unidades no triviales (Pa·s^n), lo cual complica su estimación a priori. Además, aunque HB captura la no linealidad, no representa explícitamente la transición sólido-fluido de manera dinámica: es decir, el mismo $\tau_y$ rige la iniciación y la detención, cuando en realidad en flujos naturales puede existir histéresis (se requiere más esfuerzo para iniciar que para mantener el flujo, debido a reestructuración interna). Por ello, a veces se usan variantes como el modelo de Papanastasiou que suaviza la transición en torno a $\tau_y$. Otra limitación es similar a Bingham: en escenarios de alto número de Reynolds o flujos muy rápidos, el modelo HB por sí solo no refleja la aparición de turbulencia ni de tensiones dispersivas por choques. En suma, Herschel–Bulkley es más general que Bingham y suele proporcionar mejores ajustes a datos reométricos de flujos de detritos reales, pero sigue siendo apropiado principalmente cuando el comportamiento está dominado por la matriz fluida y cohesiva bajo flujo laminar. En casos con componentes granulares dominantes o flujos extremadamente rápidos, será necesario complementarlo o optar por otros modelos.

### Modelo de Voellmy–Salm
El modelo de Voellmy–Salm surge originalmente en el contexto de las avalanchas de nieve {cite}`voellmy_lawinen_1955,perla_twoparameter_1980` y ha sido adaptado con éxito para flujos de escombros y otros movimientos rápidos de detritos. A diferencia de los modelos viscoplásticos anteriormente descritos, Voellmy–Salm no se basa en una viscosidad de fluido, sino en una descripción de la resistencia al flujo como combinación de fricción seca y resistencia turbulenta. En esencia, este modelo asume que la fuerza resistiva por unidad de volumen (o la pendiente de energía $S_f$ equivalente) se puede descomponer en dos componentes: uno independiente de la velocidad (análogo a una fricción Coulomb constante) y otro proporcional al cuadrado de la velocidad (similar a una resistencia aerodinámica o turbulenta). Una forma típica de expresarlo es:

$S_f = \mu \cos \theta + \frac{V^2}{\xi h}$  
 
**Traducción en esquemas numéricos operacionales (p. ej., RAMMS, Iber):**
Éste modelo agrupa los términos resistivos llanamente en el basal global, combinando un comportamiento tipo rotura Mohr-Coulomb modificado con resistencia aerodinámica.
* **Fricción Topológica de Coulomb ($\mu \cos \theta$):** Reemplazo directo al limitador *yield*. No es en absoluto dependiente de la rapidez del recorrido, se calcula pura y rígidamente sobre el factor angular ortogonal topográfico y su propio peso dinámico depositado. Si la gravedad proyectada no supera esta fricción, el material se detiene por rozamiento seco.
* **Término Hidrodinámico "Drag" ($\frac{V^2}{\xi h}$):** Donde el coeficiente turbulento $\xi$ compensa la agresividad local (típicamente entre 100 y 1000 m/s²). Actúa sumando un freno cuadrático inmerso proporcional al cuadrado de la velocidad del flujo ($V^2$), regulando las aceleraciones veloces en terrenos accidentados y choques inerciales.

El modelo Voellmy–Salm conceptúa el flujo de detritos como un continuo granular fluido donde la resistencia al deslizamiento proviene de dos fuentes: (a) fricción basal entre el material y el lecho (y entre partículas, en promedio), representada por $\mu_f$ constante, y (b) pérdidas por energía de fluctuaciones turbulentas o dispersivas dentro del flujo, representadas por el término cuadrático. El término de fricción seca actúa como un esfuerzo de fluencia basal basado en el peso: si la componente de la gravedad paralela al plano ($\rho g h \sin\theta$) no supera $\mu_f \rho g h \cos\theta$, el flujo tenderá a frenar y detenerse, lo que es análogo a decir que $\tan\theta < \mu_f$ resulta en detención (criterio similar al de un bloque rozante en un plano inclinado). Este modelo por tanto no considera cohesión, asumiendo material sin cohesión (cohesión = 0 en términos de Mohr-Coulomb). Por otro lado, el término turbulento-inercial capta que a mayores velocidades, la agitación interna y los choques entre partículas ejercen una fuerza resistiva creciente (al igual que la resistencia del aire crece con $V^2$). En flujos de escombros rápidos, especialmente con alto contenido de bloques grandes y surcando cauces estrechos, se observa un comportamiento semejante: una vez que aceleran, la disipación adicional proviene más de la turbulencia y colisiones que de la viscosidad del fluido intersticial. El modelo Voellmy ha sido implementado en herramientas de simulación de avalanchas y flujos, como el software RAMMS (Rapid Mass Movement Simulation) para avalanchas de nieve y flujos de detritos, y en modelos de flujo bidimensionales de investigadores en Suiza, Canadá y otros países. Por ejemplo, Zimmermann et al. {cite}`zimmermann_2020` calibraron el modelo Voellmy–Salm en 19 flujos de ladera en los Alpes Suizos, hallando que el parámetro de fricción $\mu_f$ correlacionaba con el porcentaje de arcilla del material movilizado (más arcilla tendía a elevar ligeramente la fricción dinámica, posiblemente por mayor cohesión residual al detenerse). 

Voellmy–Salm es apropiado para flujos muy rápidos y dominados por componentes granulares. Típicamente se usa en escenarios de flujos en canales empinados o avalanchas de detritos donde el material se mueve a varios m/s y recorre largas distancias con comportamiento análogo a un flujo turbulento de alta densidad. En regiones de montaña con flujos de escombros canalizados (por ejemplo, en los Himalayas, Andes o Alpes), este modelo puede reproducir adecuadamente la rápida aceleración y luego desaceleración por rozamiento que se observa en eventos extremos. También se ha empleado en simulaciones de avalanchas de rocas o flujos de detritos generados por colapsos de presas naturales, donde la componente líquida es secundaria y el comportamiento se acerca más al de un flujo granular. Debe destacarse que $\mu_f$ y $\xi$ no se miden directamente en laboratorio sino que se calibran retrospectivamente: por ejemplo, a partir de la distancia de alcance y velocidad estimada del flujo, se eligen combinaciones de $\mu_f$ y $\xi$ que reproduzcan esos datos. Valores típicos para flujos de escombros líquidos suelen ser $\mu_f \sim 0.1–0.3$ y $\xi \sim 200–600 m/s^2$, mientras que para avalanchas de roca seca $\mu_f$ puede ser aún menor (~0.05–0.1) y $\xi$ mayor (>1000) debido a la menor resistencia basal y alta movilidad. 

Aunque eficaz para capturar la dinámica general de flujos rápidos, el modelo Voellmy–Salm es fenomenológico y tiene limitaciones. En primer lugar, al no incluir $\tau_y$ cohesivo, no modela bien el inicio de movimientos en materiales muy cohesivos o en flujos de baja velocidad: en tales casos, Voellmy predice que cualquier pendiente por encima de la fricción $\mu_f$ (p. ej. >10°) debería movilizarse, lo que no explica retardos por cohesión o umbrales de humedad necesarios para iniciar ciertos flujos tropicales. Por ello, algunas versiones extendidas incluyen un término cohesivo adicional (como un $\tau_y$ pequeño o un $\mu_f$ que disminuye con la velocidad). En segundo lugar, la naturaleza de $\xi$ es un “cajón de sastre” que engloba tanto turbulencia de fluido como dispersión granular; no distingue entre agua y sólidos ni sus interacciones de forma rigurosa. Esto significa que $\xi$ puede variar de un evento a otro sin correlación simple con parámetros físicos medibles (Zimmermann et al. encontraron que un parámetro extra de cohesión mejoraba las simulaciones pero no podía relacionarse directamente con propiedades del suelo o la saturación). Asimismo, el modelo asume condiciones de flujo permanente promedio, por lo que no captura transitorios como la fase de parada final donde la turbulencia decae y la mezcla se deposita (a menudo en la práctica, se impone que el flujo se detiene cuando $V$ cae a casi cero, pues Voellmy en teoría nunca da $\tau = 0$ a menos que $V=0$ exactamente). Pese a estas simplificaciones, la formulación Voellmy–Salm ha demostrado ser muy útil en ingeniería para estimar corridas y velocidades máximas de flujos de escombros en 2D, integrándose en códigos como RAMMS DF (de Suiza) o DAN3D (de Canadá) y permitiendo calibrar escenarios de riesgo con relativamente pocos parámetros. Su popularidad se debe a que capta bien la diferencia entre la fase inicial dominada por la gravedad (aceleración rápida limitada solo por fricción basal) y la fase avanzada dominada por la disipación turbulenta (donde la velocidad máxima se regula por el balance entre pendiente y resistencia cuadrática).

### Modelo PCM
El modelo PCM {cite}`perla_twoparameter_1980` es un modelo de fricción de dos parámetros basado en el modelo de Voellmy {cite}`voellmy_lawinen_1955`. Fue desarrollado inicialmente para avalanchas de nieve, pero ha sido utilizado con éxito para simular la propagación de flujos de detritos por varios autores {cite}`gamma_dflow_2000,horton_flowr_2013,rickenmann_2016b,wichmann_becht_2004,zimmermann_2020`.

El modelo, basado en el centro de masa, asume que el movimiento está controlado principalmente por un coeficiente de fricción por deslizamiento $\mu$, y una relación masa-resistencia aerodinámica $M/D$. El parámetro $\mu$ domina el comportamiento de la velocidad en la zona de alcance (*runout*).

La velocidad puede calcularse a lo largo de segmentos de pendiente más o menos homogéneos {cite}`gamma_dflow_2000`, o  celda por celda en el camino del flujo {cite}`horton_flowr_2013,wichmann_gpp_2017`. La velocidad $v_i$ en una celda se calcula según:

$$
v_i^2 = \alpha_i \left(\frac{M}{D}\right)_i \left(1 - e^{\beta_i}\right) + v_{i-1}^2 e^{\beta_i}
$$

donde los parámetros $\alpha_i$ y $\beta_i$ son:

$$
\alpha_i = g \left( \sin \theta_i - \mu_i \cos \theta_i \right)
$$

$$
\beta_i = -\frac{2 L_i}{(M/D)_i}
$$

y:  $v_i$: velocidad [m/s] en la celda actual,  $v_{i-1}$: velocidad en la celda anterior,  $g$: aceleración de la gravedad [m/s²],  $\theta_i$: pendiente local [°],   $L_i$: longitud de la pendiente en la celda $i$ [m],  $\mu_i$: coeficiente de fricción por deslizamiento [-],  $M/D$: relación masa-resistencia aerodinámica [m].

En transiciones cóncavas (cuando la pendiente disminuye), la velocidad previa $v_{i-1}$ se ajusta para conservar el momento lineal, según:

$$
v_i = v_{i-1} \cos \left( \theta_{i-1} - \theta_i \right) \quad \text{si} \quad \theta_{i-1} \geq \theta_i
$$

La distancia de alcance del flujo se calibra ajustando los dos parámetros de fricción.  Sin embargo, como diferentes combinaciones de $\mu$ y $M/D$ pueden producir la misma distancia de alcance en la práctica, se mantiene $M/D$ constante a lo largo del trayecto, calibrándolo una sola vez para alcanzar rangos realistas de velocidad máxima.
El coeficiente de fricción $\mu$ puede mantenerse constante o variar a lo largo del camino, por ejemplo, para representar diferentes condiciones reológicas debidas al contenido de agua.

### Modelo de Bagnold

El modelo de Bagnold {cite}`bagnold_experiments_1954` con comportamiento dilatante (es decir, de espesamiento por cizalla, donde el fluido ofrece mayor resistencia a mayores esfuerzos cortantes aplicados) y diferencia tres tipos de flujos: el régimen macroviscoso, el régimen de transición y el régimen granular-inercial.

El régimen al que pertenece el flujo puede determinarse por el Número de Bagnold ($Ba$), el cual depende del diámetro del sedimento ($D$), la concentración volumétrica del sedimento ($C$), la máxima concentración de sedimento ($C_0$), la viscosidad dinámica del fluido ($\mu$) y la densidad de las partículas de sedimento ($\rho_s$).

* Número de Bagnold ($Ba$): Esta es la relación entre los esfuerzos inerciales y los viscosos.

$$Ba = \frac{\rho_s D^2 \lambda^{1/2} \dot{\gamma}}{\mu_f}$$

$\rho_s$ = densidad de las partículas sólidas

$D$ = diámetro de la partícula

$\lambda$ = concentración lineal

$\dot{\gamma}$ = tasa de cizalla (shear rate)

$\mu_f$ = viscosidad del fluido intersticial

Es importante notar que Bagnold usó la "concentración lineal" ($\lambda$), que es la relación entre el diámetro de la partícula ($D$) y la distancia media entre partículas. Se relaciona con la concentración volumétrica ($C$) así:

$$\lambda = \left[ \left( \frac{C_0}{C} \right)^{1/3} - 1 \right]^{-1}$$

Donde $C_0$ es la concentración máxima posible (aprox. 0.61-0.74).

De acuerdo con lo anterior, una ecuación describe el esfuerzo cortante como una función de la deformación del fluido para cada régimen, donde $a_v$ y $a_i$ son constantes experimentales y donde $\alpha_1$ es el ángulo de fricción dinámico (el cual es diferente del ángulo de fricción interna).

* Régimen Macroviscoso (Ba < 40): A bajas tasas de cizalla y/o bajas concentraciones. Las partículas están "lejos" y el fluido intersticial domina. El momento se transfiere a través de la viscosidad del fluido. El fluido actúa como un lubricante.
  
  El esfuerzo total ($\tau$) es dominado por el esfuerzo viscoso ($\tau_v$). La resistencia es linealmente proporcional a la tasa de cizalla (comportamiento Newtoniano o de Bingham).

$$\tau \approx \tau_v = a_v \cdot \mu_f \cdot (1 + \lambda) \cdot (1 + 0.5 \lambda) \cdot \dot{\gamma}$$

$a_v$ = constante empírica (Bagnold encontró $\approx 2.25$ para $\lambda > 5$)

$\mu_f$ = viscosidad del fluido

Forma Simplificada: $\tau_v \propto \mu_{mezcla} \cdot \dot{\gamma}$

* Régimen Granular-Inercial(Ba > 450): A altas tasas de cizalla y/o altas concentraciones. Las partículas están tan juntas que el fluido es "exprimido". El momento se transfiere a través de colisiones inerciales (choques) y fricción entre los granos. El flujo se "dilata" (expande) para poder moverse.

El esfuerzo total ($\tau$) es dominado por el esfuerzo inercial ($\tau_i$). La resistencia es proporcional al cuadrado de la tasa de cizalla (comportamiento dilatante o shear-thickening).

$$\tau \approx \tau_i = a_i \cdot \rho_s \cdot (\lambda D)^2 \cdot (\dot{\gamma})^2 \cdot \sin(\alpha_i)$$

$a_i$ = constante empírica (Bagnold encontró $\approx 0.042$)

$\alpha_i$ = ángulo de fricción dinámico (mencionado como $\alpha_1$ en tu texto, $\approx 30^\circ - 37^\circ$)

Forma Simplificada: $\tau_i \propto \rho_s D^2 \cdot (\dot{\gamma})^2$

El modelo de Bagnol tiene como suposiciones claves que el fluido entre los granos (agua) es simple (Newitoniano); no hay fuerzas electroquímicas (cohesión) entre las partículas. Esto lo hace inapropiado para flujos con alto contenido de arcilla; los granos son esféricos, rígidos, de tamaño uniforme (monodispersos) y flotabilidad neutra (en sus experimentos originales, $\rho_s = \rho_f$); el modelo describe un flujo en equilibrio, no su inicio o detención; y altas concentracione, el modelo se desarrolló para $C > 9\%$.

### Modelo cuadrático de O'Brien y Julien
El modelo reológico cuadrático de O’Brien et al. {cite}`obrien_julien_1988` combina los cuatro componentes de esfuerzo de las mezclas de sedimentos no newtonianas: (1) cohesión entre partículas, (2) fricción interna entre el fluido y las partículas de sedimento, (3) turbulencia, y (4) impacto inercial entre partículas. El modelo cuadrático separa las relaciones de esfuerzo-deformación en estos cuatro componentes aditivos, de modo que el esfuerzo cortante es:

$$\tau_{MD} = \tau_c + \tau_v + \tau_t + \tau_d$$  

Donde: $\tau_{MD}$ = el esfuerzo cortante total de lodo y detritos (Pa), $\tau_c$ = esfuerzo de cedencia (Pa), $\tau_v$ = esfuerzo cortante viscoso (Pa), $\tau_t$ = esfuerzo cortante turbulento (Pa) {cite}`julien_obrien_1997`, $\tau_d$ = esfuerzo cortante dispersivo (Pa).

**Traducción a pérdida reológica (Ecuación de Momento):**
Como pendiente de lodo-escombros agrupa esta ecuación combinada separando los efectos en cuatro disipaciones sumadas: $S_{MD} = S_y + S_v + S_t + S_d$
* **$S_y$:** Fuerzas inter-cohesivas estáticas (como en Bingham).
* **$S_v$:** Roce viscoso lineal del relleno capilar cargado capilarmente de finos.
* **$S_t$ (Turbulencia interna):** Remolinos macroscópicos del barro. A menudo equivalente matemáticamente a los ajustes de Manning.
* **$S_d$ (Dispersión de Bagnold):** Impactos inerciales resultantes por colisiones entre fragmentos rocosos (*boulders*). Crece exponencialmente cuadrático directo escalando a la matriz del material y $V^2$.

En conjunto, la fórmula cuadrática busca modelar flujos de lodo sumamente heterogéneos: a tasas de corte muy reducidas y durante la fase estática domina la cedencia de límite $\tau_c$ (Bingham clásico), a tasas intermedias el avance es netamente resistido viscosamente, y a tasas altísimas las dinámicas de fricción colisional y turbulencias (tipo dilatante engrosante). Modelos clásicos de simulación integral como FLO-2D aplican esta aproximación incorporando todo el tren termodinámico en su core matemático. Su calibración resulta muy precisa en capturar el efecto de la concentración de grandes bloques, pero requiere parametrización de factores cruzados (el límite de cedencia, los empujes newtonianos visco-reales, el choque dispersivo cuadrático de partículas y turbulencias macro).

O’Brien et al. {cite}`obrien_julien_1988` formulan cada uno de estos componentes de esfuerzo para construir su modelo cuadrático, el cual se basa en la tasa de deformación ($dv_x/dz$):

$$ \tau_{MD} = \tau_c + \mu_m \left( \frac{dv_x}{dz} \right) + \rho_m l_m^2 \left( \frac{dv_x}{dz} \right)^2 + c_{Bgd} \rho_s \left( \left[ \frac{C_*}{C_v} \right]^{\frac{1}{3}} - 1 \right)^{-2} d_s^2 \left( \frac{dv_x}{dz} \right)^2 $$   

donde: $dv_x/dz$ = la tasa de corte (1/s), calculada como una función de la velocidad promediada en la profundidad y la profundidad del flujo, $\mu_m$ = viscosidad dinámica de la mezcla (Pa·s), $\rho_m$ = densidad de masa de la mezcla de sedimento (kg/m³), $l_m$ = longitud de mezcla (m), $c_{Bgd}$ = coeficiente empírico de impacto de Bagnold ($c_{Bgd} \cong 0.01$), $\rho_s$ = densidad de las partículas de sedimento (kg/m³), $C_*$ = concentración volumétrica máxima de sedimento, $C_v$ = concentración volumétrica de sedimento, $d_s$ = tamaño de grano del sedimento (m).

Takahashi {cite}`takahashi_1980` identificó experimentalmente que el coeficiente de impacto de Bagnold ($c_{Bgd}$) varía entre 0.35 y 0.5, y que es significativamente mayor que el valor recomendado por Bagnold (1954 y 1956). Iverson {cite}`iverson_physics_1997` propone aproximar la tasa de corte media ($dv_x/dz$) como $3\bar{u}/h$ para un perfil de velocidad parabólico, o como $2\bar{u}/h$ para un perfil lineal, donde $\bar{u}$ = velocidad promediada en la profundidad (m/s), $h$ = profundidad del flujo (m). 

La ecuación para la longitud de mezcla de Prandtl se define como: $l_m = kz$. donde: $k$ = el coeficiente de Von Kármán ($\cong 0.41$), $z$ = la distancia proporcional desde el contorno (lecho).

Este modelo cuadrático combina modos reológicos lineales y no lineales para calcular el esfuerzo cortante interno. El rendimiento de los modelos reológicos disminuye a medida que las mezclas se vuelven más clásticas (es decir, con altas concentraciones de partículas gruesas). 

La ecuación de O'Brien utiliza un modelo cuadrático para añadir los impactos no lineales de la colisión de partículas y la turbulencia a los términos lineales de cedencia y viscosidad del modelo de Bingham. No es tan flexible como la de Herschel-Bulkley. Los efectos no lineales son siempre una función del cuadrado de la deformación, por lo que siempre son efectos de espesamiento por cizalla (shear-thickening) fuertes. Sin embargo, el modelo de O'Brien es más fácil de parametrizar que el de Herschel-Bulkley. La ecuación de O'Brien utiliza valores físicos para desarrollar efectos cuadráticos teóricos. La desventaja de este enfoque es que si la formulación teórica no refleja los procesos del flujo geofísico, introducirá errores. Pero el beneficio de este enfoque físico-teórico es que todos los parámetros de entrada en los términos no lineales son parámetros físicos que son o bien predeterminados o relativamente intuitivos de especificar para el usuario.

Además del esfuerzo de cedencia, Gibson et al. {cite}`gibson_2008` demostraron que valores más bajos de cedencia y viscosidad son a menudo apropiados para el enfoque de O'Brien en comparación con el de Bingham, porque la ecuación de O'Brien está considerando explícitamente procesos en el término cuadrático que Bingham está agrupando (lumping) en los parámetros lineales. Y además de la viscosidad del flujo cargado de sedimento que se requiere para el modelo de Bingham, el modelo de O'Brien solo requiere la concentración volumétrica (que ya es necesaria para el aumento de volumen o bulking y para algunas estimaciones de cedencia y viscosidad) y un tamaño de grano representativo. HEC-RAS también ha expuesto el valor predeterminado de la concentración volumétrica máxima en el término de Bagnold de O'Brien (0.615 o 61.5%). Este término es aceptable para flujos de menor concentración (Cv<50%). Pero a medida que la concentración se acerca o supera este máximo teórico, los usuarios deberían aumentarlo para que sea mayor que la concentración volumétrica.

:::{figure-md} quadratic O'Brien model
<img src="https://i.pinimg.com/736x/13/ab/a5/13aba5b9705dff738d2f30616141f667.jpg" width="700px">

Implementación del modelo cuadrático de O'Brien en HEC-RAS. Tomado de [HEC RAS Non-Newtonian User's Manual](https://www.hec.usace.army.mil/confluence/rasdocs/rasmuddebris/non-newtonian-user-s-manual/introduction-taxonomy-and-rheology-of-debris-flows).
:::

### Modelo de fricción de Coulomb
Es el caso límite en que la resistencia se considera totalmente dominada por la fricción interna del material, con un criterio de falla tipo Mohr-Coulomb (esfuerzo cortante máximo = $\tau_f = \tau_c + \sigma \tan\phi$). En flujo continuo, esto equivale esencialmente a $\tau = \mu \sigma_n + c$ (con $c$ cohesión, a menudo cero) constante durante el movimiento. Este modelo no tiene término viscoso ni dependiente de tasa de deformación. Es adecuado solo para flujos casi secos o grandes avalanchas de rocas donde el material se comporta más como un deslave granular que como un fluido. En contextos de escombros saturados, el modelo de Coulomb puro suele ser insuficiente, aunque a veces se incluye como componente basal en modelos más completos. Por ejemplo, el “modelo Coulomb-viscoso” mencionado en literatura combina una fricción Coulomb basal con un término viscoso lineal para tener tanto cohesión (c) como fricción $\tan\phi$.

HEC-RAS simula los flujos de detritos clásticos con una aproximación de Coulomb basada en el modelo viscoso de Coulomb de Johnson y Rodine {cite}`johnson_rodine_1970` {cite}`naef_2006`. Este enfoque reemplaza el esfuerzo de cedencia de Bingham ($\tau_c$) por un esfuerzo de cedencia de Coulomb de tipo geotécnico, definido como:

$$
\tau_{yc} = \rho_m g h \cos\alpha \tan\phi 
$$  

donde: $\tau_{yc}$ = Esfuerzo de cedencia de Coulomb (Pa), $\alpha$ = ángulo de la pendiente del lecho (°), $\phi$ = ángulo de fricción de Coulomb (°), que típicamente varía entre 30° y 40° {cite}`iverson_physics_1997,mcardell_2019`. El ángulo de fricción de Coulomb es una función del ángulo de fricción de los granos individuales y de la geometría de empaquetamiento de las partículas a lo largo del plano de falla.

## Comparación, Supuestos y Limitaciones de los Modelos
A continuación se comparan los modelos presentados, enfatizando sus supuestos fundamentales y los escenarios donde mejor se ajustan, así como sus limitaciones inherentes:

**Naturaleza de la resistencia**: Bingham y Herschel–Bulkley tratan la resistencia del flujo como una combinación de cohesión estática (*yield*) + fricción viscosa dependiente de la velocidad de deformación. Voellmy–Salm, por otro lado, lo concibe como fricción basal + término inercial cuadrático (dependiente de la velocidad de flujo). El modelo cuadrático de O’Brien combina ambas filosofías al incluir término viscoso lineal + término cuadrático. En términos simples, Bingham/HB son más centrados en la fase fluida/cohesiva, mientras Voellmy y Coulomb se centran en la fase sólida/friccional; los modelos híbridos (HB con $n<1$, O’Brien) buscan mediar entre ambos extremos.

**Umbral de fluencia vs. fricción continua**: Bingham y HB incorporan explícitamente un umbral de fluencia $\tau_y$ que debe vencerse para que haya flujo. Esto refleja bien la necesidad de cierta energía (p.ej. lluvia intensa que eleve presión de poros) para arrancar un flujo de escombros cohesivo. Voellmy–Salm carece de un verdadero $\tau_y$ cohesivo; el único umbral es gravitacional: si la pendiente local supera la fricción $\mu_f$, el material podría empezar a moverse (asumiendo saturación). Por tanto, para flujos cohesivos (p.ej. escombros tropicales arcillosos), Bingham/HB capturan la etapa de no flujo a pendientes moderadas hasta que se excede $\tau_y$, mientras Voellmy podría sobreestimar la movilidad incial (prediciendo flujo demasiado pronto). Algunos códigos combinan ambos: por ejemplo, RAMMS permite agregar un término cohesivo al modelo Voellmy para representar la resistencia en flujos lentos de ladera.

**Dependencia de la velocidad (régimen laminar vs turbulento)**: Los modelos de Bingham y HB suponen implícitamente un régimen laminar o cuasi-laminar, donde la tensión cortante se relaciona con la velocidad de deformación local. No incluyen términos cuadráticos en la velocidad absoluta del flujo, por lo que por sí solos no pueden representar la resistencia adicional que aparece con la turbulencia o con el transporte de grandes bloques a gran velocidad. En contraste, Voellmy–Salm y O’Brien sí incluyen un término cuadrático (ya sea $\propto V^2$ o $\propto \dot\gamma^2$, que en flujo rápido se relacionan) que representa esa resistencia inercial. Por ello, para flujos muy rápidos en cauces empinados (Re alto, Froude alto), un modelo puramente Bingham podría subestimar la disipación – típicamente en esos casos se han observado largos runouts que solo se explican introduciendo fricción turbulenta variable con $V$. Por otro lado, en flujos lentos (Re bajo) el término cuadrático es despreciable y modelos como Voellmy colapsan en un término constante (lo cual no refleja el aumento gradual de viscosidad a bajas velocidades, e.g. por tixotropía). En suma, flujos lentos cohesivos: Bingham/HB son preferibles; flujos rápidos no cohesivos: Voellmy es preferible; flujos intermedios: modelos combinados o calibración cuidadosa.

**Calibración y parámetros**: Los modelos presentados requieren distintos insumos paramétricos. Bingham: $\tau_y$ y $\mu_p$ medibles con reometría de lodos; Herschel–Bulkley: $\tau_y$, $K$, $n$ (difíciles de estimar sin datos detallados); Voellmy: $\mu_f$ y $\xi$ (se calibran con casos observados, no medibles directamente); O’Brien: $\tau_y$, $\mu$, más parámetros granulares ($c_{Bd}$, etc.) parcialmente empíricos. Así, los modelos viscoplásticos suelen vincularse más a propiedades intrínsecas del material (contenido de finos, humedad determinan $\tau_y$), mientras los inerciales requieren calibración macroscópica (por ejemplo, usando registros de huellas de flujo para ajustar $\mu_f$). Cabe destacar que, en cualquier caso, los modelos reológicos efectivos suelen involucrar coeficientes de ajuste. Garcés et al. {cite}`vergara_2021` recalcan la importancia de identificar los parámetros más sensibles y reconocer que múltiples combinaciones de parámetros pueden reproducir observaciones, lo que exige criterio experto para evitar calibraciones no físicas.

**Interacción con procesos de campo**: Una limitación común de los modelos reológicos simples es que no contemplan cambios en la mezcla debidos a erosión o deposición durante el recorrido. En ambientes montañosos y tropicales, los flujos de escombros a menudo erosionan material del lecho (aumentando su carga de sólidos) o se diluyen con afluentes de agua a lo largo de la trayectoria. Esto altera la reología instantáneamente (un flujo que gana sedimento puede pasar de Newtoniano a Bingham espeso en minutos). Los modelos presentados asumen generalmente propiedades homogéneas constantes; para mayor realismo, algunos modelos numéricos acoplan ecuaciones de *entrainment* (erosión) y recalculan parámetros reológicos localmente. No obstante, esa es una frontera activa de investigación, ya que incorporar la retroalimentación entre erosión y reología sigue siendo complejo.

**Validez de la aproximación continua**: Todos los modelos mencionados tratan al flujo de escombros como un continuo. Esto es razonable cuando la mezcla es muy heterogénea pero persistentemente mezclada (caso típico de flujos con matriz fluida abundante). Sin embargo, para avalanchas de rocas o flujos de detritos con gran proporción de bloques enormes, el supuesto de continuo puede fallar a escala local (bloques rodando individualmente). En tales casos, modelos discretos (DEM) o bifásicos pueden ser más apropiados.

### Rangos de parámetros reológicos para flujos de detritos (debris flow)

A partir de la revisión de literatura realizada por Sanz-Ramos et al. {cite}`sanzramos_iber_2025`, la Tabla siguiente recoge los valores mínimos y máximos reportados para los parámetros de calibración de cada modelo reológico específicamente en la modelación de **flujos de detritos** (*debris flow*):

| Modelo | Parámetro | Mínimo | Máximo |
|---|---|---|---|
| Manning | $n$ (s/m$^{1/3}$) | 0.1 | 1 |
| Voellmy | $\xi$ (m/s$^2$) | 10 | 600 |
| Voellmy | $\mu$ (-) | 0.1 | 0.55 |
| Bingham | $\tau_y$ (Pa) | 750 | 3500 |
| Bingham | $\mu_B$ (Pa·s) | 0.4 | 3200 |
| O'Brien-Julien | $\tau_y$ (Pa) | 700 | 1500 |
| O'Brien-Julien | $\mu_B$ (Pa·s) | 5 | 35 |
| O'Brien-Julien | $K$ (-) | 24 | 2000 |
| O'Brien-Julien | $n$ (s/m$^{1/3}$) | 0.05 | 0.20 |
| O'Brien-Julien | $C_v$ (-) | 0.3 | 0.6 |
| O'Brien-Julien | $Fr_{max}$ (-) | 0.5 | 2 |
| Herschel–Bulkley | $\tau_y$ (Pa)* | 0.0239 | 0.0239 |
| Herschel–Bulkley | $k$ (Pa·s$^\alpha$)* | 2.76 | 2.76 |
| Herschel–Bulkley | $\alpha$ (-)* | 0.5 | 0.5 |

*Para el modelo de Herschel–Bulkley, los valores provienen de un único dato reportado sin unidades por Satria et al. (2024), citado en Sanz-Ramos et al. {cite}`sanzramos_iber_2025`, por lo que el mínimo y el máximo coinciden.

Tabla Lista de modelos numéricos seleccionados para el cálculo del alcance de deslizamientos (adaptado de Kang y Chan {cite}`kang_2018`).

| Modelo          | Reología                                                  | Incorpora arrastre | Referencias seleccionadas                                                 |
|-----------------|------------------------------------------------------------|--------------------|--------------------------------------------------------------------------|
| 3dDMM           | Friccional y Voellmy                                       | Sí                 | Kwan y Sun {cite}`kwan_sun_2007`                                                       |
| DAN2D           | Friccional, Voellmy y Bingham                             | Sí                 | Hungr {cite}`hungr_model_1995`                                                            |
| DAN3D           | Friccional, Voellmy y Bingham                             | Sí                 | McDougall {cite}`mcdougall_2006`                                                        |
| FLATModel       | Friccional y Voellmy                                       | Sí                 | Medina et al. {cite}`medina_flatmodel_2008`                                                    |
| FLO-2D          | Cuadrática                                                 | No                 | O’Brien {cite}`obrien_flo2d_2006`                                                          |
| Flow-R          | Voellmy                                                    | No                 | Horton et al. {cite}`horton_flowr_2013`                                                    |
| GeoFlow-SPH     | Friccional, Voellmy y Bingham                             | Sí                 | Pastor et al. {cite}`pastor_depth_2009`                                                    |
| D-Claw          | Friccional                                                 | Sí                 | Iverson y George {cite}`iverson_george_2014`; Iverson {cite}`iverson_mechanics_2012`                                 |
| MADFLOW         | Friccional, Voellmy y Bingham                             | Sí                 | Chen y Lee {cite}`chen_lee_2000`                                                       |
| MassMov2D       | Voellmy y Bingham                                          | Sí                 | Begueria et al. (2009)                                                  |
| PFC             | Interacción entre partículas e interacción partícula-pared | Sí                 | Kang y Chan {cite}`kang_2018`                                                     |
| RAMMS           | Voellmy                                                    | No                 | Christen et al. {cite}`christen_ramms_2010`                                                  |
| RASH3D          | Friccional, Voellmy y Cuadrática                          | Sí                 | Pirulli {cite}`pirulli_2023`                                                          |
| r.avalanche     | Friccional                                                 | Sí                 | Mergili et al. {cite}`mergili_randomwalk_2012`                                                   |
| r.avaflow       | Friccional (sólido) y no newtoniano (fluido)               | Sí                 | Mergili et al. {cite}`mergili_ravaflow_2017`                                                   |
| Sassa-Wang      | Friccional                                                 | Sí                 | Wang y Sassa {cite}`wang_sassa_2002`                                                     |
| SCIDDICA S3-hex | Basado en energía                                          | No                 | D’Ambrosio et al. {cite}`dambrosio_2003`                                                |
| SHALTOP-2D      | Friccional                                                 | No                 | Mangeney-Castelnau et al. {cite}`mangeney_2003`                                        |
| TITAN2D         | Friccional                                                 | No                 | Pitman et al. {cite}`pitman_2003`                                                    |
| TOCHNOG         | Friccional (modelo elastoplástico)                        | Sí                 | Roddeman {cite}`roddeman_1994`                                                         |
| TopFlowDF       | Friccional, Voellmy y Cuadrática                          | No                 | Scheidl y Rickenmann {cite}`scheidl_rickenmann_2009`; Han et al. {cite}`han_2015`                          |
| VolcFlow        | Friccional y Voellmy                                       | Sí                 | Kelfoun y Druitt {cite}`kelfoun_druitt_2005`                                                 |
| Wang            | Friccional                                                 | No                 | Wang et al. {cite}`wang_runout_2003`; Kang y Chan {cite}`kang_2018`                                |
| Massflow        | Friccional                                                 | Sí                 | Ouyang et al. (2015)                                                    |
