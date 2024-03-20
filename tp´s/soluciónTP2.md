Todos los ejercicios fueron resueltos en C++

### EJERCICIO 1

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <list>
using namespace std;
int N;
int M;

void dfs(int v, int p, vector<list<int>>& listaAdy, vector<vector<int>>& tree_edges, vector<int>& estado, vector<int>& back_edges_con_extremo_inferior_en, vector<int>& back_edges_con_extremo_superior_en) {
    // EMPECE_A_VER = 1 , NO_LO_VI = -1, TERMINE_DE_VER = 0
    estado[v] = 1;
    for (int u : listaAdy[v]) {
        if (estado[u] == -1) {
            tree_edges[v].push_back(u);
            dfs(u, v, listaAdy, tree_edges, estado, back_edges_con_extremo_inferior_en, back_edges_con_extremo_superior_en);
        } else if (u != p) {
            if (estado[u] == 1)
                back_edges_con_extremo_inferior_en[v]++;
            else // estado[u] == TERMINE_DE_VER
                back_edges_con_extremo_superior_en[v]++;
        }
    }
    estado[v] = 0;
}

int cubren(int v, int p, vector<vector<int>>& tree_edges, vector<int>& back_edges_con_extremo_inferior_en, vector<int>& back_edges_con_extremo_superior_en, vector<int>& memo) {
    if (memo[v] != -1) return memo[v];
    int res = 0;
    for (int hijo : tree_edges[v]) {
        if (hijo != p) {
            res += cubren(hijo, v, tree_edges, back_edges_con_extremo_inferior_en, back_edges_con_extremo_superior_en, memo);
        }
    }
    res -= back_edges_con_extremo_superior_en[v];
    res += back_edges_con_extremo_inferior_en[v];
    memo[v] = res;
    return res;
}

vector<pair<int,int>> devolver_importantes (vector<pair<int,int>>& aristas, vector<list<int>>& listaAdy) {
    // Recorrer aristas e ir sacando cada una:
    vector<pair<int,int>> importantes(0);
    for (int i=0; i<aristas.size(); i++) {
        listaAdy[aristas[i].first].remove(aristas[i].second);
        listaAdy[aristas[i].second].remove(aristas[i].first);

        vector<vector<int>> tree_edges(N);
        vector<int> estado(N,-1);
        vector<int> back_edges_con_extremo_inferior_en(N);
        vector<int> back_edges_con_extremo_superior_en(N);
        dfs(0, -1, listaAdy, tree_edges, estado, back_edges_con_extremo_inferior_en, back_edges_con_extremo_superior_en);

        vector<int> memo(N, -1);
        int puentes=0;
        for(int i=1;i<N;i++){
            if (cubren(i, -1, tree_edges, back_edges_con_extremo_inferior_en, back_edges_con_extremo_superior_en, memo)==0){
                puentes++;
            }
        }
        if (puentes > 0) {
            if (aristas[i].first <= aristas[i].second) {
                importantes.push_back(aristas[i]);
            } else {
                importantes.push_back(make_pair(aristas[i].second, aristas[i].first));
            }
        }
        listaAdy[aristas[i].first].insert(listaAdy[aristas[i].first].end(), aristas[i].second);
        listaAdy[aristas[i].second].insert(listaAdy[aristas[i].second].end(), aristas[i].first);
    }
    // ordenamos lexicográficamente
    sort(importantes.begin(),importantes.end());
    return importantes;
}

int main() {
    int cantCasos;
    cin >> cantCasos;
    vector<vector<pair<int,int>>> importantes_casos(cantCasos);
    for (int c=0; c<cantCasos; c++) {
        cin >> N;
        cin >> M;
        vector<pair<int,int>> aristas(0);
        vector<list<int>> listaAdy(N, list<int>(0));
        for (int i=0; i<M; i++) {
            int u;
            int v;
            cin >> u;
            cin >> v;
            aristas.push_back(make_pair(u, v));
            listaAdy[v].insert(listaAdy[v].end(), u);
            listaAdy[u].insert(listaAdy[u].end(), v);
        }
        vector<pair<int,int>> importantes = devolver_importantes(aristas, listaAdy);
        importantes_casos[c] = importantes;
    }
    for (int c=0; c<cantCasos; c++) {
        if (c == cantCasos-1) {
            cout << importantes_casos[c].size() << endl;
            for (int i=0; i<importantes_casos[c].size(); i++) {
                if (i==importantes_casos[c].size()-1) {
                    cout << importantes_casos[c][i].first << " " << importantes_casos[c][i].second;
                } else {
                    cout << importantes_casos[c][i].first << " " << importantes_casos[c][i].second <<  endl;
                }
            }
        } else {
            cout << importantes_casos[c].size() << endl;
            for (int i=0; i<importantes_casos[c].size(); i++) {
                cout << importantes_casos[c][i].first << " " << importantes_casos[c][i].second <<  endl;
            }
        }
    }
    return 0;
}
```

### EJERCICIO 2

```cpp
#include <iostream>
#include <vector>
#include <tuple>
#include <queue>

