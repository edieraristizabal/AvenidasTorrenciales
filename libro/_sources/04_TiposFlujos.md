<p style="font-size:11px;"><em><strong>Créditos</strong>: El contenido de este capítulo ha sido adaptado del [HEC-RAS Non-Newtonian / Mud and Debris Flow User's Manual](https://www.hec.usace.army.mil/confluence/rasdocs/rasmuddebris/non-newtonian-user-s-manual/introduction-taxonomy-and-rheology-of-debris-flows) y de la literatura clásica de reología de flujos de escombros (Bagnold, O'Brien y Julien, Coussot, entre otros).</em></p>


# Tipos de flujos

La reología es el estudio de cómo los materiales se deforman bajo un esfuerzo (o estrés). Por lo tanto, los *modelos reológicos* a menudo se expresan como relaciones simples entre esfuerzo y deformación. 
Los modelos hidráulicos de aguas claras ya asumen un modelo reológico para las simulaciones hidrodinámicas. 
Asumen que el agua comienza a deformarse (movimiento o deformación) bajo cualquier esfuerzo (intersección en cero en la relación esfuerzo-deformación), la deformación aumenta linealmente con el esfuerzo, y la viscosidad del agua es la relación entre el esfuerzo y la deformación. 
Estas son las suposiciones del flujo "newtoniano". 
Existen fluidos que se desvían de estas suposiciones, incluyendo una intersección esfuerzo-deformación distinta de cero o una relación esfuerzo-deformación no lineal, o ambas.

:::{figure-md} flow taxonomy
<img src="https://i.pinimg.com/736x/a9/95/92/a99592e92c06eb073c1c7d7699c5785e.jpg" alt="Flow axonomy" width="700px">h

