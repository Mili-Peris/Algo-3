Todos los ejercicios fueron resueltos en C++

### EJERCICIO 1

```cpp
#include <iostream>
#include <vector>
#include <tuple>

using namespace std;

// direcciones: 1=arriba, 2=abajo, 3=izquierda, 4=derecha
int cantCasos;
int N;
int M;
vector<vector<char>> T;
int infinito=1e9;
int maximo= (-infinito);
int minimo= (+infinito);
int cantPasos=0;
vector<vector<bool>> recorridos;

bool estaFuera(int i, int j){
    return (i < 0 || i >= N || j < 0 || j >= M);
}

void tuki(int i, int j, int dirAnterior)  {
    //me fijo si estoy en la esquina inferior derecha
    if (i==N-1 && j==M-1) {
        if (T[i][j]!='#') {
            if (cantPasos < minimo) {
                minimo = cantPasos;
            }
            if (cantPasos > maximo){
                maximo = cantPasos;
            }
        }
    } else {
        if (estaFuera(i,j) || T[i][j] == '#' || recorridos[i][j]==true) {
            return;
        }
        recorridos[i][j]=true;
        if (T[i][j] == 'I') {
            if (dirAnterior==1) {
                cantPasos++;
                tuki(i+1,j,1);
                cantPasos--;
            }
            if (dirAnterior==2){
                cantPasos++;
                tuki(i-1,j,2);
                cantPasos--;
            }
            if (dirAnterior== 3){
                cantPasos++;
                tuki(i, j+1, 3);
                cantPasos--;
            }
            if (dirAnterior== 4){
                cantPasos++;
                tuki(i, j-1, 4);
                cantPasos--;
            }
        }
        if (T[i][j] == 'L') {
            if (dirAnterior==1 || dirAnterior==2) {
                cantPasos++;
                tuki(i, j - 1, 4);
                cantPasos--;

                cantPasos++;
                tuki(i, j + 1, 3);
                cantPasos--;
            }
            if (dirAnterior== 3 || dirAnterior == 4){
                cantPasos++;
                tuki(i+1, j, 1);
                cantPasos--;
                cantPasos++;
                tuki(i-1, j, 2);
                cantPasos--;
            }
        }
        if (T[i][j] == '+') {
            if (dirAnterior==1) {
                cantPasos++;
                tuki(i+1,j,1);
                cantPasos--;
                cantPasos++;
                tuki(i,j+1,3);
                cantPasos--;
                cantPasos++;
                tuki(i,j-1,4);
                cantPasos--;
            }
            if (dirAnterior==2){
                cantPasos++;
                tuki(i-1,j,2);
                cantPasos--;
                cantPasos++;
                tuki(i,j+1,3);
                cantPasos--;
                cantPasos++;
                tuki(i,j-1,4);
                cantPasos--;
            }
            if (dirAnterior==3){
                cantPasos++;
                tuki(i+1,j,1);
                cantPasos--;
                cantPasos++;
                tuki(i,j+1,3);
                cantPasos--;
                cantPasos++;
                tuki(i-1,j,2);
                cantPasos--;
            }
            if (dirAnterior==4){
                cantPasos++;
                tuki(i+1,j,1);
                cantPasos--;
                cantPasos++;
                tuki(i-1,j,2);
                cantPasos--;
                cantPasos++;
                tuki(i,j-1,4);
                cantPasos--;
            }
        }
    }
    recorridos[i][j]=false;
}

int main() {
    cin >> cantCasos;
    vector<tuple<string, int,int>> resultados(cantCasos);
    for (int p=0; p<cantCasos; p++) {
        cin >> N;
        cin >> M;
        T = vector<vector<char>>(N, vector<char>(M));
        recorridos = vector<vector<bool>>(N, vector<bool>(M, false));
        for (int i=0; i<N; i++) {
            string fila;
            cin >> fila;
            for (int j=0; j<fila.size(); j++) {
                T[i][j] = fila[j];
            }
        }
        maximo = -infinito;
        minimo = infinito;
        cantPasos=0;
        tuki(0,0,1);
        cantPasos=0;
        tuki(0,0,3);
        if (maximo!= (-infinito)) {
            resultados[p] = make_tuple("POSIBLE", minimo, maximo);
        } else {
            resultados[p] = make_tuple("IMPOSIBLE", minimo, maximo);
        }
    }
    for (int p=0; p<resultados.size(); p++) {
        if (get<0>(resultados[p]) == "POSIBLE") {
            cout << get<0>(resultados[p]) << " " << get<1>(resultados[p]) << " " << get<2>(resultados[p]) << endl;
        } else {
            cout << get<0>(resultados[p]) << endl;
        }
    }
    return 0;
}
```

