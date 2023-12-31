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

## Programación Dinámica

### Ejercicio 5

**b)**

**d)** Tengo n*k estados, cada uno se calcula en O(1), entonces la complejidad es O(n*k)


### Ejercicio 6

**a)** 

$$
cc(B,c) = 
\begin{cases}
    (\infty, \infty) \\ \\ \\ \\ \\ \\ \\ \\ \\  \text{si } i=|B| \land c>0\\
    (0,0) \\ \\ \\ \\ \\ \\ \\ \\ \\ \\ \\ \\ \\  \text{si } c\leq 0\\
    min(min(cc({b2,...,bn}, c-b1).first+b1, cc({b2,...,bn}, c-b1).second+1), (cc({b2,...,bn}, c-b1).first, cc({b2,...,bn}, c-b1).second))) \text{caso contrario}
\end{cases}
$$

**b)** y **c)**
En b tendria que pasar los billetes como parametro, pero es esencialmente lo mismo.
Implementación en C++
```cpp
ESTA FORMA NO FUNCIONO
#include <iostream>
#include <vector>
#include <algorithm>
#include <tuple>

using namespace std;

vector<int> B;
int billetesMinimos;
int excesoMinimo;
pair<int,int> sol;
//vector<vector<int>> memoria;

int sumaElementos(vector<int> B){
    int res = 0;
    for(int i=0; i< B.size(); i++){
        res+= B[i];
    }
    return res;
}

void minimoExceso(int c, int i, pair<int,int>& sol_parcial){
    if(sumaElementos(B) < c || i<0){
        sol_parcial.first = 1e9;
        sol_parcial.second = 1e9;
        sol = sol_parcial;
        return;
    }
    if(c <= 0){
        int exceso = sol_parcial.first;
        if(exceso < excesoMinimo){
            excesoMinimo = exceso;
        } else if(exceso == excesoMinimo){
            if(sol_parcial.second < billetesMinimos){
                billetesMinimos = sol_parcial.second;
            }
        }
        sol.first = excesoMinimo;
        sol.second = billetesMinimos;
    }
    minimoExceso(c, i- 1, sol_parcial);
    sol_parcial.first = sol_parcial.first + B[i];
    sol_parcial.second = sol_parcial.second + 1;
    minimoExceso(c - B[i],i - 1, sol_parcial);
    sol_parcial.first = sol_parcial.first - B[i];
    sol_parcial.second = sol_parcial.second - 1;

}

int main() {
    int N;
    int c;
    cin >> N;
    cin >> c;
    B.resize(N);
    for(int i= 0; i<N; i++){
        int elem;
        cin >> elem;
        B[i] = elem;
    }
    int billetesMinimos = 10000;
    int excesoMinimo = 10000;
    pair<int,int> ini = make_pair(0,0);
    minimoExceso(c,N-1,ini);
    cout << "Lo minimo que puedo pagar con los billetes es " << sol.first << " y la minima cantidad de billetes es " << sol.second;
    return 0;
}
 ------------------------
ESTA FUNCIONA

#include <iostream>
#include <vector>
#include <algorithm>
#include <tuple>

using namespace std;

vector<int> B;
//vector<vector<int>> memoria;

int sumaElementos(vector<int> B){
    int res = 0;
    for(int i=0; i< B.size(); i++){
        res+= B[i];
    }
    return res;
}

pair<int,int> minimoExceso(int c, int i) {
    if (i < 0 && c>0){
        return make_pair(1e9, 1e9);
    }
    if (c <= 0) {
        return make_pair(0, 0);
    }
    pair<int, int> con_billete_i = minimoExceso(c - B[i], i - 1);
    pair<int, int> sin_billete_i = minimoExceso(c, i - 1);
    if (con_billete_i.first+B[i] == sin_billete_i.first) {
        if (con_billete_i.second+1 < sin_billete_i.second) {
            return make_pair(con_billete_i.first+B[i], con_billete_i.second+1);
        }
        return make_pair(sin_billete_i.first, sin_billete_i.second);
    }
    if (con_billete_i.first+B[i] < sin_billete_i.first) {
        return make_pair(con_billete_i.first+B[i], con_billete_i.second+1);
    }
    return make_pair(sin_billete_i.first, sin_billete_i.second);
}

int main(){
        int N;
        int c;
        cin >> N;
        cin >> c;
        B.resize(N);
        for(int i= 0; i<N; i++){
            int elem;
            cin >> elem;
            B[i] = elem;
        }
        pair<int,int> res = minimoExceso(c,N-1);
        cout << "Lo minimo que puedo pagar con los billetes es " << res.first << " y la minima cantidad de billetes es " << res.second;
        return 0;
}
```

