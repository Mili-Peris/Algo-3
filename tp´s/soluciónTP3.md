Todos los ejercicios fueron resueltos en C++

### EJERCICIO 1

```cpp
#include <iostream>
#include <vector>
#include <tuple>
#include <list>

using namespace std;
int N;
int M;

void dfs(int v, int p, vector<tuple<vector<pair<int,int>>, vector<pair<int,int>>>>& lista_ady, vector<int>& estado, list<int>& orden_topo) {
    // EMPECE_A_VER = 1 , NO_LO_VI = -1, TERMINE_DE_VER = 0
    estado[v] = 1;
    for (pair<int,int> u : get<1>(lista_ady[v])) {
        if (estado[u.first] == -1) {
            dfs(u.first, v, lista_ady, estado, orden_topo);
        }
    }
    estado[v] = 0;
    orden_topo.push_front(v);
}

vector<int> camino_minimo_DAG (vector<tuple<vector<pair<int,int>>,vector<pair<int,int>>>>& lista_ady, int pablo) {
    vector<int> distancias = vector<int>(4*N+1, 1e9);
    distancias[pablo]=0;
    list<int> orden_topo;
    vector<int> estado = vector<int>(4*N+1, -1);
    dfs(pablo, -1, lista_ady, estado, orden_topo);
    for (int x : orden_topo) {
        for (pair<int,int> z : get<0>(lista_ady[x])) {
            distancias[x] = min(distancias[x], distancias[z.first] + z.second);
        }
    }
    return distancias;
}

int main() {
    int c;
    cin >> c;
    vector<int> resultados(0);
    for (int i=0; i<c; i++) {
        cin >> N;
        cin >> M;
        vector<tuple<int,int>> pasadizos(M);
        for (int p=0; p<M; p++) {
            int i;
            cin >> i;
            int f;
            cin >> f;
            pasadizos[p] = make_tuple(i-1,f-1);
        }
        vector<tuple<vector<pair<int,int>>, vector<pair<int,int>>>> lista_ady(4*N+1, make_tuple(vector<pair<int,int>>(0),vector<pair<int,int>>(0)));
        // El primer vecindario de la lista de cada vértice es el de entrada, el segundo es el de salida

        // Usamos pasadizo: para cada pasadizo <i,f>
        // agregamos i0 --> f1, i1-->f2, i2-->f3, cada una con costo=2

        // Usamos muralla: para cada salon i,
        // agregamos i0 --> i+1_0, i1 --> i+1_1, i2-->i+1_2, i3-->i+1_3, cada una con costo=1

        // Si queremos el vértice i_j, su posicion en la lista es 4j+i

        // Además agregamos un vértice ficticio al final unido a los 4 vértices correspondientes a la posición de Betún con aristas de costo 0.
        for (int s=0; s<N-1; s++) {
            // Agregamos s_0 al vecindario de entrada de s+1_0, s_1 al vecindario de entrada de s+1_1, s_2 al de s+1_2, y s_3 al de s+1_3
            // Agregamos s+1_0 al vecindario de salida de s_0, s+1_1 al de s_1, s+1_2 al de s_2, y s+1_3 al de s_3
            for (int i=0; i<4; i++) {
                get<0>(lista_ady[4*(s+1)+i]).push_back(make_pair(4*s+i,1));
                get<1>(lista_ady[4*s+i]).push_back(make_pair(4*(s+1)+i,1));
            }
        }
        for (int p=0; p<M; p++) {
            tuple<int,int> pasadizo = pasadizos[p]; // <i,f>
            // Agregamos i_0 al vecindario de entrada de f_1 y agregamos f_1 al vecindario de salida de i_0
            // Agregamos i_1 al vecindario de entrada de f_2 y agregamos f_2 al vecindario de salida de i_1
            // Agregamos i_2 al vecindario de entrada de f_3 y agregamos f_3 al vecindario de salida de i_2
            for (int h=0; h<3; h++){
                get<0>(lista_ady[get<1>(pasadizo)*4+h+1]).push_back(make_pair(4*get<0>(pasadizo)+h,2));
                get<1>(lista_ady[get<0>(pasadizo)*4+h]).push_back(make_pair(4*get<1>(pasadizo)+h+1,2));
            }
        }
        for (int k=0; k<4; k++) {
            // Agregamos las aristas (N-1)_k a N
            // Agregamos (N-1)_k al vecindario de entrada de 4N
            // Agregamos 4N al vecindario de salida de (N-1)_k
            get<0>(lista_ady[4*N]).push_back(make_pair(4*(N-1)+k,1));
            get<1>(lista_ady[4*(N-1)+k]).push_back(make_pair(4*N,1));
        }
        vector<int> distancias = camino_minimo_DAG(lista_ady, 0);
        resultados.push_back(distancias[4*N]);
    }
    for(int caso=0; caso<c; caso++) {
        if (caso == c-1) {
            cout << resultados[caso];
        } else {
            cout << resultados[caso] << endl;
        }
    }
    return 0;
}
```

