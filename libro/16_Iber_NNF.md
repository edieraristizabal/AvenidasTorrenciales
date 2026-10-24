<p style="font-size:11px;"><em><strong>Créditos</strong>: El contenido de este capítulo ha sido tomado y adaptado de Sanz-Ramos & Bladé {cite}`sanzramos_blade_2025a` (manual de referencia del módulo Iber-NNF) y de Sanz-Ramos et al. {cite}`sanzramos_iber_2025`.</em></p>

# Iber-NNF

**Iber** es un modelo hidrodinámico bidimensional de código abierto, desarrollado originalmente para la simulación de flujo en lámina libre en ríos y estuarios {cite}`blade_iber_2014`. Desde su primera versión en 2010, su ámbito de aplicación se ha ampliado progresivamente hasta incluir procesos morfodinámicos, transporte de sedimentos y contaminantes, dinámica de madera flotante, erosión de suelos, drenaje urbano y ecohidráulica. A partir de la versión 3, Iber incorpora **Iber-NNF** (*Non-Newtonian Flows*), un módulo específico para la simulación de flujos someros no newtonianos: avalanchas de nieve densas, propagación de relaves mineros, lahares y flujos hiperconcentrados (por ejemplo, flujos cargados de madera) {cite}`sanzramos_blade_2025a`.

A diferencia del capítulo anterior, centrado en los fundamentos de los modelos reológicos (véase [Modelos reológicos](04_ModelosReologicos.md)), este capítulo describe **cómo Iber-NNF implementa dicha física** dentro de un esquema numérico de volúmenes finitos.

## Ecuaciones de gobierno

Iber-NNF resuelve, al igual que Iber, las ecuaciones de aguas someras promediadas en profundidad (2D-SWE) en un sistema de coordenadas cartesianas, adaptadas para incorporar la física no newtoniana como términos de fricción y para permitir pendientes pronunciadas:

**Ecuación de continuidad:**

$$\frac{\partial h}{\partial t}+\frac{\partial q_{x}}{\partial x}+\frac{\partial q_{y}}{\partial y}=0$$

**Ecuaciones de cantidad de movimiento:**

