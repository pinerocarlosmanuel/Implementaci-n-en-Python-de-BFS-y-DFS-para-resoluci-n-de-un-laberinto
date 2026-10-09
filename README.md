# Búsqueda en Laberinto: BFS vs DFS

Implementación en Python de dos algoritmos de búsqueda sin información — **BFS** (Búsqueda en Amplitud) y **DFS** (Búsqueda en Profundidad) — para resolver un laberinto representado como una cuadrícula, con visualización gráfica y comparación cuantitativa de métricas.

Trabajo independiente de Inteligencia Artificial. Notebook: `FTL1_Carlos_Piñero_laberinto.ipynb`.

## Formalización del problema

### Espacio de estados

- **Estado**: tupla `(fila, columna)` que representa la posición del robot en la cuadrícula.
- **Estado inicial**: coordenadas de la celda con valor `2` (marcada como `S`).
- **Estado objetivo**: coordenadas de la celda con valor `3` (marcada como `G`).
- **Acciones**: `A = {ARRIBA, ABAJO, IZQUIERDA, DERECHA}` (4-conectividad, sin diagonales).

### Función de transición

`resultado(estado, acción) → nuevo_estado`

Dado un estado `s = (f, c)` y una acción `a ∈ A`:

1. Se calcula la posición candidata según la acción (`(f-1, c)`, `(f+1, c)`, `(f, c-1)` o `(f, c+1)`).
2. Se valida que esté dentro de los límites de la matriz y que no sea una pared (valor `1`).
3. Si es válida, se retorna el nuevo estado; si no, la acción no es aplicable.

### Función de costo

- Costo uniforme: `costo(s, a, s') = 1` para toda transición válida.
- El costo de un camino equivale a su longitud en pasos.

### Propiedades del espacio de búsqueda

- **Finito**: número de estados ≤ `filas × columnas`.
- **Determinista**: cada acción lleva a un único sucesor.
- **Totalmente observable**: el agente conoce el laberinto completo.
- **Estático**: el entorno no cambia durante la búsqueda.

## Estructura del notebook

| Sección | Contenido |
|---|---|
| 1. Formalización del problema | Espacio de estados, transición, costo y propiedades |
| 2. Configuración y funciones auxiliares | Laberinto 9×10, `es_valido()`, `obtener_sucesores()`, `reconstruir_camino()` |
| 3. BFS (iterativo) | `bfs()` con cola FIFO (`collections.deque`) |
| 4. DFS (recursivo) | `dfs()` / `dfs_recursivo()` con backtracking y límite de recursión |
| 5. Visualización | `visualizar_laberinto()` con matplotlib (laberinto original, camino BFS y camino DFS) |
| 6. Tabla comparativa | Métricas: longitud, nodos explorados, optimalidad, complejidades |
| 7. Análisis y conclusiones | Comparación cualitativa y recomendaciones prácticas |

## Representación del laberinto

El laberinto es una matriz NumPy `int` de 9×10 con los siguientes códigos:

| Valor | Significado | Color en la visualización |
|---|---|---|
| `0` | Camino libre | Blanco |
| `1` | Pared | Negro |
| `2` | Inicio (`S`) | Verde |
| `3` | Objetivo (`G`) | Rojo |
| `4` | Celda del camino solución | Amarillo (dorado) |

El orden fijo de exploración de acciones es: **ARRIBA → ABAJO → IZQUIERDA → DERECHA**.

## Requisitos previos

- Python ≥ 3.10 (probado en Python 3.12)
- `pip`

## Instalación y ejecución

```bash
# 1. Crear el entorno virtual
python3 -m venv venv

# 2. Activarlo (Linux/macOS)
source venv/bin/activate
# Activar en Windows:
# venv\Scripts\activate

# 3. Instalar dependencias
pip install -r requirements.txt

# 4. Abrir el notebook
jupyter notebook FTL1_Carlos_Piñero_laberinto.ipynb
```

Alternativa para ejecutar sin abrir Jupyter:

```bash
jupyter nbconvert --to notebook --execute FTL1_Carlos_Piñero_laberinto.ipynb --output ejecutado.ipynb
```

## Algoritmos implementados

### BFS — Búsqueda en Amplitud (iterativa)

- Utiliza una **cola FIFO** (`deque`) y un conjunto de `visitados`.
- Expande por capas de profundidad creciente: todos los nodos a distancia `k` se expanden antes que los de distancia `k+1`.
- **Garantiza el camino más corto** en grafos con costo uniforme.
- Complejidad espacial: `O(b^d)` (almacena toda la frontera).

### DFS — Búsqueda en Profundidad (recursiva)

- Implementada con **recursión y backtracking**, marcando los nodos visitados al expandirlos.
- `sys.setrecursionlimit(10000)` como protección para laberintos grandes.
- **No garantiza optimalidad**: puede encontrar una solución larga antes que la corta.
- Complejidad espacial: `O(d)` (solo la pila de llamadas).

Ambos algoritmos reconstruyen el camino final mediante un diccionario de `padres`.

## Resultados obtenidos

Laberinto de prueba 9×10 (inicio `(1, 1)`, objetivo `(7, 8)`):

| Métrica | BFS | DFS |
|---|---|---|
| Longitud del camino (pasos) | 19 | 19 |
| Nodos explorados (estados visitados) | 31 | 26 |
| ¿Garantiza camino óptimo? | SÍ | NO |
| Completitud (si existe solución) | SÍ | SÍ |
| Complejidad espacial (memoria) | `O(b^d)` | `O(d)` |
| Complejidad temporal (peor caso) | `O(b^d)` | `O(b^d)` |

En este laberinto ambos hallaron caminos de igual longitud (19 pasos), y DFS exploró 5 nodos menos que BFS. Sin embargo, solo BFS **garantiza** que ese camino sea el óptimo.

## Comparación BFS vs DFS

| Criterio | Ganador | Justificación |
|---|---|---|
| Camino óptimo garantizado | **BFS** | Propiedad fundamental de BFS en grafos con costo uniforme |
| Memoria (espacio) | **DFS** | `O(d)` vs `O(b^d)`; crítico en espacios de estados grandes |
| Velocidad (tiempo) | **Depende** | BFS mejor si la solución está cerca; DFS puede ser más rápido si está profunda y hay poca ramificación |
| Implementación simple | **DFS** | Recursión natural; BFS requiere cola explícita |
| Detección de ausencia de solución | **Empate** | Ambos son completos en un espacio finito con `visitados` |

## Recomendaciones prácticas

- **Usar BFS** cuando: el espacio de estados es moderado, se requiere optimalidad y la solución se espera a poca profundidad.
- **Usar DFS** cuando: la memoria es muy limitada, el espacio es enorme o arbolado, o cualquier solución sirve (no se requiere optimalidad).

## Estructura del proyecto

```
.
├── FTL1_Carlos_Piñero_laberinto.ipynb   # Notebook principal
├── README.md                             # Este archivo
├── requirements.txt                      # Dependencias
└── venv/                                 # Entorno virtual (no versionado)
```

## Licencia

Material académico de uso educativo.