### EJERCICIO 2

```cpp
#include <vector>
#include <iostream>
#include <algorithm>

// Para ver si es posible o no, vemos para todos k,h,a si L[k,h] <= L[k,a] + L[a,h]. Si se cumple esto, es posible
// Idea: Armamos matriz de latencias. Luego para cada i,j unimos v_i con v_j con una arista de costo L[i,j]. Vemos las que hacen que sea ineficiente (las aristas ij tq existe un a que hace que M[i,j]=M[i,a]+M[a,j), y las sacamos.
// Luego vemos las distancias en el grafo resultante y devolvemos eso.

using namespace std;
int N;

vector<vector<int>> floyd_warshall (vector<vector<int>> &matriz_ady) {
    vector<vector<int>> distancias = matriz_ady;
    vector<vector<int>> L_anterior = matriz_ady;
    for (int k=0; k<N; k++) {
        for (int i=0; i<N; i++) {
            for (int j=0; j<N; j++) {
                if (L_anterior[i][j] > L_anterior[i][k]+L_anterior[k][j] && L_anterior[i][k]!= 1e9 && L_anterior[k][j] !=1e9) {
                    distancias[i][j] = L_anterior[i][k]+L_anterior[k][j];
                }
            }
        }
        L_anterior = distancias;
    }
    return distancias;
}

int main() {
    int c;
    cin >> c;
    vector<bool> res_posibilidad(c, true);
    vector<vector<vector<int>>> resultados_distancias(c);
    for (int i=0; i<c; i++) {
        cin >> N;
        vector<vector<int>> matriz_latencias(N, vector<int>(N,-1));
        for (int j=0; j<N; j++) {
            for (int l=0; l<j; l++) {
                int latencia;
                cin >> latencia;
                matriz_latencias[j][l] = latencia;
                matriz_latencias[l][j] = latencia;
            }
        }
        vector<vector<int>> matriz_ady(N, vector<int>(N, 1));
        for (int i=0; i<N; i++) {
            matriz_ady[i][i]=0;
        }
        // Vemos si es posible:
        for (int k=0; k<N; k++) {
            for (int h=0; h<N; h++) {
                for (int a=0; a<N; a++) {
                    if (matriz_latencias[k][h] > matriz_latencias[k][a] + matriz_latencias[a][h] && a!=h && a!=k && k!=h) {
                        res_posibilidad[i] = false;
                    }
                }
            }
        }
        // Vemos las que hacen que sea ineficiente:
        for (int k=0; k<N; k++) {
            for (int h=0; h<N; h++) {
                for (int a=0; a<N; a++) {
                    if (matriz_latencias[k][h] == matriz_latencias[k][a] + matriz_latencias[a][h] && a!=h && a!=k && k!=h) {
                        matriz_ady[k][h] = 1e9; // borramos la arista k-->h
                        break;
                    }
                }
            }
        }
        vector<vector<int>> distancias = floyd_warshall(matriz_ady);
        resultados_distancias[i] = distancias;
    }
    for (int i=0; i<c; i++) {
        if (i!=c-1) {
            if (res_posibilidad[i]==false) {
                cout << "IMPOSIBLE" << endl;
            } else {
                cout << "POSIBLE" << endl;
                for (int k=0; k<resultados_distancias[i].size(); k++) {
                    if (k==resultados_distancias[i].size()-1) {
                        for (int h=0; h<resultados_distancias[i].size(); h++) {
                            if (h==resultados_distancias[i].size()-1) {
                                cout << resultados_distancias[i][k][h];
                                cout << endl;
                            } else {
                                cout << resultados_distancias[i][k][h] << " ";
                            }
                        }
                    } else {
                        for (int h=0; h<resultados_distancias[i].size(); h++) {
                            if (h==resultados_distancias[i].size()-1) {
                                cout << resultados_distancias[i][k][h];
                                cout << endl;
                            } else {
                                cout << resultados_distancias[i][k][h] << " ";
                            }
                        }
                    }
                }
            }
        } else {
            if (res_posibilidad[i]==false) {
                cout << "IMPOSIBLE" << endl;
            } else {
                cout << "POSIBLE" << endl;
                for (int k=0; k<resultados_distancias[i].size(); k++) {
                    for (int h=0; h<resultados_distancias[i].size(); h++) {
                        if (h==resultados_distancias[i].size()-1) {
                            cout << resultados_distancias[i][k][h];
                            if (k!=resultados_distancias[i].size()-1) {
                                cout << endl;
                            }
                        } else {
                            cout << resultados_distancias[i][k][h] << " ";
                        }
                    }
                }
            }
        }
    }
    return 0;
}
```

