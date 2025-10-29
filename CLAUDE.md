# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Descripción del Proyecto

Repositorio académico para la Tarea 3 del curso **Cadenas de Markov 2025-II** de la Maestría en Actuaría y Finanzas (Universidad Nacional de Colombia). Implementa simulaciones de:
- **Modelo Hard-Core**: Configuraciones de partículas en rejillas donde ninguna pareja de partículas puede ser adyacente
- **Modelo q-Coloraciones**: Coloraciones propias de grafos donde vértices adyacentes tienen colores distintos

Ambos modelos utilizan **Gibbs Sampler** con cadenas de Markov para generar muestras de distribuciones estacionarias.

## Estructura del Proyecto

```
Cadenas-de-Markov/
├── notebooks/
│   ├── tarea_markov_3.ipynb             # Notebook original (3-3.5 horas)
│   └── tarea_markov_3_optimizado.ipynb  # ⚡ Versión optimizada (30-90 seg)
├── images/                      # Visualizaciones generadas (gitignored)
├── docs/                        # Documento LaTeX del reporte
│   ├── main.tex                # Documento principal
│   ├── config/
│   │   ├── packages.tex        # Paquetes LaTeX
│   │   ├── commands.tex        # Comandos personalizados
│   │   └── doc_style.tex       # Estilos del documento
│   └── content/
│       ├── Marco_teorico.tex
│       ├── Implementacion.tex
│       └── Resultados.tex
└── CLAUDE.md                    # Este archivo
```

## ⚡ Notebook Optimizado (tarea_markov_3_optimizado.ipynb)

**IMPORTANTE**: Existe una versión optimizada del notebook con mejoras dramáticas de rendimiento.

### Mejoras Implementadas

#### Fase 1: Optimizaciones Rápidas
- ✅ **dtype optimizado**: `int8` en lugar de `int64` (8x menos memoria)
- ✅ **np.random.randint**: Reemplaza `np.random.choice([0,1])` (2-3x más rápido)
- ✅ **Verificación de vecinos**: Short-circuit evaluation (2-3x más rápido)
- ✅ **Set para puntos_guardado**: O(1) lookup en lugar de O(n)
- ✅ **Reutilización de objetos**: No crea sets/listas en cada iteración

#### Fase 2: Vectorización
- ✅ **Actualización bipartita**: Patrón de tablero de ajedrez vectorizado (20-30x más rápido)
- ✅ **Validación vectorizada**: Detección de colisiones con operaciones NumPy (10-20x más rápido)
- ✅ **Funciones optimizadas**:
  - `gibbs_sampler_hardcore_optimizado()` - Vectorización completa
  - `_actualizar_hardcore_vectorizado()` - Actualización sin conflictos
  - `es_configuracion_factible_hardcore_vectorizada()` - Validación vectorizada
  - `es_coloracion_propia_vectorizada()` - Validación vectorizada

#### Fase 3: Paralelización
- ✅ **Generación paralela de muestras**: `multiprocessing.Pool` (6-8x más rápido con 8 cores)
- ✅ **Reproducibilidad garantizada**: Seeds determinísticas `base_seed + i`
- ✅ **Funciones paralelas**:
  - `generar_muestras_hardcore_paralelo()` - 500 muestras en paralelo
  - `generar_muestras_coloracion_paralelo()` - Coloraciones en paralelo
  - `_generar_muestra_hardcore_wrapper()` - Wrapper con seed única
  - `_generar_muestra_coloracion_wrapper()` - Wrapper para q-coloraciones

### Mejora de Rendimiento

| Operación | Original | Optimizado | Speedup |
|-----------|----------|------------|---------|
| Simulación única (K=20) | ~15-20 seg | ~0.5-1 seg | **~20-30x** |
| 500 muestras (K=10) | ~30-40 min | ~2-3 min | **~15-20x** |
| Análisis convergencia | ~35-40 min | ~2-3 min | **~15-20x** |
| **Notebook completo** | **3-3.5 horas** | **30-90 seg** | **~100-400x** |

### Uso del Notebook Optimizado