**d)**

Sobreposición de estados: cuando la cantidad de estados es mucho menor a la cantidad de llamados recursivos. O sea si
$NP<< 2^{N} \leftrightarrow P<<2^{N}/N$
con N=|B|. 
Tendria que tener una estructura de memorización de tamaño N*P. Indexo segun la pocision en la que estoy de B y la cantidad que me queda poner.

**e)**

```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<int> B;
vector<vector<pair<int,int>>> memoria;

int sumaElementos(vector<int> B){
    int res = 0;
    for(int i=0; i< B.size(); i++){
        res+= B[i];
    }
    return res;
}

pair<int,int> minimoExceso(int c, int i) {
    if (i < 0 && c>0){
        return make_pair(1e9, 1e9);
    }
    if (c <= 0) {
        return make_pair(0, 0);
    }
    if (memoria[i][c].first != -1 || memoria[i][c].second != -1){
        return memoria[i][c];
    }
    pair<int, int> con_billete_i = minimoExceso(c - B[i], i - 1);
    pair<int, int> sin_billete_i = minimoExceso(c, i - 1);
    if (con_billete_i.first+B[i] == sin_billete_i.first) {
        if (con_billete_i.second+1 < sin_billete_i.second) {
            memoria[i][c] = make_pair(con_billete_i.first+B[i], con_billete_i.second+1);
        } else {
            memoria[i][c] = make_pair(sin_billete_i.first, sin_billete_i.second);
        }

    } else if (con_billete_i.first+B[i] < sin_billete_i.first) {
        memoria[i][c] = make_pair(con_billete_i.first+B[i], con_billete_i.second+1);
    } else {
        memoria[i][c] = make_pair(sin_billete_i.first, sin_billete_i.second);
    }
    return memoria[i][c];
}

int main(){
    int N;
    int c;
    cin >> N;
    cin >> c;
    memoria.resize(N, vector<pair<int,int>>(c+1, make_pair(-1,-1)));
    B.resize(N);
    for(int i= 0; i<N; i++){
        int elem;
        cin >> elem;
        B[i] = elem;
    }
    pair<int,int> res = minimoExceso(c,N-1);
    cout << "Lo minimo que puedo pagar con los billetes es " << res.first << " y la minima cantidad de billetes es " << res.second;
    return 0;
}
```

**f)** 
El llamado recursivo que lo resuelve es minimoExceso(c,N-1). La nueva complejidad se calcula como la cantidad de estados posibles * complejidad de calcular cada estado. Complejidad = O(NP)

### Ejercicio 7
En este ejercicio tengo la compra/venta de asteroides con diferentes condiciones. Tengo que maxmizar las ganancias en base a lo que salen los asteroides cada día.

**a)** No puede pasar que tenga negativas cantidades de asteroides (no puedo vender un asteroide que no tengo) ni la cantidad de asteroides puede superar a la cantidad de días que tengo. En estos casos se indefine
En el día j tengo c asteroides:
-Si en el dia j-1 tengo c-1 asteroides, significa que compre un asteroide en el dia j (para llegar a tener c), por lo que baja mi ganancia.
-Si en el día j-1 tengo c+1 asteroides, significa que vendi un asteroide en el dia j (para llegar a tener c), por lo que sube mi ganancia.
-Si en el día j-1 tengo c asteroides, significa que no opere en el dia j.

**b)**

$$
av(P, c, j) =
\begin{cases}
     indefinido & \text{si } c < 0 \lor j < c \\
     0 & \text{si } j < 0 \\
     max(av(P, c+1, j-1) + P[j], av(P, c-1, j-1) - P[j], av(P, c, j-1)) & \text{caso contrario}
\end{cases}
$$