### EJERCICIO 3

```cpp
#include <iostream>
#include <vector>
#include <queue>

using namespace std;
int INF = 1e9;

int n;
vector<vector<int>> capacity;
vector<vector<int>> adj;
/*
 The matrix capacity stores the capacity for every pair of vertices.
 adj is the adjacency list of the undirected graph, since we have also to use the reversed of directed edges when we are looking for augmenting paths.
 The function maxflow will return the value of the maximal flow.
 During the algorithm, the matrix capacity will actually store the residual capacity of the network.
 The value of the flow in each edge will actually not be stored, but it is easy to extend the implementation - by using an additional matrix - to also store the flow and return it.
 */
int bfs(int s, int t, vector<int>& parent) {
    fill(parent.begin(), parent.end(), -1);
    parent[s] = -2;
    queue<pair<int, int>> q;
    q.push({s, INF});

    while (!q.empty()) {
        int cur = q.front().first;
        int flow = q.front().second;
        q.pop();

        for (int next : adj[cur]) {
            if (parent[next] == -1 && capacity[cur][next]) {
                parent[next] = cur;
                int new_flow = min(flow, capacity[cur][next]);
                if (next == t)
                    return new_flow;
                q.push({next, new_flow});
            }
        }
    }

    return 0;
}
int maxflow(int s, int t) {
    int flow = 0;
    vector<int> parent(n);
    int new_flow;
    while (new_flow = bfs(s, t, parent)) {
        flow += new_flow;
        int cur = t;
        while (cur != s) {
            int prev = parent[cur];
            capacity[prev][cur] -= new_flow;
            capacity[cur][prev] += new_flow;
            cur = prev;
        }
    }

    return flow;
}

int main() {
    int cantcasos;
    cin >> cantcasos;
    vector<int> resultados(cantcasos);
    for (int c=0; c<cantcasos; c++) {
        int N;
        cin >> N;
        // Guardamos el tablero de entrada:
        vector<vector<int>> tablero (N, vector<int>(N));
        for (int i=0; i<N; i++) {
            for (int j=0; j<N; j++) {
                int estado;
                cin >> estado;
                tablero[i][j] = estado;
            }
        }
        // La idea es representar cada parte de una columna/fila que tiene una o mas roturas como si fueran columnas/filas distintas.
        // O sea, cada fila y columna puede tener subfilas y subcolumnas.
        // Cada subfila y cada subcolumna va a ser un vértice distinto.
        // Armamos una matriz en donde nos vamos guardando para cada posición, un par <vertice i, vertice j> que indica que la subfila correspondiente a vi
        // tiene intersección con la subcolumna correspondiente a vj.
        // Los vértices los numeramos de la siguiente manera: s, v1,...,vn, vn+1, ..., vn+m, t, donde los vertices de 1 a n son las subfilas y los de n+1 a n+m son las subcolumnas

        // La matriz va a tener (-1,-1) si en la posición había rotura
        vector<vector<pair<int,int>>> matriz_intersecciones(N, vector<pair<int,int>>(N, make_pair(-1,-1)));

        // Primero, asignamos los vertices correspondientes a las subfilas
        int vertice_a_asignar=0; // Arranca en 1 porque el vértice 0 es s
        int cant_subfila_actual = 0;
        int cant_subfilas=0;
        for (int i=0; i<N; i++) {
            vertice_a_asignar++;
            cant_subfilas++;
            cant_subfila_actual=0;
            for (int j=0; j<N; j++) {
                if (tablero[i][j]==1 && cant_subfila_actual>0) {
                    vertice_a_asignar++;
                    cant_subfila_actual = 0;
                    cant_subfilas++;
                } else if (tablero[i][j]==0) {
                    matriz_intersecciones[i][j].first=vertice_a_asignar;
                    cant_subfila_actual++;
                }
            }
        }
        // Ahora asignamos los vertices correspondientes a las subcolumnas
        int cant_subcolumna_actual = 0;
        for (int j=0; j<N; j++) {
            vertice_a_asignar++;
            cant_subcolumna_actual=0;
            for (int i=0; i<N; i++) {
                if (tablero[i][j]==1 && cant_subcolumna_actual>0) {
                    vertice_a_asignar++;
                    cant_subcolumna_actual = 0;
                } else if (tablero[i][j]==0) {
                    matriz_intersecciones[i][j].second=vertice_a_asignar;
                    cant_subcolumna_actual++;
                }
            }
        }
        int t = vertice_a_asignar+1;
        int s = 0;
        vector<vector<int>> lista_ady(t+1, vector<int>(0));
        vector<vector<int>> capacidades(t+1, vector<int>(t+1, 0));
        // Agregamos las aristas entre subfilas y subcolumnas:
        for (int i=0; i<N; i++) {
            for (int j=0; j<N; j++) {
                pair<int,int> vecinos = matriz_intersecciones[i][j];
                if (vecinos!= make_pair(-1,-1)) {
                    lista_ady[vecinos.first].push_back(vecinos.second);
                    lista_ady[vecinos.second].push_back(vecinos.first);
                    capacidades[vecinos.first][vecinos.second]=1;
                }
            }
        }
        // Agregamos las aristas entre s y subfilas (desde el vertice 1 al cant_subfilas son vertices correspondientes a subfilas)
        for (int i=1; i<=cant_subfilas; i++) {
            lista_ady[s].push_back(i);
            lista_ady[i].push_back(s);
            capacidades[s][i]=1;
        }
        // Agregamos las aristas entre subcolumnas y t
        for (int i=cant_subfilas+1; i<t; i++) {
            lista_ady[i].push_back(t);
            lista_ady[t].push_back(i);
            capacidades[i][t]=1;
        }

        adj=lista_ady;
        capacity = capacidades;
        n = t+1;
        resultados[c] = maxflow(s,t);
    }
    for (int c=0; c<cantcasos; c++) {
        if (c!=cantcasos-1) {
            cout << resultados[c] << endl;
        } else {
            cout << resultados[c];
        }
    }
    return 0;
}
```
