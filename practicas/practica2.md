# Introducción a la teoría algorítmica de grafos

## Demostración de propiedades simples sobre grafos

### Ejercicio 1

### Ejercicio 2

### Ejercicio 3


## Representación de grafos y digrafos

### Ejercicio 11

Operaciones

**a)** O(n+m), estoy inicializando n listas y agregando las uniones a estas.

**b)** O(d(v)) ya que al acceder a la posicion v, donde estan todos los vecinos de este, tengo que buscar a w. A lo sumo recorro todos los vecinos de v.

**c)** O(d(v)) ya que el vecindario es un vector<int>, accedo al vecindario de v indexando en O(1) y luego lo recorro. n ya que a lo sumo estan todas los vertices como vecinos de v.

**d)** O(d(v)). Agrego en la posicion v, N(v) y luego recorro N(v) y, por cada uno, accedo a sus vecinos y les agrego v.

**e)** O(1). Tengo que acceder a los vecinos de v y agrego w; accedo a los vecinos de w y agrego v. 

**f)** O(d(v)). Recorro todos los vecinos de v, les elimino v. Luego elimino N(v).

**g)** O(d(v) + d(w)). Recorro los vecinos de w hasta encontrar a v y lo borro. Lo mismo para v. 

**h)** O(d(v))

### Ejercicio 12

Las listas de adyacencia, donde en cada posicion de un vertice hay un vector con sus vecinos, cuestan inicializarlas O(n+m). Esto es porque:
-n ya que necesito una posicion por cada vértice del grafo. Creo n listas.
-m ya que recorro cada arista y por cada arista agrego el elemento que conecta.

Matriz: creo n vectores de n elementos. Crear cada una es O(n). Queda en O(n^2)

## Algoritmos sobre grafos y digrafos