using namespace std;
int N;
int M;

int derecha(int i, int j){
    return M * (i-1) + j + 1;
}
int izquierda(int i, int j){
    return M * (i-1) + j - 1;
}
int abajo(int i, int j){
    return M * (i+1) + j;
}
int arriba(int i, int j){
    return M * i + j;
}

bool enRango(int i, int j) {
    return i>=0 && i<N && j>=0 && j<M;
}

void bfs(int s, vector<vector<int>>& aristas, vector<bool>& visitado, vector<int>& distancia, vector<int>& manifestacion_en,int a) {
    visitado[s] = true;
    distancia[s] = 0;
    queue<int> q;
    q.push(s);
    while (!q.empty()) {
        int v = q.front(); q.pop();
        for (auto u : aristas[v]) {
            if (!visitado[u] && (manifestacion_en[u] == 0 || (distancia[v]+1 + a < manifestacion_en[u]))) {
                visitado[u] = true;
                distancia[u] = distancia[v] + 1;
                q.push(u);
            }
        }
    }
}

pair<int,int> ambulancia (vector<vector<int>>& aristas, vector<bool>& visitado, vector<int>& distancia, vector<int>& manifestacion_en, int hospital, int paciente) {
    bfs(hospital, aristas, visitado, distancia, manifestacion_en, 0);
    int ida = distancia[paciente];
    if (ida==-1) {
        return make_pair(-1,-1);
    }
    visitado = vector<bool> (N*M, false);
    distancia = vector<int> (N*M, -1);
    bfs(paciente, aristas, visitado, distancia, manifestacion_en, ida);
    int vuelta = distancia[hospital];
    if (vuelta==-1) {
        return make_pair(-1,-1);
    }
    return make_pair(ida, ida+vuelta);
}

int main() {
    int cantcasos;
    cin >> cantcasos;
    vector<pair<int,int>> resultados(cantcasos);
    for(int c = 0; c < cantcasos;c++){
        cin >> N;
        cin >> M;
        vector<vector<int>> ciudad(N, vector<int>(M));
        for(int i = 0; i < N; i++){
            for(int j = 0; j < M; j++){
                int x;
                cin >> x;
                ciudad[i][j] = x;
            }
        }
        int x1;
        int y1;
        cin >> x1;
        cin >> y1;
        int x2;
        int y2;
        cin >> x2;
        cin >> y2;
        vector<bool> visitado(N*M,false);
        vector<int> distancia(N*M,-1);
        vector<vector<int>> aristas(N*M,vector<int>(0));
        vector<int> manifestacion_en(0);
        int hospital;
        int paciente;
        for(int i = 0; i < N; i++){
            for(int j = 0; j < M; j++){
                manifestacion_en.push_back(ciudad[i][j]);
                int num_vertice;
                if (i==0) {
                    num_vertice = j;
                } else {
                    num_vertice = M * i + j;
                }
                if (enRango(i, j+1)) {
                    aristas[num_vertice].push_back(num_vertice+1);
                }
                if (enRango(i, j-1)) {
                    aristas[num_vertice].push_back(num_vertice-1);
                }
                if (enRango(i+1, j)) {
                    aristas[num_vertice].push_back(num_vertice+M);
                }
                if (enRango(i-1, j)) {
                    aristas[num_vertice].push_back(num_vertice-M);
                }
                if (i==x1 && j==y1) {
                    hospital = num_vertice;
                }
                if (i==x2 && j==y2) {
                    paciente = num_vertice;
                }
            }
        }
        pair<int,int> res = ambulancia(aristas, visitado, distancia, manifestacion_en, hospital, paciente);
        resultados[c] = res;
    }
    for(int c=0; c<resultados.size(); c++) {
        if (c==resultados.size()-1) {
            if (resultados[c]==make_pair(-1,-1)) {
                cout << "IMPOSIBLE";
            } else {
                cout << resultados[c].first << " " << resultados[c].second;
            }
        } else {
            if (resultados[c]==make_pair(-1,-1)) {
                cout << "IMPOSIBLE" << endl;
            } else {
                cout << resultados[c].first << " " << resultados[c].second << endl;
            }
        }
    }
    return 0;
}
```
### Ejercicio 3

```cpp
#include <iostream>
#include <vector>
#include <tuple>
#include <algorithm>
#include <math.h>
using namespace std;
int N;
int M;
int c;