**c)** El dato que es respuesta al problema es av(P, 0, |P|-1) (quiero ver cual es la maxima ganancia, si al finalizar el ultimo día tengo 0 asteroides). (Obs: el dia 1 es j=0, y el ultimo dia es j=|P|-1)). 

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
    if(c < 0 || (c > j+1 && c > 0)){
        return -1e9;
    }
    //caso base
    if(j < 0){
        return 0;
    }
    // veo si ya resolví esta solución
    if (memoria[c][j] != -1) {
        return memoria[c][j];
    } else {
        //casos posibles
        int comprar = AstroVoid(c-1,j-1) - P[j];
        int vender = AstroVoid(c+1,j-1) + P[j];
        int nada = AstroVoid(c,j-1);
        //caso recursivo
        memoria[c][j] = max(comprar, max(vender,nada));
        return memoria[c][j];
    }
}


int main() {
    cin >> N;
    memoria.resize(N,vector<int>(N,-1));
    P.resize(N);
    for(int i= 0; i< N; i++){
        int elem;
        cin >> elem;
        P[i] = elem;
    }
    int res = AstroVoid(0,N-1);
    if (res == -1) {
        cout << "No es posible obtener ganancia neta." << endl;
    } else {
        cout << "La maxima ganancia neta es: " << res << endl;
    }
    return 0;
}

```
### Ejercicio 8

**b)**

$$
minCosto({k_{0},...,k_{n-1}}, i, j) =
\begin{cases}
     0 & \text{si } c < 0 \\
     j - i + \min_{k_i \in C \atop 0 \leq i < n} \left( minCosto({k_{0},...,k_{i-1}}, i, k_{i}) + minCosto({k_{i+1},...,k_{n-1}}, k_{i}, j) \right) & \text{caso contrario}
\end{cases}
$$


**c)**

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> C;
int infinito = 1e9;
int N;

int cortarVara(int i, int j, int c){
    if(c == N || C[c] < i || C[c] > j){
        return 0;
    }
    int cortarPorIzquierda = cortarVara(i,C[c],c + 1);
    int cortarPorDerecha = cortarVara(C[c],j,c + 1);
    return (j-i) + cortarPorIzquierda + cortarPorDerecha;
}

int main(){
    int longVara;
    cin >> longVara;
    cin >> N;
    C.resize(N);
    for(int i=0; i<N; i++){
        int elem;
        cin >> elem;
        C[i] = elem;
    }
    int res = cortarVara(0,longVara,0);
    cout << "El minimo costo para cortar una madera en esos puntos es " << res;
    return 0;
}
```

### Ejercicio 9

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;
vector<vector<int>> matriz;
vector<vector<int>> memoria;
int infinito = 1e9;
int M;
int N;
//en memoria voy a tener la vida que necesito para llegar a esa posicion

int minimaVida (int m, int n){
    if (m == M || n == N){
        return infinito;
    }
    if (m == M-1 && n == N-1){
        return 0;
    }
    if (memoria[n][m] != -1){
        return memoria[n][m];
    }
    int res = min(minimaVida(m+1,n), minimaVida(m,n+1));
    if (res < 0){
        return (1 - matriz[n][m]);
    } else {
        return (res - matriz[n][m]);
    }
}

int main(){
    cin >> M;
    cin >> N;
    matriz.resize(M, vector<int>(N));
    for(int i=0; i<M; i++){
        for(int j=0; j<N;j++){
            int elem;
            cin >> elem;
            matriz[i][j] = elem;
        }
    }
    memoria.resize(M, vector<int>(N,-1));
    int rta = minimaVida(0,0);
    cout << "La minima vida con la que hay que comenzar es " << rta;
    return 0;
}
```

### Ejercicio 10

**a)** La idea con backtracking seria ir poniendo o no poniendo las cajas y devolver el maximo de estos resultados. Como voy en orden siempre se va a cumplir la resticcion de que esten apiladas ordenadamente y voy a ir chequeando que no se supere el peso que pueden soportar (para hacer esto voy a llamar siempre al minimo entre los soportes de todas las cajas que apile).

**b)**

$$
apilarCajas(aguanta,i)_{w,s,sumaSoportes} =
\begin{cases}
    0 & \text{si } s[i] > aguanta \\
    -\infty & \text{si } i > |P| - 1 \\
    max(apilarCajas(aguanta, i+1), apilarCajas(min(s[i], aguanta-w[i]), i+1)) & \text{en otros casos}
\end{cases}
$$
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> pesos;
vector<int> soportan;
vector<vector<int>> memoria;
int infinito = 1e9;

//en memoria voy a tener la vida que necesito para llegar a esa posicion

int apilarCajas (int aguanta, int i){
    if(soportan[i] > aguanta){
        return 0;
    }
    if (i > pesos.size()-1){
        return infinito;
    }
    if (memoria[aguanta][i] != -1){
        return memoria[aguanta][i];
    }
    int noAgregoCaja = apilarCajas(aguanta, i+1);
    int agregoCaja = apilarCajas(min(soportan[i], (aguanta - pesos[i])), i+1);
    memoria[aguanta][i] = max(noAgregoCaja, agregoCaja+1);
    return memoria[aguanta][i];
}

int main(){
    int N;
    cin >> N;
    pesos.resize(N);
    soportan.resize(N);
    for (int i=0; i< N; i++){
        int elem;
        cin >> elem;
        pesos[i] = elem;
    }
    for (int j=0; j< N; j++){
        int elem;
        cin >> elem;
        soportan[j] = elem;
    }
    int sumaSoportes = 0;
    for (int h=0; h< N; h++){
        sumaSoportes+= soportan[h];
    }
    memoria.resize(sumaSoportes+1,vector<int>(N,-1));
    int res = apilarCajas(sumaSoportes, 0);
    cout << "La maxima cantidad de cajas que puedo apilar es " << res;
    return 0;
}
```