```bash
# Iniciar Jupyter y abrir notebook optimizado
jupyter notebook notebooks/tarea_markov_3_optimizado.ipynb

# O ejecutar desde línea de comandos
jupyter nbconvert --execute --to notebook --inplace notebooks/tarea_markov_3_optimizado.ipynb
```

**Nota**: El notebook optimizado genera resultados estadísticamente idénticos al original (mismas medias y desviaciones estándar con seed=42).

### Cuándo Usar Cada Versión

**Usar `tarea_markov_3.ipynb` (original)**:
- Para entender el algoritmo básico sin optimizaciones
- Para comparar rendimiento (benchmarking)
- Si no tienes múltiples cores disponibles

**Usar `tarea_markov_3_optimizado.ipynb` (recomendado)**:
- Para ejecutar el notebook completo en tiempo razonable
- Para experimentar con diferentes parámetros rápidamente
- Para análisis de producción con muchas muestras
- Para aprovechar múltiples cores CPU

## Entorno de Desarrollo

### Stack Tecnológico
- **Python 3.13** con Anaconda
- **Jupyter Notebook** para análisis interactivo
- **Librerías principales**:
  - `numpy 2.1.3` - Operaciones numéricas y matrices
  - `matplotlib 3.10.0` - Visualizaciones
  - `seaborn 0.13.2` - Gráficos estadísticos avanzados
  - `pandas` - Análisis de datos

### Configuración del Entorno
```bash
# Activar ambiente conda (si aplica)
conda activate base

# Instalar dependencias (si es necesario)
conda install numpy matplotlib seaborn pandas jupyter
```

## Comandos Comunes

### Jupyter Notebook
```bash
# Iniciar servidor Jupyter
jupyter notebook

# Ejecutar notebook sin abrir navegador
jupyter notebook --no-browser --port=8888

# Convertir notebook a script Python
jupyter nbconvert --to script notebooks/tarea_markov_3.ipynb

# Ejecutar notebook desde línea de comandos
jupyter nbconvert --execute --to notebook --inplace notebooks/tarea_markov_3.ipynb
```

### LaTeX (Generación del Reporte)
```bash
# Compilar documento LaTeX
cd docs
pdflatex main.tex
pdflatex main.tex  # Segunda pasada para referencias

# Limpiar archivos auxiliares
rm -f *.aux *.log *.out *.toc
```

### Sincronización (Comandos Personalizados)
El proyecto usa comandos personalizados para sincronización con Google Drive:
- `/sync-push-ekg` - Subir cambios (git commit + push + sync data/ e images/ a GDrive)
- `/sync-pull-ekg` - Bajar cambios (git pull + sync data/ e images/ desde GDrive)

**Nota**: Las carpetas `data/` e `images/` están en `.gitignore` y se sincronizan con Google Drive usando `rclone` (remote: `gdriveekg:ekg-sync/Cadenas-de-Markov/`).

## Arquitectura del Código

### Notebook Principal (`tarea_markov_3.ipynb`)

El notebook implementa dos sistemas de simulación con arquitectura modular:

#### 1. Modelo Hard-Core
**Función principal**: `gibbs_sampler_hardcore(K, T, config_inicial=None)`
- Simula configuraciones de partículas en rejilla K×K durante T iteraciones
- Restricción: ningún par de partículas puede ser adyacente
- Algoritmo:
  1. Para cada vértice (i,j), verificar vecinos (arriba, abajo, izquierda, derecha)
  2. Si algún vecino tiene partícula → forzar config[i,j] = 0
  3. Si no hay vecinos ocupados → elegir uniformemente {0, 1}
  4. Repetir para todos los vértices durante T iteraciones

**Funciones auxiliares**:
- `es_configuracion_factible_hardcore(config)` - Valida restricción Hard-Core
- `visualizar_configuracion_hardcore(config, titulo)` - Renderiza rejilla con matplotlib

**Parámetros típicos**:
- K ∈ {3, 10, 20} - Tamaños de rejilla explorados
- T = 10,000 - Iteraciones para convergencia
- num_muestras = 500 - Muestras independientes para estimación estadística