struct DSU {
    DSU(int n){
        padre = vector<int>(n);
        for(int v = 0; v < n; v++) padre[v] = v;
        tamano = vector<int>(n,1);
    }

    int find(int v){
        while(padre[v] != v) v = padre[v];
        return v;
    }

    void unite(int u, int v){
        u = find(u); v = find(v);
        if(tamano[u] < tamano[v]) swap(u,v);
        //ahora u es al menos tan grande como v
        padre[v] = u;
        tamano[u] += tamano[v];
    }

    vector<int> padre;
    vector<int> tamano;

    //tamano[v] <= n
    //INV: si padre[v] != v entonces tamano[padre[v]] >= 2*tamano[v]
};


tuple<long long, long long, long double> kruskal(vector<tuple<long double, int,int,long long,long long>>& E, int n){
    long double res = 0;
    long long D = 0;
    long long R = 0;
    //mergeSort(E, 0, E.size()-1, cAux);
    sort(E.begin(), E.end());
    DSU dsu(n);

    int aristas = 0;
    for(int i=0; i<E.size(); i++){
        //u y v estan en distinta cc?
        tuple<long double, int,int,long long,long long> arista = E[i];
        if(dsu.find(get<1>(arista)) != dsu.find(get<2>(arista))) {
            dsu.unite(get<1>(arista),get<2>(arista));
            res += get<0>(arista);
            aristas++;
            D += get<3>(arista);
            R += get<4>(arista);
        }
        if(aristas == n-1) break;
    }
    return make_tuple(D, R, res);
}

tuple<long long, long long, long double> costo_edificios(vector<tuple<int,int,long long,long long>>& aristas, long long N, long double c) {
    vector<tuple<long double, int,int,long long, long long>> aristas_nuevas(M);
    for (int i=0; i<M; i++) {
        aristas_nuevas[i] = make_tuple(c*((long double)get<3>(aristas[i]))-((long double)get<2>(aristas[i])), get<0>(aristas[i]), get<1>(aristas[i]), get<2>(aristas[i]), get<3>(aristas[i]));
    }
    tuple<long long, long long, long double> costo = kruskal(aristas_nuevas, N);
    return costo;
}

tuple<long long, long long, long double> edificios (vector<tuple<int,int,long long,long long>>& aristas, long long sumaD) {
    long double izquierda = 0.00001;
    long double derecha = ((long double)(sumaD));
    tuple<long long, long long, long double> actual;
    while (fabs(izquierda - derecha) >= 0.00001) {
        long double medio = izquierda + ((derecha - izquierda) / 2);
        c = medio;
        actual = costo_edificios(aristas, N, medio);
        if (get<2>(actual) <= 0) { // nos quedamos con los de la derecha incluido c
            izquierda = medio;
        } else { // nos quedamos con los de la izquierda sin incluir a c
            derecha = medio-0.00001;
        }
    }
    return actual;
}

int main() {
    int cantCasos;
    cin >> cantCasos;
    vector<pair<long long, long long>> resultados(cantCasos);
    for (int c=0; c<cantCasos; c++) {
        cin >> N;
        cin >> M;
        vector<tuple<int,int,long long,long long>> aristas(M);
        long long sumaD = 0;
        for (int i=0; i<M; i++) {
            int u;
            int v;
            long long d;
            long long r;
            cin >> u;
            cin >> v;
            cin >> d;
            cin >> r;
            aristas[i] = make_tuple(u-1,v-1,d,r);
            sumaD += d;
        }
        tuple<long long, long long, long double> res = edificios(aristas, sumaD);
        resultados[c] = make_pair(get<0>(res), get<1>(res));
    }
    for (int i=0; i<cantCasos; i++) {
        if (i==cantCasos-1) {
            cout << resultados[i].first << " " << resultados[i].second;
        } else {
            cout << resultados[i].first << " " << resultados[i].second << endl;
        }
    }
    return 0;
}
```
