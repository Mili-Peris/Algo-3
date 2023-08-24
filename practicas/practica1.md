# Práctica 1: Técnicas Algorítmicas

## Backtracking

### Ejercicio 1

**a)** a = <0,0,0>, <0,0,1>, <0,1,0>, <0,1,1>, <1,0,0>, <1,0,1>, <1,1,0> y <1,1,1>

**b)** a = <1,0,1> y <0,1,0>

**c)** p = <0>, <1>, <0,0>, <0,1>, <1,0>, <1,1> y todas las del punto a.

**d)** y **g)** Dibujado en cuaderno

**f)** 2^|C|, siempre vos a ir dividiendo en 2 casos= 0 o 1, |C| veces.

**i)** otra regla de factibilidad seria que cuando j= 0 devuelva true. Esto significaria que lo que quiero sumar ya se sumo, entonces ya es true (ni se paso ni falta, j representa k que es lo que quiero sumar con los elementos de C)

**j)** Implementación en C++

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector <int> solucion(0);
bool existe;
int k, longC;
vector<int> C;

void subset_sum(const vector<int> &C, int i, int j, vector<int> sol_parcial){
    if (j == 0){
        solucion = sol_parcial;
        existe = true;
        return;
    }
    if (j < 0 || i == 0){
        return;
    }
    subset_sum(C,i-1,j, sol_parcial);
    sol_parcial.push_back(C[i]);
    subset_sum(C,i-1,j-C[i], sol_parcial);
    sol_parcial.pop_back();
}

int main() {
    cin >> k;
    cin >> longC;
    C.resize(longC);
    for (int i=0; i<longC; i++) {
        int elem;
        cin >> elem;
        C[i]=elem;
    }
    existe = false;
    vector<int> sol_parcial(0);
    subset_sum(C, C.size()-1, k, sol_parcial);
    cout << (existe ? "Existe" : "No existe") << endl;
    if (existe) {
        cout << "El subconjunto que suma lo solicitado es {";
        for(int j=0; j<solucion.size(); j++) {
            if (j!=solucion.size()-1) {
                cout << solucion[j] << ", ";
            } else {
                cout << solucion[j];
            }
        }
        cout << "}" << endl;
    }
    return 0;
}
En el ejercicio tengo mi resultado final (declarado fuera de la funcion, se le dice variable global)) y mi resultado parcial (entrado como parametro) para poder ir modificando este (se pasa por referencia para no tener el costo de copiar) y directamente asignarlo a la respuesta final cuando sea válido.
```

### Ejercicio 2

**a)** Al tener n^2 casillas tendría que generar (n^2)! cuadrados mágicos (al no haber repetidos, para la primera casilla tendria que generar n^2 cuadrados, para la segunda n^2-1 ya descarto la que incluye el numero de la primera casilla y asi sucesivamente).

**b)** Resolución en C++

```cpp
#include <iostream>
#include <vector>
#include <math.h>
using namespace std;

int N;
int cantidad;

bool esValido(const vector<vector<int>> &s){
    //me fijo cuál es el número mágico
    int magico = 0;
    for(int i= 0; i< N; i++){
        magico += s[i][0];
    }
    //me fijo si la suma de cada fila es el número mágico
    for(int i=0; i < N; i++){
        int fila = 0;
        for(int j=0; j < N; j++){
            fila += s[i][j];
        }
        if (fila != magico){
            return false;
        }
    }
    //me fijo si la suma de cada columna es el número mágico
    for(int i=0; i < N; i++){
        int columna = 0;
        for(int j=0; j < N; j++){
            columna += s[j][i];
        }
        if (columna != magico){
            return false;
        }
    }
    //me fijo si las diagonal izquierda suma el número mágico
    int diagIzq = 0;
    for(int i=0 ; i< N; i++){
        diagIzq+= s[i][i];
    }
    if(diagIzq != magico){
        return false;
    }
    //me fijo si las diagonal izquierda suma el número mágico
    int diagDer = 0;
    for(int i=0 ; i< N; i++){
        diagDer+= s[i][N-1-i];
    }
    if(diagDer != magico){
        return false;
    }
    return true;
}

//s: vector con la matriz que es un posible cuadrado perfecto;
//numeros:tiene n2 pos. En cada posicion, al acceder, voy a tener un 0 si puedo usar ese número (correspondiente a la indexacion de este en el vector) o un 1 si ya fue usado (no pueden haber repetidos en la matriz)
//N es el orden del cuadrado
// i, j representan la posicion de la matriz que voy a querer modificar, o sea meter un numero que no esté.

void CuadradosMagicos(vector<vector<int>>& s, int i, int j, int N, vector<int>& numeros){
    if(i==N && esValido(s)){
        cantidad++;
        return;
    }
    for(int h=0; h< numeros.size() ;h++){
        if(numeros[h] == 0){
            s[i][j] = h+1;
            numeros[h] = 1;
            if(j== N-1){
                CuadradosMagicos(s,i+1,0,N,numeros);
            } else {
                CuadradosMagicos(s,i,j+1,N,numeros);
            }
            //reinicio el problema en el estado que estaba para poder ir a otra rama.
            numeros[h] = 0;
            s[i][j]= 0;
        }
    }
}

int main() {
    cin >> N;
    vector<int> numeros(pow(N,2),0);
    vector<vector<int>> s(N,vector<int>(N,0));
    cantidad = 0;
    CuadradosMagicos(s,0,0,N,numeros);
    cout << "La cantidad de cuadrados magicos de orden " << N << " es: "<< cantidad;
    return 0;
}
```
**c)** Tengo, en el peor caso, O(n^2!) nodos ya que, cada ramificacion se va abriendo (n^2)-1 veces al no poder tener numero repetidos, asi hasta llegar al 0.

**d)** 

**e)**