### EJERCICIO 2

```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<int64_t> minutos;
vector<int64_t> descontentos;
vector<int64_t> personas;

void mergeSort(vector<int64_t>& personas){
    if (personas.size()>1) {
        vector<int64_t> subA(0);
        vector<int64_t> subB(0);
        for (int i=0; i<personas.size()/2; i++) {
            subA.push_back(personas[i]);
        }
        for (int i=(personas.size()/2); i<personas.size(); i++) {
            subB.push_back(personas[i]);
        }
        mergeSort(subA);
        mergeSort(subB);
        vector<int64_t> ordenado(0);
        int iA=0;
        int iB=0;
        while (iA<subA.size() && iB<subB.size()) {
            if (descontentos[subA[iA]]*minutos[subB[iB]] == descontentos[subB[iB]]*minutos[subA[iA]]) {
                if (descontentos[subA[iA]] >= descontentos[subB[iB]]) {
                    ordenado.push_back(subA[iA]);
                    iA++;
                } else {
                    ordenado.push_back(subB[iB]);
                    iB++;
                }
            } else if (descontentos[subA[iA]]*minutos[subB[iB]] > descontentos[subB[iB]]*minutos[subA[iA]]) {
                ordenado.push_back(subA[iA]);
                iA++;
            } else {
                ordenado.push_back(subB[iB]);
                iB++;
            }
        }
        while (iA<subA.size()) {
            ordenado.push_back(subA[iA]);
            iA++;
        }
        while (iB<subB.size()) {
            ordenado.push_back(subB[iB]);
            iB++;
        }
        personas = ordenado;
    }
}

int64_t correcciones_pendientes() {
    //ordenamos en base a descontento/minutos de mayor a menor
    mergeSort(personas);
    int64_t minutosAcumulados=0;
    int64_t descontentoAcumulado=0;
    for (int i=0; i<personas.size(); i++) {
        minutosAcumulados += minutos[personas[i]];
        descontentoAcumulado += descontentos[personas[i]]*minutosAcumulados;
    }
    return descontentoAcumulado;
}

int main() {
    int cantCasos = 0;
    cin >> cantCasos;
    int N = 0;
    vector<int64_t> respuestas(cantCasos);
    for (int c=0; c<cantCasos; c++) {
        cin >> N;
        minutos.resize(N);
        descontentos.resize(N);
        personas.resize(N);
        for (int i=0; i<N; i++) {
            int64_t minuto =0;
            cin >> minuto;
            minutos[i]=minuto;
            personas[i]=i;
        }
        for (int j=0; j<N; j++) {
            int64_t descontento=0;
            cin >> descontento;
            descontentos[j]= descontento;
        }
        respuestas[c] = correcciones_pendientes();
    }
    for (int i=0; i<respuestas.size(); i++) {
        if (i==respuestas.size()-1) {
            cout << respuestas[i] % (1000000000+7);
        } else {
            cout << respuestas[i] % (1000000000+7)<< endl;
        }
    }
    return 0;
}

----------------------------------------------------------


#include <iostream>
#include <vector>

using namespace std;

vector<int> saldos;
vector<char> sol_final;
int N;
int w;
int maxSuma;
vector<vector<int>> memo;

bool saldosSospechosos(int acum, int i) {
    if(i==N) {
        return acum == w;
    }
    if(memo[(acum+maxSuma)/100][i]!=-1){
        return memo[(acum+maxSuma)/100][i];
    }
    bool positivo = saldosSospechosos(acum + saldos[i], i+1);
    bool negativo = saldosSospechosos(acum - saldos[i], i+1);
    if(positivo && negativo){
        sol_final[i] = '?';
    } else if (positivo && !negativo) {
        if (sol_final[i]=='#') {
            sol_final[i]='+';
        } else if (sol_final[i]=='-') {
            sol_final[i]='?';
        }
    } else if (negativo && !positivo) {
        if (sol_final[i]=='#') {
            sol_final[i]='-';
        } else if (sol_final[i]=='+') {
            sol_final[i]='?';
        }
    }
    if (positivo || negativo) {
        memo[(acum+maxSuma)/100][i] = 1;
    } else {
        memo[(acum+maxSuma)/100][i] = 0;
    }
    return memo[(acum+maxSuma)/100][i];
}

int main(){
    int cantCasos;
    cin >> cantCasos;
    vector<vector<char>> resultados(cantCasos, vector<char>());
    for (int i=0; i<cantCasos; i++) {
        cin >> N;
        cin >> w;
        sol_final = vector<char>(N, '#');
        saldos = vector<int>(N);
        maxSuma=0;
        for (int j=0; j<N; j++) {
            int saldo;
            cin >> saldo;
            saldos[j]=saldo;
            maxSuma+=saldo;
        }
        memo = vector<vector<int>>((2*maxSuma/100)+1, vector<int>(N,-1));
        saldosSospechosos(0, 0);
        resultados[i]=sol_final;
    }
    for (int i=0; i<cantCasos; i++) {
        for (int j=0; j<resultados[i].size(); j++) {
            cout << resultados[i][j];
        }
        if (i != cantCasos-1) {
            cout << endl;
        }
    }
    return 0;
}
```

