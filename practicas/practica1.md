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

**d)** Resolución en C++
```cpp
#include <iostream>
#include <vector>
#include <math.h>
using namespace std;

int N;
int cantidad;
vector<int> SumaFila;
vector<int> SumaColumna;

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
    int magico= (pow(N,3)+ N) /2;
    for(int h=0; h< numeros.size() ;h++){
        if(numeros[h] == 0 && SumaFila[i] + h <= magico && SumaColumna[i] + h <= magico){
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
    SumaFila.resize(N,0);
    SumaColumna.resize(N,0);
    vector<vector<int>> s(N,vector<int>(N,0));
    cantidad = 0;
    CuadradosMagicos(s,0,0,N,numeros);
    cout << "La cantidad de cuadrados magicos de orden " << N << " es: "<< cantidad;
    return 0;
}
```
**e)** 
$\sum_{i=1}^{n^2}x_{i}$

### Ejercicio 3

**a)** Solución candidata= Subconjunto I de tamaño k que contiene posiciones de la matriz M.
Solución válida = La que tenga la mayor suma al indexar en las posiciones del subconjunto I y sumarlas (si dos suman lo mismo y es el máximo que se puede sumar, ambas son válidas)
Solución parcial = Subconjuntos menores o iguales a k que son posiciones de la matriz M.
Solución en c++
```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> I;
int maxSuma;
vector<vector<int>> matriz;
int k;
int N;

int sumaConjunto(vector<int> &A){
    int res=0;
    for(int i=0; i<N; i++){
        for(int j=0; j<N; j++){
            res+= matriz[i][j];
        }
    }
    return res;
}

void maximaSuma(int k2, vector<int>& sol_parcial, int valor){
    if (k2==0){
        int suma = sumaConjunto(sol_parcial);
        if(suma > maxSuma){
            I = sol_parcial;
            maxSuma = suma;
        }
        return;
    }
    if (valor==N) {
        return;
    }
    sol_parcial.push_back(valor);
    maximaSuma(k2-1,sol_parcial,valor+1);
    sol_parcial.pop_back();
    maximaSuma(k2,sol_parcial,valor+1);
}

int main() {
    cin >> k;
    cin >> N;
    matriz.resize(N, vector<int>(N,0));
    for(int i=0; i<N; i++){
        for(int j=0; j<N; j++){
            int elemento;
            cin >> elemento;
            matriz[i][j] = elemento;
        }
    }
    vector<int> sol_parcial(0);
    maxSuma=0;
    maximaSuma(k, sol_parcial, 0);
    cout <<"La maxima suma de la matriz lo forma el conjunto {";
    for(int i=1; i<=k ;i++) {
        cout << I[i-1]+1 << ",";
    }
    // le sumo uno porque en mejor_solucion tengo los indices, pero como I empieza a sumar en 1 hay q arreglar eso
    cout <<"} y su suma es " << maxSuma;
    return 0;
}
```
**b)** Muy grande, calcular despues.

**c)** Como estamos en una matriz simetrica, no haría falta sumar las posiciones de abajo de todo. 

### Ejercicio 4

