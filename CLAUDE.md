# CLAUDE.md

Guía para Claude Code (y para cualquier persona) que actualice este repositorio. **Cada cambio que se haga en el repositorio debe registrarse al final, en "Registro de cambios".**

## Qué es

Curso **Avenidas Torrenciales** (Universidad Nacional de Colombia, sede Medellín, Prof. Edier Aristizábal). Un solo repositorio contiene tres productos:

| Producto | Fuente en `main` | URL publicada |
|---|---|---|
| Página del curso (Jekyll, tema minimal) | `index.md`, `_config.yml`, programa en PDF | https://edieraristizabal.github.io/AvenidasTorrenciales/ |
| Diapositivas (Reveal.js) | `html/AvenidasTorrenciales.html` y sus recursos en `html/` | https://edieraristizabal.github.io/AvenidasTorrenciales/html/AvenidasTorrenciales.html |
| Libro (Jupyter Book 1.x) | `libro/` | https://edieraristizabal.github.io/AvenidasTorrenciales/libro/ |

Remoto: https://github.com/edieraristizabal/AvenidasTorrenciales

## Resumen del contenido

El curso de Avenidas Torrenciales está orientado para estudiantes de ingeniería que deseen adquirir conocimientos sobre métodos para la evaluación y zonificación de la susceptibilidad y amenaza por avenidas torrenciales. 
El curso es teórico - práctico, en el cual se hace una revisión detallada del estado del arte y las diferentes

El libro está escrito en MyST Markdown, sin código ejecutable, con la bibliografía en `libro/references.bib`. Las diapositivas incluyen videos en `html/videos/` (32 MB en total).

Contenido del libro (orden de `libro/_toc.yml`; el título es el primer encabezado de cada archivo):

- `intro`: Avenidas Torrenciales

**Parte I: Caracterización de las avenidas torrenciales**

- `01_Intro`: Introducción
- `02_Flujos`: Qué son las Avenidas torrenciales?
- `02_Entrainment`: Incorporación de sedimentos (Entrainment)
- `02_Hidrometeorologia`: La Lluvia como factor detonante

**Parte II: Reología y Dinámica**

- `03b_EvolucionModelos`: Evolución histórica de los modelos de flujos
- `03_Ecuaciones`: Ecuaciones de Saint-Venant y técnicas numéricas de solución
- `04_TiposFlujos`: Tipos de flujos
- `04_ModelosReologicos`: Modelos reológicos
- `05_DosFases`: Modelos de dos fases (granular-fluido):
- `07_Routing`: Modelos de propagacion

**Parte III: Modelos Numéricos**

- `08_Flow-R`: Flow-R
- `09_OpenLisem`: OpenLISEM
- `10_RandomWalk`: r.randomwalk
- `11_Predictor`: DebrisFlow Predictor
- `12_FlowPy`: Flow-Py
- `13_GMP`: Gravitational Process Path (GPP)
- `14_ravaflow`: r.avaflow
- `15_Grfin`: Grfin Tools
- `16_Iber_NNF`: Iber-NNF
- `17_FLO2D`: FLO-2D
- `18_HECRAS`: HEC-RAS

**Parte IV: Aplicación normativa en Colombia**

- `21_GuiaSGC`: Metodología del Servicio Geológico Colombiano

**Referencias**

- `19_Referencias`: Referencias

## Ramas: dónde hacer pull y push

**Siempre se trabaja en `main`.** Ese es el único lugar donde se edita, se hace `git pull` y `git push`, tanto para cambios de la página como de las diapositivas o del libro.

**Nunca editar ni hacer push a `gh-pages` a mano.** Esa rama la genera el workflow `.github/workflows/deploy.yml` en cada push a `main`: construye Jekyll en `_site/`, construye el libro y lo copia en `_site/libro/`, y publica todo en `gh-pages` con `force_orphan` (cualquier cambio manual en `gh-pages` se pierde en el siguiente despliegue).

GitHub Pages sirve un solo sitio por repositorio; por eso la página y el libro se combinan en el Action. En **Settings → Pages** la fuente debe ser *Deploy from a branch*, rama `gh-pages`, carpeta `/ (root)`.

Flujo normal:

```bash
git pull origin main
# ... editar ...
# ... agregar una línea al Registro de cambios de este archivo ...
git add -A
git commit -m "Descripción del cambio"
git push origin main      # el Action publica en 2 a 5 minutos (pestaña Actions)
```

## Estructura

```
.github/
    workflows/
.gitignore
Programa_AvenidasTorrenciales.pdf
Programa_AvenidasTorrenciales.tex
README.md
_config.yml
build/
html/
    .github/
    AvenidasTorrenciales.html
    AvenidasTorrenciales_old.html
    videos/
index.md
libro/
```

Material fuente que **no** se sube (libros guía, presentaciones originales, documentos con derechos): está en `C:\Users\edier\Documents\CATEDRA\AVENIDASTORRENCIALES`, fuera de este repositorio.

## Cómo actualizar

**Página del curso.** Editar `index.md`. Los enlaces internos deben ser relativos (`Programa.pdf`, `html/...`), nunca empezar por `/`, porque el sitio vive en `/AvenidasTorrenciales/`. El libro se enlaza como `https://edieraristizabal.github.io/AvenidasTorrenciales/libro/<capítulo>.html`. Si cambia la evaluación, actualizarla también en el programa (`.tex` + PDF) y en las diapositivas.

**Diapositivas.** Editar `html/AvenidasTorrenciales.html` directamente (Reveal.js; ecuaciones con MathJax). Previsualizar con `python -m http.server` desde la raíz y abrir `http://localhost:8000/html/AvenidasTorrenciales.html`.