### EJERCICIO 3

```cpp
#include <iostream>
#include <vector>

using namespace std;

vector<int> saldos;
vector<char> sol_final;
int N;
int w;
int maxSuma;
vector<vector<int>> memo;

bool saldosSospechosos(int acum, int i) {
    if(i==N) {
        return acum == w;
    }
    if(memo[(acum+maxSuma)/100][i]!=-1){
        return memo[(acum+maxSuma)/100][i];
    }
    bool positivo = saldosSospechosos(acum + saldos[i], i+1);
    bool negativo = saldosSospechosos(acum - saldos[i], i+1);
    if(positivo && negativo){
        sol_final[i] = '?';
    } else if (positivo && !negativo) {
        if (sol_final[i]=='#') {
            sol_final[i]='+';
        } else if (sol_final[i]=='-') {
            sol_final[i]='?';
        }
    } else if (negativo && !positivo) {
        if (sol_final[i]=='#') {
            sol_final[i]='-';
        } else if (sol_final[i]=='+') {
            sol_final[i]='?';
        }
    }
    if (positivo || negativo) {
        memo[(acum+maxSuma)/100][i] = 1;
    } else {
        memo[(acum+maxSuma)/100][i] = 0;
    }
    return memo[(acum+maxSuma)/100][i];
}

int main(){
    int cantCasos;
    cin >> cantCasos;
    vector<vector<char>> resultados(cantCasos, vector<char>());
    for (int i=0; i<cantCasos; i++) {
        cin >> N;
        cin >> w;
        sol_final = vector<char>(N, '#');
        saldos = vector<int>(N);
        maxSuma=0;
        for (int j=0; j<N; j++) {
            int saldo;
            cin >> saldo;
            saldos[j]=saldo;
            maxSuma+=saldo;
        }
        memo = vector<vector<int>>((2*maxSuma/100)+1, vector<int>(N,-1));
        saldosSospechosos(0, 0);
        resultados[i]=sol_final;
    }
    for (int i=0; i<cantCasos; i++) {
        for (int j=0; j<resultados[i].size(); j++) {
            cout << resultados[i][j];
        }
        if (i != cantCasos-1) {
            cout << endl;
        }
    }
    return 0;
}
```

### EJERCICIO 4

