<p style="font-size:11px;"><em><strong>Créditos</strong>: El contenido de este capítulo se apoya en el manual de usuario 2D de HEC-RAS del Cuerpo de Ingenieros del Ejército de EE. UU. {cite}`usace_hecras_2023`, en Gibson et al. {cite}`gibson_2008` (aplicación de reologías cuadrática y de Bingham en HEC-RAS) y en estudios comparativos recientes con FLO-2D {cite}`srk_tailings_2022,hinzmann_2023_stsophia,pacheco_2025_esteroalfonso`.</em></p>

# HEC-RAS

**HEC-RAS** (*Hydrologic Engineering Center's River Analysis System*) es un modelo hidráulico de código gratuito desarrollado por el Cuerpo de Ingenieros del Ejército de los Estados Unidos (USACE), originalmente concebido para el análisis unidimensional de flujo en ríos. Desde su versión 5, incorpora un módulo bidimensional de aguas someras (2D) que resuelve el flujo en llanuras de inundación, y desde versiones más recientes incluye un módulo de **flujo no newtoniano (*Mud and Debris Flow*, HEC-RAS-MDF)** que permite representar flujos de detritos, lodos y lahares mediante reologías viscoplásticas, en un desarrollo conceptualmente análogo al de Iber-NNF (véase el capítulo [Iber-NNF](16_Iber_NNF.md)).

### Componentes

El módulo 2D de HEC-RAS trabaja sobre mallas computacionales **estructuradas o no estructuradas**, lo que permite el uso de líneas de ruptura (*break-lines*) para representar con precisión bermas, diques, canales de conducción y otras estructuras lineales del terreno, una ventaja frente a la malla estrictamente regular de FLO-2D {cite}`usace_hecras_2023`.

Para la resolución de las ecuaciones de gobierno, HEC-RAS ofrece distintos esquemas numéricos que el usuario puede seleccionar según el balance deseado entre precisión física y costo computacional:

- **Onda difusiva 2D**: análoga a la de FLO-2D, computacionalmente más económica pero con menor fidelidad física en frentes de flujo rápidos.
- **SWE-ELM** (*Shallow Water Equations - Eulerian-Lagrangian Method*): un esquema híbrido que ofrece un compromiso entre estabilidad numérica y tiempo de cómputo.
- **SWE-EM** (*Shallow Water Equations - Eulerian Method*): resuelve el conjunto completo de ecuaciones de conservación de masa y momento (aguas someras completas), siendo el esquema que mejor conserva físicamente la cantidad de movimiento, aunque requiere pasos de tiempo más pequeños y, por tanto, mayor tiempo de cómputo {cite}`srk_tailings_2022`.

El módulo **MDF** (no newtoniano) extiende estos solucionadores incorporando términos de fricción viscoplástica en la pendiente de fricción, siguiendo formulaciones cuadráticas y de Bingham análogas a las descritas en el capítulo de [modelos reológicos](04_ModelosReologicos.md) {cite}`gibson_2008`. Esto permite representar tanto la fase dinámica del flujo como su cese y depositación, de forma similar a los modelos reológicos disponibles en Iber-NNF.

### Requisitos de Datos

- Modelo digital de terreno (DEM), idealmente derivado de levantamientos LiDAR de alta resolución.
- Malla computacional, con líneas de ruptura opcionales para representar estructuras lineales relevantes (diques, bermas, canales).
- Coeficiente de rugosidad de Manning para el componente hidráulico convencional.
- Parámetros reológicos del módulo MDF: esfuerzo de fluencia, viscosidad plástica y, según el esquema, coeficientes adicionales de resistencia turbulenta.
- Condiciones de frontera: hidrograma de caudal de entrada y, para el módulo MDF, una concentración de sedimentos que —a diferencia de FLO-2D— generalmente debe especificarse como valor **constante** durante la simulación, en lugar de acoplarse dinámicamente a un hidrograma de sedimentos {cite}`srk_tailings_2022`.

### Salidas

Los resultados de HEC-RAS se visualizan mediante el módulo **RAS Mapper**, que permite comparar múltiples escenarios o planes de simulación de forma simultánea y exportarlos directamente como shapefiles o rásteres georreferenciados, una ventaja operativa relevante frente al visor de plano único de FLO-2D. Las variables de salida incluyen profundidad, velocidad, elevación de la superficie libre, y —en el módulo MDF— variables adicionales asociadas a la reología no newtoniana, como el esfuerzo cortante y la extensión del depósito final.

### Calibración y Evaluación

- **Rotura de presas de relaves**: SRK Consulting {cite}`srk_tailings_2022` comparó los tres esquemas numéricos de HEC-RAS con FLO-2D para una falla hipotética por sobrevertimiento en una presa de relaves, encontrando tiempos de cómputo de 4,58 h (onda difusiva), 1,15 h (SWE-ELM) y 1,85 h (SWE-EM) para un escenario de 24 h, frente a 0,33 h de FLO-2D; el esquema de onda difusiva de HEC-RAS proyectó además un área de inundación considerablemente más extensa y dispersa que los esquemas de conservación de momento.
- **Flujo de detritos de St. Sophia, California (2003)**: Hinzmann {cite}`hinzmann_2023_stsophia` encontró que el módulo HEC-RAS-MDF produjo calados (3,0–5,0 m) y velocidades (≈3,6 m/s) consistentes con el mapeo geomorfológico post-evento y los testimonios de testigos, en clara ventaja frente a la sobreestimación de profundidades y velocidades obtenida con FLO-2D para el mismo evento.
- **Aluvión del Estero San Alfonso, Chile (2017)**: Pacheco, Martínez y Cuevas {cite}`pacheco_2025_esteroalfonso` encontraron velocidades consistentes entre HEC-RAS 2D y FLO-2D (diferencias menores al 10 %), aunque HEC-RAS proyectó sistemáticamente áreas de inundación más extensas que FLO-2D.

En síntesis, HEC-RAS ofrece mayor flexibilidad de malla, mayor rigor físico en sus esquemas de conservación completa de momento y herramientas de comparación de escenarios más versátiles, a cambio de mayores tiempos de cómputo y de una configuración del módulo MDF algo menos flexible que la de FLO-2D en cuanto a la variación temporal de la concentración de sedimentos. Al ser de código gratuito y contar con soporte técnico continuo del USACE, se ha convertido en una alternativa cada vez más utilizada frente a FLO-2D en la práctica profesional reciente.