Modelos reológicos utilizados para simular (a) agua limpia y (b, c) flujos de lodos y flujos de escombros. Tomado de [HEC RAS Non-Newtonian User's Manual](https://www.hec.usace.army.mil/confluence/rasdocs/rasmuddebris/non-newtonian-user-s-manual/introduction-taxonomy-and-rheology-of-debris-flows).
:::

La propiedad reológica de un flujo conformado por aguas y sedimentos depende de una variedad de factores, tales como la concentración de sólidos en suspensión, la cohesión, la distribución del tamaño de las partículas (granulometría), la forma de las partículas, la fricción entre granos y la presión de poros. En general, a medida que la concentración aumenta y el tamaño de grano se vuelve más grueso, la mezcla de fluido y sedimento pasa a través de cinco clasificaciones: (1) Flujo Newtoniano (agua clara o transporte de sedimento aluvial), (2) flujo hiperconcentrado, (3) flujo de lodo, (4) flujo de detritos y (5) flujo clástico. Sin embargo, estos tipos de flujo son continuos y se solapan entre sí.

## Propiedades físicas y reológicas

**Densidad aparente (ρ):** Varía entre 1500 y 2500 kg/m³. Los flujos hiperconcentrados oscilan entre 1330 y 1800 kg/m³ y los flujos de escombros entre 1800 y 2300 kg/m³, dependiendo de la composición litológica.

**Viscosidad (μ, en Pa·s):** Comúnmente entre 0,001 y 0,1 Pa·s. Controla la resistencia al corte y depende de la concentración de sedimentos, especialmente de limo y arcilla.

**Esfuerzo cortante (τ):** Calculado mediante:

$$
τ = ρ * g * R * S
$$

donde *ρ* es la densidad, *g* la gravedad, *R* el radio hidráulico y *S* la pendiente. A mayor densidad, mayor capacidad erosiva.

**Presión intersticial (pfp):** Varía espacial y temporalmente, reduciendo la resistencia friccional cuando es alta, lo que incrementa la movilidad del flujo.

## Características hidráulicas

**Velocidad (u, en m/s):** La velocidad del flujo se puede descomponer en tres componentes {cite}`doyle_2019`:
   - Velocidad superficial (*usurf*): calculada a partir de grabaciones de video.
   - Velocidad de desplazamiento (*ur*): velocidad total del flujo.
   - Velocidad del cuerpo (*ub*): velocidad media en profundidad para una ubicación dada.
   
La velocidad del cuerpo *ub* se considera proporcional a la velocidad superficial mediante un factor de corrección *k*, que varía entre 0,7 y 0,9 {cite}`creutin_2018` y está definido por una distribución vertical de velocidades {cite}`cronin_1999`. La velocidad del frente del flujo se usa frecuentemente en ecuaciones, ya que la cabeza del flujo, rica en bloques, juega un papel crucial en la intensidad inicial del impacto sobre estructuras {cite}`lavigne_2004,thouret_2007`.

**Caudal (Q, en m³/s):** Es función de la velocidad media del flujo, su capacidad de erosionar y cargar material del lecho y las orillas del canal, y la geometría del canal. La descarga se expresa mediante:
   
$$
Q(ti + 1 - ti) = ½ (Ai * ubi + Ai+1 * ubi+1)
$$

donde *A* es el área mojada, *ub* la velocidad del cuerpo y *i* representa las mediciones individuales en intervalos Δt = ti+1 - ti {cite}`doyle_2019a`. La tasa de descarga varía en el tiempo y el espacio debido a los ciclos de carga (*bulking*) y descarga (*debulking*) de sedimentos.

**Profundidad del flujo (H, en m):** Puede ser de 4 a 5 veces el espesor del depósito. A mayor profundidad, mayor área expuesta y mayor altura alcanzada en las estructuras.

**Movilidad del flujo:** La velocidad, el caudal, la profundidad y la relación ancho/profundidad del canal influyen en la movilidad del flujo y determinan la distancia de recorrido.


## Mecanismos de disipación de energía en flujos
Las diferencias entre flujo plástico, turbulento, dispersivo y de Coulomb se refieren a los mecanismos físicos dominantes que controlan cómo se resiste o disipa la energía del flujo en movimiento. Estos mecanismos de disipación de energía en flujos de escombros (*debris flows*), que pueden coexistir o dominar en diferentes momentos o zonas del flujo.

| Tipo de flujo            | Ecuación general del esfuerzo cortante                                                                                 | Variables principales                                                                               |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| **Viscoso**              | $\tau = \mu , \dot{\gamma}$                                                                                          | $\mu$: viscosidad dinámica <br> $\dot{\gamma}$: tasa de cizalla                                 |
| **Plástico (Bingham)**   | $\tau = \tau_y + \mu_p , \dot{\gamma}$                                                                             | $\tau_y$: esfuerzo de fluencia <br> $\mu_p$: viscosidad plástica                              |
| **Turbulento**           | $\tau \propto \rho , u^2$                                                                                            | $\rho$: densidad <br> $u$: velocidad promedio                                                   |
| **Dispersivo (Bagnold)** | $\tau \propto \rho_s , d^2 , \dot{\gamma}^2$                                                                        | $\rho_s$: densidad de sólidos <br> $d$: tamaño de grano <br> $\dot{\gamma}$: tasa de cizalla |
| **Coulomb**          | $\tau = \sigma_n , \tan \phi + c$                                                                                   | $\sigma_n$: presión normal <br> $\phi$: ángulo de fricción interna <br> $c$: cohesión        |
| **Dilatante**            | $\tau = \sigma_n^{\text{eff}} , \tan \phi$ con $\sigma_n^{\text{eff}} = \sigma_n + \Delta \sigma_{\text{dil}}$ | $\Delta \sigma_{\text{dil}}$: aumento de presión normal por expansión volumétrica                |


### Flujo Friccional

* **Mecanismo Dominante:** La resistencia al movimiento es controlada por la fricción de contacto entre las partículas sólidas y entre estas y el lecho. Es una resistencia seca, independiente de la velocidad.
* **Explicación:** Este es el comportamiento más simple y se rige por la ley de fricción de Coulomb. La fuerza de resistencia es directamente proporcional a la fuerza normal (el peso del material perpendicular a la pendiente) y a un coeficiente de fricción ($\mu$ o $\tan\phi$). No hay términos que dependan de la velocidad de deformación (viscosidad) ni de la turbulencia. Es la física de un sólido deslizándose sobre otro.
* **Ejemplo Intuitivo:** Imagina empujar una caja de madera pesada sobre un suelo de cemento. La fuerza que necesitas para moverla depende del peso de la caja y de la rugosidad de las superficies, no de si la empujas lenta o rápidamente.
* **Tipo de flujo:** Es el modelo ideal para avalanchas de rocas secas, deslizamientos de bloques cohesivos, o la fase inicial de un deslizamiento donde el agua aún no juega un papel dominante.

### Flujo Viscoso

* **Mecanismo Dominante:** La resistencia al movimiento proviene de la fricción interna del propio fluido intersticial, que se opone a la deformación. La resistencia es directamente proporcional a la tasa de deformación ($\dot{\gamma}$).
* **Explicación:** En este régimen, no existe un umbral de esfuerzo para iniciar el movimiento (esfuerzo de cedencia); el flujo comienza con cualquier esfuerzo, por pequeño que sea. La relación entre el esfuerzo aplicado y la deformación resultante es lineal, y la pendiente de esa relación es la viscosidad. Un fluido más viscoso ofrece más resistencia.
* **Ejemplo Intuitivo:** Compara verter agua y verter miel. Ambos fluyen inmediatamente al inclinar el recipiente, pero la miel, al ser mucho más viscosa, fluye mucho más lentamente porque su "fricción interna" es mayor.
* **Tipo de flujo:** Este comportamiento es típico de flujos muy diluidos con bajo contenido de sólidos, o de la fase fluida dentro de una mezcla más compleja. Es la base para entender la resistencia del "líquido" que transporta los sólidos.

### Flujo Plástico

* **Mecanismo Dominante:** La resistencia está dominada por un esfuerzo de cedencia o fluencia ($\tau_y$). El material se comporta como un sólido rígido hasta que el esfuerzo aplicado supera este umbral, momento en el que empieza a fluir como un fluido viscoso.
* **Explicación:** La resistencia total en un flujo plástico tiene dos componentes: una resistencia inicial constante (el esfuerzo de cedencia) y una resistencia que aumenta con la velocidad de deformación (la parte viscosa). La presencia de arcillas y limos en la matriz fluida es la que genera esta cohesión y resistencia inicial.
* **Ejemplo Intuitivo:** La pasta de dientes. Puedes poner el tubo boca abajo y no se cae (resiste la gravedad como un sólido). Necesitas apretar con una fuerza mínima (superar el esfuerzo de cedencia) para que empiece a fluir. Una vez que fluye, se comporta como un líquido muy espeso.
* **Tipo de flujo:** Es el modelo clásico para flujos de lodo (*mudflows*) y flujos de detritos ricos en finos cohesivos, donde la matriz de lodo tiene la capacidad de mantener en suspensión a las partículas más grandes cuando el flujo se detiene.

### Flujo Turbulento

* **Mecanismo Dominante:** La resistencia al flujo y el transporte de partículas provienen de la energía caótica de los remolinos (eddies) en la fase fluida.
* **Explicación:** En lugar de un flujo suave y ordenado (laminar), el movimiento es caótico, con un intenso intercambio de momento dentro del propio fluido. Esta turbulencia es la que mantiene a los sedimentos en suspensión. La resistencia en este régimen no es lineal con la velocidad, sino que es proporcional al cuadrado de la velocidad. Por eso, los flujos turbulentos pueden ser tan erosivos y destructivos.
* **Ejemplo Intuitivo:** Un río de montaña durante una creciente aquí en Antioquia. El agua no fluye de forma ordenada; hierve con remolinos que son capaces de arrancar y transportar piedras y troncos.
* **Tipo de flujo:** Es el régimen típico de los flujos hiperconcentrados y los flujos de lodo más diluidos y rápidos, donde la concentración de sólidos no es tan alta como para que las interacciones entre granos dominen.

### Flujo Dispersivo

* **Mecanismo Dominante:** La resistencia es generada por las colisiones e interacciones mecánicas entre las partículas sólidas grandes.
* **Explicación:** En flujos con alta concentración de granos gruesos, el momento se transfiere principalmente por el choque de un grano con otro. Estas colisiones generan una "presión dispersiva" que tiende a separar las partículas y se opone al cizallamiento. Este es el famoso efecto *Bagnold*. Es un comportamiento puramente granular, no viscoso. El esfuerzo dispersivo es proporcional al cuadrado del tamaño del grano y al cuadrado de la tasa de cizalla.
* **Ejemplo Intuitivo:** Imagina una caja de balines densamente empacados. Si la agitas vigorosamente, los balines chocan constantemente entre sí y contra las paredes, generando una presión interna que se opone al movimiento.
* **Tipo de flujo:** Es el mecanismo dominante en flujos de detritos granulares y secos y en el frente de muchas avalanchas de rocas, donde los bloques más grandes interactúan directamente.

### Flujo Dilatante

* **Mecanismo Dominante:** La resistencia proviene de la **expansión volumétrica (dilatancia) del esqueleto granular** cuando se somete a un esfuerzo de cizalla.
* **Explicación Detallada:** Cuando un material granular está densamente empaquetado, las partículas no pueden simplemente deslizarse unas sobre otras. Para moverse, necesitan "montarse" unas sobre otras, lo que obliga al volumen total de la masa a expandirse. Este trabajo mecánico de expansión consume energía y se manifiesta como una resistencia adicional al cizallamiento. A menudo coexiste con la fricción de Coulomb y los esfuerzos dispersivos.
* **Ejemplo Intuitivo:** Una bolsa de café en grano bien apretada. Si intentas meter la mano, sientes una fuerte resistencia. Los granos están tan juntos que para moverse necesitan separarse, y tú tienes que hacer fuerza para crear ese espacio.
* **Tipo de flujo:** Este fenómeno es especialmente importante en la **fase de iniciación del movimiento** de materiales granulares densos, como arenas compactas o la base de un flujo de detritos denso. Puede explicar por qué se necesita un esfuerzo inicial mayor para poner en movimiento una masa densa.

| Tipo de flujo       | Mecanismo de disipación principal         | Forma de disipación de energía                                                                 |
|---------------------|-------------------------------------------|------------------------------------------------------------------------------------------------|
| **Viscoso (laminar)**   | Fricción interna (viscosidad)              | Calor generado por fricción molecular entre capas del fluido                                   |
| **Plástico (Bingham, HB)** | Ruptura de estructuras cohesivas          | Trabajo mecánico necesario para superar el esfuerzo de fluencia ($\tau_y$) → pérdida irreversible |
| **Turbulento**         | Agitación caótica del fluido               | Cascada de vórtices → fricción microscópica → disipación en calor                              |
| **Dispersivo (granular)** | Colisiones inelásticas entre partículas   | Fricción interpartícula, deformación, calor, sonido, reorganización granular                    |
| **Coulomb**        | Fricción basal seca                        | Pérdida por roce entre masa movilizada y superficie subyacente (dependiente de $\tan\phi$)     |
| **Dilatante**          | Expansión volumétrica por cizalla          | Trabajo contra presión normal para permitir expansión → disipación mecánica                    |


## Números adimensionales

Existen parámetros cuantitativos que ayudan a distinguir los regímenes de flujo (plástico, turbulento, dispersivo, Coulomb) en flujos de escombros, basados en conceptos de mecánica de fluidos, reología y dinámica granular. Estos números adimensionales permiten evaluar qué tipo de comportamiento físico domina en un flujo dado. 

**Número de Bingham (Bn)**. Evalúa la importancia del umbral de fluencia (flujo plástico) frente al esfuerzo viscoso. Útil para identificar flujos tipo Bingham o Herschel–Bulkley, típicos en ambientes tropicales ricos en finos cohesivos.

$Bn=\frac{𝜏_𝑦⋅𝐿}{𝜇⋅𝑉}$
​
 
$\tau_y$: esfuerzo de fluencia (Pa). $L$: escala característica (por ejemplo, espesor del flujo). $\mu$: viscosidad plástica (Pa·s). $V$: velocidad del flujo (m/s)

* Bn ≫ 1: flujo dominantemente plástico → el esfuerzo de fluencia es más importante que el esfuerzo viscoso.

* Bn ≪ 1: flujo más viscoso o inercial → la cohesión es despreciable frente a la viscosidad o inercia.


**Número de Reynolds (Re)**. Mide la relación entre fuerzas inerciales y viscosas. Indica si el flujo es laminar o turbulento. Útil para evaluar si es más adecuado un modelo viscoso vs. turbulento (por ejemplo, aplicar Voellmy con término cuadrático cuando Re es alto)

$Re=\frac{𝜌⋅𝑉⋅𝐿}{𝜇}$
​
 
$\rho$: densidad del flujo. $V$: velocidad. $L$: altura del flujo. $\mu$: viscosidad dinámica.

* Re < 1000: régimen laminar → modelos como Bingham, Herschel–Bulkley son más válidos.

* Re > 2000–4000: régimen turbulento → la disipación turbulenta domina, apropiado para flujos rápidos y diluidos.

**Número de Savage (Sav)**. Relaciona energía cinética de partículas con presiones de confinamiento. Detecta flujo dispersivo granular. Muy útil para distinguir entre núcleo fangoso (Sav bajo) y cabeza de bloques (Sav alto) en un debris flow.

$Sav=\frac{\dot{\gamma}⋅𝑑}{(𝑃/𝜌_𝑠)^{1/2}}$
​
 
$\dot{\gamma}$: tasa de deformación. $d$: tamaño medio de grano. $P$: presión normal (típicamente $\sim \rho g h$). $\rho_s$: densidad de sólidos.

* Sav ≫ 1: flujo dispersivo/granular inercial, choques frecuentes → aplicar modelo de Bagnold o término cuadrático dispersivo.

* Sav ≪ 1: partículas confinadas, sin dispersión → flujo cohesivo o tipo lodo.

**Número de Froude (Fr)**. Indica la relación entre velocidad y gravedad. Clasifica flujo como subcrítico, crítico o supercrítico. En flujos de escombros: Fr > 1 suele asociarse a frentes dispersivos y rápidos → apropiado usar Voellmy o dispersivo.

$Fr=\frac{𝑉𝑔⋅ℎ}{V}$
​
 
$V$: velocidad del flujo. $g$: gravedad. $h$: espesor del flujo

* Fr < 1: flujo subcrítico (lento, estable)

* Fr > 1: flujo supercrítico (rápido, dominado por inercia)


**Número de Stokes (St)**. Relaciona la inercia de una partícula con la viscosidad del medio. Valores altos indican dominio de inercia granular → dispersión; valores bajos → régimen viscoso.

$St=\frac{𝜌𝑠⋅𝑑^2⋅\dot{\gamma}}{𝜇}$


| Número adimensional                        | Fórmula                                               | Descripción                                                                                                      | Valores típicos o umbrales                                              |
|---------------------------------------------|-------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| **Número de Froude**                       | Fr = √(u / gH)                                       | Relación entre la fuerza inercial y la fuerza gravitacional                                                     | <1: domina la gravedad<br>>1: domina la inercia                        |
| **Número de Reynolds**                     | NRe = ρ₀H(gL)¹ᐟ² / µf                               | Relación entre la fuerza inercial y la fuerza viscosa                                                           | <500: flujo laminar<br>500–12,500: flujo transicional<br>>12,500: turbulento |
| **Número de difusión de presión de poros** | NP = (L/g)¹ᐟ² µf H² / (kE)                           | Relación entre la escala temporal del flujo y la difusión normal al talud de la presión positiva del fluido de poros | -                                                                       |
| **Número inercial**                        | I = √(γ δ ̇P / ρ₀)                                   | Relación entre el esfuerzo inercial y el esfuerzo de confinamiento                                              | 1 × 10⁻⁵ – 1 × 10⁻¹ *(rango típico en flujos de detritos naturales)* {cite}`jerolmack_2019` |
| **Número de Savage**                       | NS = (ρs – ρf) / ρs                                  | Relación entre el esfuerzo colisional y el esfuerzo por fricción                                                | <0.1: domina el esfuerzo por fricción<br>>0.1: domina el esfuerzo colisional |
| **Número de Bagnold**                      | NB = (1 – vs) / vs                                    | Relación entre el esfuerzo colisional y el esfuerzo viscoso                                                     | <40: domina el esfuerzo viscoso<br>>450: domina el esfuerzo colisional |
| **Número de fricción**                     | Nfric = (1 – vs) / vs · (ρs γ δ²) / µf               | Relación entre el esfuerzo por fricción y el esfuerzo viscoso                                                   | <100: domina el esfuerzo viscoso<br>>100: domina el esfuerzo por fricción |