## Greedy

### Ejercicio 13

**b)** 

```cpp
#include <iostream>
#include <vector>
#include <math.h>

using namespace std;

vector<int> A;
int a;
vector<int> B;
int b;
int infinito = 1e9;

int maximasParejas(int i, int j){
    int cantParejas = 0;
    while (i < a && j < b){
        if(abs((A[i]-B[i])) <= 1){
            cantParejas++;
            i++;
            j++;
        } else if(A[i] < B[i]){
            i++;
        } else {
            j++;
        }
    }
    return cantParejas;
}

int main(){
    cin >> a;
    A.resize(a);
    for (int i=0; i<a; i++){
        int elem;
        cin >> elem;
        A[i] = elem;
    }
    cin >> b;
    B.resize(b);
    for (int j=0; j<b; j++){
        int elem;
        cin >> elem;
        B[j] = elem;
    }
    int res = maximasParejas(0,0);
    cout << "La cantidad maxima de parejas que se pueden formar es " << res;
    return 0;
}
```
Complejidad: 
* Temporal: O(N+M) 
* Espacial: O(1)

**c)**
Para demostrar que el algoritmo es correcto, veo que 1) La solución es válida (todas las parejas tienen una diferencia de habilidad menor o igual a 1, no hay dos personas que esten en dos parejas, y todas las parejas tienen a una persona de cada grupo). 2) La solución es óptima


1) La solución es válida ya que en cada paso me voy fijando si |el primer elemento del multiconjunto A - el primer elemento del multiconjunto B| es menor o igual a 1, si lo es sumo uno a la cantidad de parejas y aumento ambos indices, si no lo es (al estar ambos multiconjuntos ordenados) me voy a fijar que elemento es menor dado que este es el que voy a descartar (no puede pasar que, si aumento el indice del mayor numero luego coincidan ya que solo va a agrandar el numero y cada vez va a haber mas diferencia)

2) Es óptima ya que


### Ejercicio 14

**a)**

```cpp
#include <iostream>
#include <vector>
#include <math.h>
#include <algorithm>

using namespace std;

vector<int> A;
int a;
int infinito = 1e9;

int menorCostoSumas(int i){
    int costo = 0;
    sort(A.begin(), A.end());
    while (i < a-1){
        costo = costo + A[i] + A[i+1];
        A[i+1] = costo;
        i++;
    }
    return costo;
}

int main(){
    cin >> a;
    A.resize(a);
    for (int i=0; i<a; i++){
        int elem;
        cin >> elem;
        A[i] = elem;
    }
    int res = menorCostoSumas(0);
    cout << "La cantidad maxima de parejas que se pueden formar es " << res;
    return 0;
}
```
Entonces la estrategia seria ordenar la lista ya que siempre me va a convenir sumar las 2 cosas mas chicas primero. Luego le voy a asignar a la posicion i+1 este resultado para sumarselo al procximo elemento.

**b)** 

1) La solución es válida: la solución es válida ya que estoy agarrando elementos de la lista siempre, y cualquier combinacion de estos va a ser válida.

2) La solución es óptima : Es óptima ya que no hay otra forma de sumar los elementos de la lista que sea menor a la de mi algoritmo greedy.