```cpp
#include <iostream>
#include <vector>
#include <math.h>

using namespace std;

int N;
int K;
vector<int> ubi_puestos;
vector<int> sol_final;
int infinito = 1e9;
int costo_final = infinito;
vector<vector<pair<int,int>>> memoria;

bool menorLexico(vector<int >& a, vector<int >& b) {
    for (int i = 0; i < a.size(); i++) {
        if (a[i] < b[i]) {
            return true;
        }
    }
    return false;
}


pair<int, int> choripanes (int ult_prov, int prov_a_ubi, vector<int>& sol_parcial, int costo_acum) {
    // Si ya completamos la sollucion, nos fijamoe si es la minima
    if (prov_a_ubi == 0) {
        int costo_faltante =0;
        for (int i=ult_prov+1; i<N; i++) {
            costo_faltante += abs(ubi_puestos[i]-ubi_puestos[ult_prov]);
        }
        if (costo_acum+costo_faltante < costo_final || (costo_acum+costo_faltante==costo_final && menorLexico(sol_parcial, sol_final))) {
            costo_final = costo_acum+costo_faltante;
            sol_final = sol_parcial;
        }
        return make_pair(costo_faltante, -1);
    }
    
    //Rama invalida
    if (ult_prov != N && prov_a_ubi > N-ult_prov-1) {
        return make_pair(infinito, infinito);
    }
    
    // Si el camino ya está calculado, lo seguimos para reconstruir la solución. 
    if (memoria[ult_prov][prov_a_ubi] != make_pair(-1,-1)) {
        pair<int,int> camino_a_seguir = memoria[ult_prov][prov_a_ubi];
        sol_parcial.push_back(ubi_puestos[camino_a_seguir.second]);
        int costo = 0;
        if (ult_prov==N) {
            for (int j = 0; j < camino_a_seguir.second; j++) {
                costo += abs(ubi_puestos[camino_a_seguir.second] - ubi_puestos[j]);
            }
        } else {
            for (int j = ult_prov; j < camino_a_seguir.second; j++) {
                costo += min(abs(ubi_puestos[j] - ubi_puestos[ult_prov]), abs(ubi_puestos[camino_a_seguir.second] - ubi_puestos[j]));
            }
        }
        pair<int, int> poniendo_en_i = choripanes(camino_a_seguir.second, prov_a_ubi - 1, sol_parcial, costo_acum + costo);
        sol_parcial.pop_back();
        return make_pair(poniendo_en_i.first+costo, camino_a_seguir.second);
    }
    
    // Probamos para cada puesto desde el ultimo en que pusimos proveeduria hasta el ultimo puesto, cual genera el costo minimo 
    int i=0;
    if (ult_prov==N) {
        i=0;
    } else {
        i = ult_prov+1;
    }
    pair<int, int> minimo = make_pair(-1,-1);
    int costo_minimo = -1;
    int prov_para_minimo = -1;
    while (i<N) {
        sol_parcial.push_back(ubi_puestos[i]);
        int costo = 0;
        if (ult_prov==N) {
            for (int j = 0; j < i; j++) {
                costo += abs(ubi_puestos[i] - ubi_puestos[j]);
            }
        } else {
            for (int j = ult_prov; j < i; j++) {
                costo += min(abs(ubi_puestos[j] - ubi_puestos[ult_prov]), abs(ubi_puestos[i] - ubi_puestos[j]));
            }
        }
        pair<int, int> poniendo_en_i = choripanes(i, prov_a_ubi - 1, sol_parcial, costo_acum + costo);
        if (minimo == make_pair(-1,-1) ||  poniendo_en_i.first + costo <  costo_minimo) {
            minimo = poniendo_en_i;
            costo_minimo = poniendo_en_i.first + costo;
            prov_para_minimo = i;
        }
        sol_parcial.pop_back();
        i++;
    }
    // Una vez que sabemos donde ubicar la proveeduria, devolvemos el costo desde ult_prov hasta el final, habiendo ubicado proveeduria ahí. 
    memoria[ult_prov][prov_a_ubi] = make_pair(costo_minimo, prov_para_minimo);
    return memoria[ult_prov][prov_a_ubi];
}

int main() {
    int cantCasos;
    cin >> cantCasos;
    vector<int> costos(cantCasos);
    vector<vector<int>> posiciones(cantCasos);
    for (int i=0; i<cantCasos; i++) {
        cin >> N;
        cin >> K;
        ubi_puestos = vector<int>(N);
        for (int j=0; j<N; j++) {
            int puesto;
            cin >> puesto;
            ubi_puestos[j] = puesto;
        }
        sol_final = vector<int>(K, infinito);
        costo_final=infinito;
        vector<int> sol_parcial = vector<int>(0);
        memoria = vector<vector<pair<int,int>>>(N+1, vector<pair<int,int>>(K+1, make_pair(-1,-1)));
        pair<int,int> sol = choripanes(N, K, sol_parcial, 0);
        costos[i] = costo_final;
        posiciones[i] = sol_final;
    }
    for (int i=0; i<cantCasos; i++) {
        cout << costos[i] << endl;
        for (int j=0; j<posiciones[i].size(); j++) {
            if (j==posiciones[i].size()-1) {
                cout << posiciones[i][j];
            } else {
                cout << posiciones[i][j] <<  " ";
            }
        }
        if (i!=cantCasos-1) {
            cout << endl;
        }
    }
    return 0;
}
```
