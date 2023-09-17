# Introducción a la teoría algorítmica de grafos

## Demostración de propiedades simples sobre grafos

### Ejercicio 1

### Ejercicio 2

### Ejercicio 3


## Representación de grafos y digrafos

### Ejercicio 11

Operaciones

**a)** O(1), estoy inicializando un vector<vector<int>>.

**b)** O(m) ya que al acceder a la posicion v, donde estan todos los vecinos de este, tengo que buscar a w. m ya que a lo sumo recorro todas las aristas 

**c)** O(n) ya que el vecindario es un vector<int>, accedo al vecindario de v indexando en O(1) y luego lo recorro. n ya que a lo sumo estan todas los vertices como vecinos de v.

**d)** O(n). Agrego en la posicion v N(v) y luego recorro N(v) y, por cada uno, accedo a sus vecinos y les agrego v.

**e)** O(1). Tengo que acceder a los vecinos de v y agrego w; accedo a los vecinos de w y agrego v. 

**f)** O(n). Recorro todos los vecinos de v, les elimino v. Luego elimino N(v).

**g)** O(n). Recorro los vecinos de w hasta encontrar a v y lo borro. Lo mismo para v. 

**h)** 

### Ejercicio 12

Las listas de adyacencia, donde en cada posicion de un vertice hay un vector con sus vecinos, cuestan inicializarlas O(n+m). Esto es porque:
-n ya que necesito una posicion por cada vértice del grafo.
-m ya que accedo a cada posicion y por cada arista agrego el elemento que conecta.

## Algoritmos sobre grafos y digrafos