**a)**  Solución candidata= Subconjunto de tamaño n que contiene todas las posiciones de la matriz M (en orden o permutadas).
Solución válida = La que tenga la menor suma al indexar en las posiciones del subconjunto en el orden que indica la formula al sumarlas (si dos suman lo mismo y es el minimo que se puede sumar, ambas son válidas)
Solución parcial = iguales que las candidatas.
Solución en c++
```cpp
#include <iostream>
#include <vector>

using namespace std;

int N;
vector<vector<int>> matriz;
int minSum;
vector<int> minimaPermutacion;

int sumaConjunto(vector<int>& conjunto){
    int res = 0;
    res+= matriz[N-1][0];
    for(int i=0; i<N-1; i++){
        res+= matriz[i][i+1];
    }
    return res;
}

//idea: voy permutando sol_parcial de diferentes maneras hasta quedarme sin rotaciones disponibles MUY COMPLICADO
/*void minimaSuma(vector<int>& sol_parcial, int rotacionesDisp){
    int suma = sumaConjunto(sol_parcial);
    if(rotacionesDisp == 0){
        if(suma < minSum){
            minSum = suma;
            minimaPermutacion = sol_parcial;
        }
        return;
    }
} */

//tengo mi solucion parcial, que voy a ir completando con permutaciones de los numero de 1...N, agregando elementos de
//a uno si todavia no estan (lo chequeo indexando en el vector esta)
void minimaSuma(vector<int>& sol_parcial, vector<bool>& esta){
    int suma = sumaConjunto(sol_parcial);
    if(sol_parcial.size() == N){
        if(suma < minSum || minSum == -1){
            minSum = suma;imaPermutacion <<
            minimaPermutacion = sol_parcial;
        }
        return;
    }
    for(int i=1;i<=N; i++){
        if(!esta[i]){
            sol_parcial.push_back(i);
            esta[i] = true;
            minimaSuma(sol_parcial, esta);
            esta[i] = false;
            sol_parcial.pop_back();
        }
    }
}

int main() {
    cin >> N;
    matriz.resize(N, vector<int>(N,0));
    for(int i=0; i<N; i++){
        for(int j=0; j<N; j++){
            int elemento;
            cin >> elemento;
            matriz[i][j] = elemento;
        }
    }
    minSum = -1;
    minimaPermutacion.resize(N);
    vector<int> sol_parcial(0);
    vector<bool> esta(N, false);
    minimaSuma(sol_parcial,esta);
    cout << "La minima suma esta formada por el conjunto {";
    for (int i=0; i<N;i++){
        cout << minimaPermutacion[i];
        // le sumo uno porque en mejor_solucion tengo los indices, pero como I empieza a sumar en 1 hay q arreglar eso
    }
    cout << "} y su suma es " << minSum;
    return 0;
}
```

### Ejercicio 5

### Ejercicio 6

### Ejercicio 7
En este ejercicio tengo la compra/venta de asteroides con diferentes condiciones. Tengo que maxmizar las ganancias en base a lo que salen los asteroides cada día.

**a)** No puede pasar que tenga negativas cantidades de asteroides (no puedo vender un asteroide que no tengo) ni la cantidad de asteroides puede superar a la cantidad de días que tengo. En estos casos se indefine
En el día j tengo c asteroides:
-Si en el dia j-1 tengo c-1 asteroides, significa que compre un asteroide en el dia j (para llegar a tener c), por lo que baja mi ganancia.
-Si en el día j-1 tengo c+1 asteroides, significa que vendi un asteroide en el dia j (para llegar a tener c), por lo que sube mi ganancia.
-Si en el día j-1 tengo c asteroides, significa que no opere en el dia j.

**b)**
AV(P,c,j) = ⊥ si c<0 || c>j

            0 si j<1
            
            max( AV(P,c-1, j-1) - P[j], AV(P,c+1,j-1) + P[j], AV(P,c,j-1) )

**c)** El dato

**d)** 
Resolución en C++
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int N;
vector<int> P;
vector<vector<int>> memoria;

int AstroVoid(int c, int j){
    //caso indefinido
    if(c < 0 || c > j){
        return -1;
    }
    //caso base
    if(j < 1){
        return 0;
    }
    // veo si ya resolví esta solución
    if (memoria[c][j] != -1) {
        return memoria[c][j];
    } else {
        //casos posibles
        int comprar = AstroVoid(c-1,j-1) - P[j-1];
        int vender = AstroVoid(c+1,j-1) + P[j-1];
        int nada = AstroVoid(c,j-1);
        //caso recursivo
        memoria[c][j] = max(comprar, max(vender,nada));
        return memoria[c][j];
    }
}


int main() {
    cin >> N;
    memoria.resize(N+1,vector<int>(N+1,-1));
    P.resize(N);
    for(int i= 0; i< N; i++){
        int elem;
        cin >> elem;
        P[i] = elem;
    }
    int res = AstroVoid(0,N);
    if (res == -1) {
        cout << "No es posible obtener ganancia neta." << endl;
    } else {
        cout << "La maxima ganancia neta es: " << res << endl;
    }
    return 0;
}


```