$$\frac{\partial q_{x}}{\partial t}+\frac{\partial}{\partial x}\left(\frac{q_{x}^{2}}{h}+g'k_{p}\frac{h^{2}}{2}\right)+\frac{\partial}{\partial y}\left(\frac{q_{x}q_{y}}{h}\right)=g'h(S_{o,x}-S_{f,x})$$

$$\frac{\partial q_{y}}{\partial t}+\frac{\partial}{\partial x}\left(\frac{q_{x}q_{y}}{h}\right)+\frac{\partial}{\partial y}\left(\frac{q_{y}^{2}}{h}+g'k_{p}\frac{h^{2}}{2}\right)=g'h(S_{o,y}-S_{f,y})$$

donde $h$ es la profundidad del flujo; $q_x$, $q_y$ son las componentes del caudal específico; $g' = g\cos^2\theta$ es la gravedad corregida por la pendiente del terreno (relevante en cauces y laderas empinadas); $k_p$ es un factor de corrección de presión no hidrostática; $S_{o,x}$, $S_{o,y}$ son las componentes de la pendiente del lecho; y $S_{f,x}$, $S_{f,y}$ son las componentes de la pendiente de fricción, calculada según el modelo reológico seleccionado a partir de $\tau = \rho g h S_f$.

Un desafío numérico central es garantizar el balance entre los términos de presión, la pendiente del lecho y la fricción, de forma que el flujo se detenga correctamente en pendientes pronunciadas y topografías complejas, sin generar movimientos artificiales. Para ello, Iber-NNF utiliza un esquema de discretización *upwind* que asegura este balance incluso en presencia de fricción no lineal y dependiente de la velocidad {cite}`sanzramos_blade_2025a`.

## Modelos reológicos implementados

La versión actual de Iber-NNF incorpora **ocho modelos reológicos**, todos ellos aplicables a la modelación de lahares excepto el modelo de Bartelt (concebido para avalanchas de nieve con fuerzas de cohesión):

| Modelo | Pendiente de fricción $S_f$ | Parámetros de calibración |
|---|---|---|
| Manning {cite}`chow_openchannel_1959` | $\dfrac{n^2 v^2}{h^{4/3}}$ | $n$ |
| Viscoso | $\dfrac{n^2 v}{h^{3}}$ | $n$ |
| Dilatante | $\dfrac{n^2 v}{h^{2}}$ | $n$ |
| Bingham simplificado {cite}`bingham_investigation_1916` | $\dfrac{1}{\rho g h}\left(\omega\tau_y+3\dfrac{\mu_B v}{h}\right)$ | $\tau_y$, $\mu_B$ |
| Voellmy {cite}`voellmy_lawinen_1955` | $\dfrac{v^2}{\xi h}+\mu$ | $\xi$, $\mu$ |
| Bartelt (avalanchas de nieve) | — | cohesión, $\mu$, $\xi$ |
| O'Brien y Julien (cuadrático) {cite}`obrien_julien_1988` | $\dfrac{\tau_y}{\rho g h}+\dfrac{K\mu_B v}{8\rho g h^2}+\dfrac{n^2 v^2}{h^{4/3}}$ | $\tau_y$, $\mu_B$, $n$, $K$ |
| Herschel–Bulkley {cite}`herschel_bulkley_1926` | $\dfrac{1}{\rho g h}\left(\tau_y+k\left(\dfrac{v}{h}\right)^{\alpha}\right)$ | $\tau_y$, $k$, $\alpha$ |

Los parámetros de calibración de cada modelo son la densidad del flujo ($\rho$), el coeficiente de Manning ($n$), el esfuerzo de cedencia ($\tau_y$), la viscosidad dinámica ($\mu_B$), el coeficiente de fricción de Coulomb ($\mu$), el coeficiente de fricción turbulenta ($\xi$), un parámetro de resistencia ($K$), un parámetro de consistencia ($k$) y la potencia de corte ($\alpha$). El modelo de Coulomb puro, usado con frecuencia en la literatura de lahares, puede reproducirse dentro de Iber-NNF haciendo $\xi \to \infty$ en el modelo de Voellmy, eliminando así el término viscoso-turbulento {cite}`sanzramos_iber_2025`.

Los fundamentos físicos, ventajas y limitaciones de cada uno de estos modelos se describen en detalle en el capítulo [Modelos reológicos](04_ModelosReologicos.md); este capítulo se centra en su implementación y desempeño dentro de Iber.

## Incorporación de sedimento (entrainment)

Iber-NNF incluye formulaciones de incorporación de material del lecho (*entrainment*), un proceso especialmente relevante para lahares y avalanchas de nieve densas, donde el volumen del flujo puede crecer sustancialmente a lo largo del recorrido {cite}`sanzramos_blade_2025a`:

- **Modelo de velocidad:** $E = K_u(u - u_{crit})$ cuando $u > u_{crit}$.
- **Modelo de altura:** función de la profundidad de la cubierta de nieve o sedimento disponible, con límites superiores de incorporación.
- **Modelo de velocidad cuadrática:** $E = K_u(u^2 - u_{crit}^2)$.
- **Modelo de esfuerzo cortante basal:** $E = K_\tau(\tau - \tau_{crit})$.

## Arquitectura del software

Iber-NNF está completamente integrado en la interfaz gráfica de Iber y se activa como un complemento (*Iber tools >> Plug-ins*). En su versión actual funciona como un módulo independiente, sin acoplamiento directo con otros módulos de cálculo de Iber (sedimentos, calidad de agua, etc.). Además de las variables hidrodinámicas estándar (profundidad, velocidad, cota de la superficie libre), Iber-NNF calcula variables específicas para la evaluación de amenaza, como la pendiente del terreno, la presión de impacto dinámica y sus valores máximos durante la simulación {cite}`sanzramos_blade_2025a`. Las futuras versiones del módulo contemplan la incorporación de cómputo paralelo en GPU, siguiendo el mismo enfoque ya utilizado en los módulos de transporte de sedimentos y hábitat de Iber, con aceleraciones reportadas de más de 100 veces {cite}`sanzramos_iber_2025`.