**Libro.** Capítulos en MyST Markdown (`.md`) o notebooks (`.ipynb`) dentro de `libro/`; un capítulo nuevo debe agregarse a `libro/_toc.yml`; las citas `{cite:t}` / `{cite:p}` deben existir en el archivo `.bib` del libro.

- **El Action no ejecuta los notebooks** (`execute_notebooks: "off"`): publica las salidas guardadas en cada `.ipynb`. Si se cambia código, ejecutar el notebook localmente y guardarlo con sus salidas antes del commit.
- `libro/requirements.txt` contiene solo lo necesario para construir el libro; `libro/requirements-notebooks.txt` (si existe) lista las librerías para ejecutar los notebooks.
- Compilar localmente: `C:\Users\edier\.jbenv\Scripts\jupyter-book.exe build libro` y abrir `libro/_build/html/index.html`.

## Notas del libro (CLAUDE.md anterior del repositorio Libro_AvTorrenciales)

Lo siguiente proviene del CLAUDE.md del libro. Las rutas relativas se refieren ahora a `libro/`, y el libro se construye con `jupyter-book build libro` desde la raíz.

### Project Overview

This is a **Jupyter Book** for the course "Avenidas Torrenciales" (Torrential Flows/Debris Flows) taught at Universidad Nacional de Colombia sede Medellín. The book covers the science of debris flows, including hydrology, rheology, numerical modeling tools, and risk assessment — written in Spanish.

### Book Structure

The book uses **MyST Markdown** (`.md` files) as its source format. The table of contents is defined in [_toc.yml](_toc.yml) and book settings (title, author, bibtex, execute config) are in [_config.yml](_config.yml).

Chapter files are numbered by topic area:
- `01_*` — Introduction
- `02_*` — Fundamentals (flows, climate change, dating, interactions)
- `03_*` — Governing equations
- `04_*` — Rheology
- `05_*` — Two-phase flows
- `07_*` to `16_*` — Numerical modeling tools (Routing, Flow-R, OpenLISEM, r.randomwalk, Predictor, FlowPy, GMP, r.avaflow, Grfin, Iber/NNF)
- `20_*` — Risk assessment

To add a new chapter, create a `.md` file and add its filename (without extension) to the `chapters:` list in [_toc.yml](_toc.yml).

### Citations

References are managed via [references.bib](references.bib) (BibTeX). In-text citations use MyST syntax: `` {cite}`AuthorYear` ``. The bibliography is rendered by the `bibtex_bibfiles` setting in `_config.yml`.

### Content Conventions

- Each chapter begins with an HTML `<p>` credits line attributing sources.
- Math equations use standard LaTeX inline (`$...$`) and display (`$$...$$`) syntax, rendered via MathJax.
- Figures use MyST `{figure}` directives.
- The book language is Spanish; all chapter content and headings should be in Spanish.

### Notebook Execution

`_config.yml` sets `execute_notebooks: force`, meaning any `.ipynb` or MyST notebook with executable code cells will be re-run on every build. If a chapter contains computational examples (matplotlib/numpy plots), ensure the Python environment from `requirements.txt` is active before building.

## Preferencias del autor

- Proponer los cambios de contenido del curso y esperar aprobación antes de editarlos.
- Prosa en párrafos, español, sin rayas (em dash): usar comas o paréntesis.
- Las figuras nuevas se buscan primero en los tableros de Pinterest del autor.

## Pendientes conocidos

- Crear en GitHub el repositorio vacío `edieraristizabal/AvenidasTorrenciales` (sin README) y hacer el primer `git push -u origin main`; luego en Settings → Pages elegir la rama `gh-pages`.
- Borrar el repositorio viejo `Libro_AvTorrenciales` cuando se verifique el libro nuevo.
- En `libro/` hay archivos que parecen temporales del trabajo anterior (`out.txt`, `zz_permtest.txt`, `_g2021.txt`) y listas de trabajo (`missing_citations.txt`, `missing_context.txt`); revisar si se eliminan.
- Advertencias menores del libro (contenido, no corregidas sin aprobación): saltos de nivel de encabezado (H1 a H3, H2 a H4) en varios capítulos de modelos.
- `html/AvenidasTorrenciales_old.html` es una versión anterior de la presentación.

## Registro de cambios

Cada cambio al repositorio se anota aquí, el más reciente primero, con el formato `- **AAAA-MM-DD**: qué se cambió y por qué (archivos principales).` Esta sección es la memoria del curso entre sesiones: leerla antes de empezar a trabajar.

- **2026-10-24**: CLAUDE.md creado con resumen del contenido, estructura, flujo de ramas, pendientes y este registro; el CLAUDE.md anterior del libro se integra en "Notas del libro" y `libro/CLAUDE.md` queda como un enlace a este archivo.
- **2026-10-24**: Creación del repositorio unificado: la carpeta de la página (sin git hasta ahora) se inicializa con rama `main`; el libro (antes `Libro_AvTorrenciales`) se copia a `libro/` con `execute_notebooks: off`; workflow `deploy.yml`; `_config.yml` con `baseurl: /AvenidasTorrenciales`; enlaces de `index.md` relativos y los enlaces al libro en `index.md` y `html/AvenidasTorrenciales.html` apuntan a `/AvenidasTorrenciales/libro/`; se quitaron de `.gitignore` las líneas `.git` y `.gitignore`.
- **2026-10-24**: Commit de respaldo en `Libro_AvTorrenciales` de los 252 cambios locales pendientes antes de unificar.