#### 2. Modelo q-Coloraciones
**Función principal**: `gibbs_sampler_coloracion(K, q, T, config_inicial=None)`
- Asigna q colores a vértices de rejilla K×K
- Restricción: vértices adyacentes deben tener colores diferentes
- Algoritmo:
  1. Para cada vértice (i,j), obtener colores de vecinos
  2. Determinar colores válidos: {0, ..., q-1} \ {colores_vecinos}
  3. Elegir uniformemente un color válido
  4. Repetir durante T iteraciones

**Funciones auxiliares**:
- `es_coloracion_propia(config)` - Valida restricción de coloración
- `visualizar_coloracion(config, q, titulo)` - Renderiza rejilla con colormap

**Parámetros típicos**:
- q ∈ {2, 3, 5} - Número de colores explorado
- K ∈ {3, 10, 20}
- T = 10,000

#### Análisis Estadístico
El notebook incluye:
- **Convergencia**: Análisis de distribuciones en t={100, 1000, 5000, 10000}
- **Estimación**: Media y desviación estándar de partículas/colores (500+ muestras)
- **Visualizaciones**: Histogramas, boxplots, evolución temporal

### Salidas Generadas
Las visualizaciones se guardan en `images/`:
- `hardcore_evolucion_K{K}.png` - Evolución temporal Hard-Core
- `hardcore_histograma_particulas.png` - Distribución de partículas
- `hardcore_convergencia.png` - Análisis de convergencia
- `coloracion_evolucion_K{K}_q{q}.png` - Evolución q-coloraciones
- `coloracion_distribucion_colores.png` - Distribución por color
- `comparativo_general.png` - Comparación de modelos

## Consideraciones Importantes

### Tiempos de Ejecución

**Notebook Original (`tarea_markov_3.ipynb`)**:
- **Simulación única** (K=20, T=10,000): ~15-20 segundos
- **500 muestras** (K=10): ~30-40 minutos
- **Notebook completo**: ~3-3.5 horas de ejecución

**Notebook Optimizado (`tarea_markov_3_optimizado.ipynb`)**:
- **Simulación única** (K=20, T=10,000): ~0.5-1 segundo (**~20-30x más rápido**)
- **500 muestras** (K=10): ~2-3 minutos (**~15-20x más rápido**)
- **Notebook completo**: ~30-90 segundos (**~100-400x más rápido**)

### Limitaciones Conocidas
1. **Densidad Hard-Core**: El algoritmo observa densidad ~0.24, menor a la teórica (~0.368). Posible causa: inicialización vacía favorece configuraciones subestimadas.
2. **Verificación q-coloraciones**: La función `es_coloracion_propia()` reporta `False` para K≥10, posible bug en implementación para rejillas grandes.

### Reproducibilidad
- Semilla aleatoria: `np.random.seed(42)` establecida al inicio
- Todas las simulaciones usan la misma semilla para reproducibilidad

## Flujo de Trabajo Típico

### Modificar Simulaciones
1. Editar parámetros en el notebook (K, T, num_muestras, q)
2. Re-ejecutar celdas relevantes
3. Verificar que las imágenes se regeneren en `images/`
4. Usar `/sync-push-ekg` para subir cambios y nuevas imágenes

### Actualizar Reporte LaTeX
1. Modificar archivos `.tex` en `docs/content/`
2. Compilar: `cd docs && pdflatex main.tex && pdflatex main.tex`
3. Commit y push cambios

### Sincronización Completa
```bash
# Bajar últimos cambios y archivos pesados
/sync-pull-ekg

# Hacer modificaciones...

# Subir todos los cambios
/sync-push-ekg
```

## Contexto Académico

**Curso**: Cadenas de Markov 2025-II
**Profesor**: Freddy Hernández
**Integrantes**:
- José Miguel Acuña Hernández
- Andrés Puertas Londoño
- Guillermo Murillo Tirado

**Objetivo**: Implementar Gibbs sampler para muestrear distribuciones estacionarias de cadenas de Markov en modelos combinatorios (Hard-Core y q-coloraciones), analizar convergencia y propiedades estadísticas.
