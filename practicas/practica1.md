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
```

### Ejercicio 2


