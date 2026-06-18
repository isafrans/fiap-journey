# 📘 Capítulo 3 — Os Grafos que Modelam as Rotas de Distribuição de Energia

> Resumo do capítulo para a disciplina de **Estruturas de Dados / Algoritmos** — FIAP  
> Autor do material: Prof. (elaborado em 2026)
---

## 👤 Informações do Aluno

- **Nome:** Isabelle Caroline de Camargo Francisco  
- **RM:** 572096  
- **Curso:** Ciência da Computação EAD  
- **Instituição:** FIAP  
- **Disciplina:** Estruturas de Dados / Algoritmos  

---

## 📌 Assuntos Abordados

- **Grafos** — Teoria, terminologia e modelagem de redes
- **Estruturas de Dados** — Matriz de adjacência e Lista de adjacência
- **Algoritmos de Busca** — BFS (Busca em Largura) e DFS (Busca em Profundidade)
- **Caminho Mínimo** — Algoritmo de Dijkstra
- **Linguagem C** — Implementações práticas dos algoritmos

---

## 🧠 Visão Geral

Grafos são estruturas usadas para modelar **redes**: cidades e estradas, pessoas e relações sociais, roteadores e enlaces. Um grafo é formado por **vértices** (entidades) e **arestas** (conexões entre elas). O capítulo cobre desde o vocabulário básico até algoritmos clássicos de busca e caminho mínimo.

---

## 📂 Conceitos Fundamentais

### 1️⃣ Vértices, Arestas e Adjacência

- **Vértice**: representa uma entidade (cidade, pessoa, computador)
- **Aresta**: representa uma conexão entre dois vértices (estrada, amizade, cabo)
- **Adjacência**: dois vértices são adjacentes quando existe uma aresta ligando-os

> A pergunta central em grafos: *"quais são os vizinhos desse vértice?"*

---

### 2️⃣ Tipos de Grafos

#### 🔹 Não Direcionado vs. Direcionado

| Tipo | Arestas | Exemplo |
|---|---|---|
| **Não direcionado** | Sem seta — conexão bidirecional | Estradas de mão dupla |
| **Direcionado (dígrafo)** | Com seta — sentido importa | Links na web, dependências |

Na implementação: em grafos não direcionados, inserir `u–v` registra `v` como vizinho de `u` **e** `u` como vizinho de `v`. No dígrafo, `u→v` registra apenas a saída de `u`.

#### 🔹 Grau, In-degree e Out-degree

| Conceito | Significado |
|---|---|
| **Grau** | Número de arestas conectadas ao vértice (grafo não direcionado) |
| **In-degree** | Quantidade de arestas **entrando** no vértice |
| **Out-degree** | Quantidade de arestas **saindo** do vértice |

#### 🔹 Caminho, Ciclo e Conectividade

- **Caminho**: sequência de vértices ligados por arestas consecutivas
- **Ciclo**: caminho que retorna ao vértice inicial
- **Grafo conexo**: todos os vértices podem ser alcançados a partir de qualquer outro
- **Componente conexa**: subgrupo isolado dentro de um grafo desconexo

#### 🔹 Grafos Ponderados, Esparsos e Densos

| Tipo | Característica | Melhor representação |
|---|---|---|
| **Ponderado** | Arestas têm pesos (distância, custo, tempo) | Depende da densidade |
| **Esparso** | Poucas arestas em relação aos vértices | Lista de adjacência |
| **Denso** | Muitas arestas, próximo do máximo possível | Matriz de adjacência |

---

### 3️⃣ Representações na Memória

#### 🔹 Matriz de Adjacência

Uma matriz `V × V` onde `mat[u][v] = 1` indica aresta de `u` para `v`.

```c
void initMatrix(int adj[MAXV][MAXV], int V) {
    for (int i = 0; i < V; i++)
        for (int j = 0; j < V; j++)
            adj[i][j] = 0;
}

// Grafo não direcionado — registra nos dois sentidos
void addEdgeUndirected(int adj[MAXV][MAXV], int u, int v) {
    adj[u][v] = 1;
    adj[v][u] = 1;
}
```

#### 🔹 Lista de Adjacência

Um vetor de listas encadeadas — cada posição guarda os vizinhos do vértice.

```c
typedef struct Node {
    int v;
    struct Node* next;
} Node;

typedef struct Graph {
    int V;
    Node** adj;
} Graph;

void addEdgeUndirectedList(Graph* g, int u, int v) {
    addEdgeDirectedList(g, u, v);
    addEdgeDirectedList(g, v, u);
}
```

#### 🔹 Comparação: Matriz vs. Lista

| Operação | Matriz de adjacência | Lista de adjacência |
|---|---|---|
| **Espaço** | O(V²) | O(V + E) |
| **Verificar aresta (u,v)** | O(1) | O(grau(u)) |
| **Listar vizinhos de u** | O(V) — varre toda a linha | O(grau(u)) — só os existentes |
| **Inserir aresta** | O(1) | O(1) |
| **Remover aresta** | O(1) | O(grau(u)) |
| **BFS/DFS (impacto)** | Pode ser O(V²) em grafos esparsos | O(V + E) |

> ⚠️ **Regra prática**: grafo **esparso** → lista de adjacência. Grafo **denso** → matriz pode valer.

---

## 🔍 Algoritmos de Busca (Percursos)

### 4️⃣ BFS — Busca em Largura (Breadth-First Search)

Explora o grafo **por camadas**: primeiro todos os vizinhos diretos, depois os vizinhos dos vizinhos, e assim por diante. Usa uma **fila (queue)**.

**Mecânica:** retirar vértice da fila → percorrer vizinhos → enfileirar não visitados.

```c
// Estrutura de fila
#define QMAX 1000
typedef struct { int data[QMAX]; int front, rear; } Queue;
void qInit(Queue *q) { q->front = 0; q->rear = 0; }
int qEmpty(Queue *q) { return q->front == q->rear; }
void qPush(Queue *q, int x) { q->data[q->rear++] = x; }
int qPop(Queue *q) { return q->data[q->front++]; }

// BFS com matriz de adjacência
void bfsMatrix(int adj[MAXV][MAXV], int V, int s) {
    int visited[MAXV] = {0};
    Queue q; qInit(&q);
    visited[s] = 1;
    qPush(&q, s);
    while (!qEmpty(&q)) {
        int u = qPop(&q);
        printf("Visitou: %d\n", u);
        for (int v = 0; v < V; v++) {
            if (adj[u][v] == 1 && !visited[v]) {
                visited[v] = 1;
                qPush(&q, v);
            }
        }
    }
}
```

#### BFS com reconstrução de caminho (`parent[]`)

`parent[v] = u` significa "para chegar em v, passei por u". Para reconstruir o caminho, basta seguir os pais do destino até a origem.

```c
// parent[v] = u → registrado no momento da descoberta
if (adj[u][v] == 1 && !visited[v]) {
    visited[v] = 1;
    parent[v] = u;   // salva o pai
    qPush(&q, v);
}
```

---

### 5️⃣ DFS — Busca em Profundidade (Depth-First Search)

Avança o **máximo possível** em uma direção antes de retornar (backtracking). Usa **recursão** (ou pilha). Análoga a explorar um labirinto sempre entrando na primeira porta disponível.

```c
// DFS recursiva com lista de adjacência
void dfsList(Graph *g, int u, int visited[]) {
    visited[u] = 1;
    printf("Visitou: %d\n", u);
    for (Node *p = g->adj[u]; p != NULL; p = p->next) {
        int v = p->v;
        if (!visited[v])
            dfsList(g, v, visited);
    }
}
```

> ⚠️ O vetor `visited[]` é essencial para evitar loops infinitos em grafos com ciclos.

#### Componentes Conexas com DFS/BFS

Para encontrar componentes: iniciar percurso em cada vértice ainda não visitado. Tudo que ele alcançar pertence à mesma componente.

---

## 🗺️ Caminhos e Custos em Redes

### 6️⃣ Menor Caminho: Não Ponderado vs. Ponderado

| Tipo de grafo | Significado de "menor" | Algoritmo indicado |
|---|---|---|
| **Não ponderado** | Menor número de arestas | BFS |
| **Ponderado** | Menor soma de pesos | Dijkstra |

Exemplo: um caminho com 2 arestas pode ter custo 100, enquanto outro com 4 arestas custa apenas 12. BFS escolheria o de 2 arestas, mas o correto seria o de custo 12.

---

### 7️⃣ Algoritmo de Dijkstra

Encontra o caminho de **menor custo total** a partir de uma origem em grafos ponderados com **pesos não negativos**.

**Ideia central:**
1. Inicializa `dist[origem] = 0` e `dist[outros] = ∞`
2. A cada iteração, escolhe o vértice não finalizado com menor `dist[]`
3. Relaxa as arestas: se `dist[u] + w(u,v) < dist[v]`, atualiza `dist[v]`
4. Repete até finalizar todos os vértices alcançáveis

```c
#define INF 1000000000

int minDist(int dist[], int used[], int V) {
    int best = INF, idx = -1;
    for (int i = 0; i < V; i++)
        if (!used[i] && dist[i] < best) { best = dist[i]; idx = i; }
    return idx;
}

void dijkstra(int w[MAXV][MAXV], int V, int s, int dist[], int parent[]) {
    int used[MAXV];
    for (int i = 0; i < V; i++) { dist[i] = INF; parent[i] = -1; used[i] = 0; }
    dist[s] = 0;
    for (int iter = 0; iter < V; iter++) {
        int u = minDist(dist, used, V);
        if (u == -1) break;
        used[u] = 1;
        for (int v = 0; v < V; v++) {
            if (w[u][v] > 0 && !used[v] && dist[u] + w[u][v] < dist[v]) {
                dist[v] = dist[u] + w[u][v];
                parent[v] = u;
            }
        }
    }
}
```

#### Exemplo passo a passo (grafo A–G):

| Passo | Vértice escolhido | dist[A,B,C,D,E,G] |
|---|---|---|
| 0 | — (init) | [0, ∞, ∞, ∞, ∞, ∞] |
| 1 | A | [0, 4, 2, ∞, ∞, ∞] |
| 2 | C | [0, 3, 2, 10, 12, ∞] |
| 3 | B | [0, 3, 2, 8, 12, ∞] |
| 4 | D | [0, 3, 2, 8, 10, 14] |
| 5 | E | [0, 3, 2, 8, 10, 13] |

#### Reconstrução do caminho

A mesma função `printPath()` usada em BFS funciona em Dijkstra, pois `parent[]` tem o mesmo significado nos dois algoritmos.

```c
void printPathParent(int parent[], int s, int t) {
    int stack[MAXV], top = 0;
    for (int v = t; v != -1; v = parent[v]) {
        stack[top++] = v;
        if (v == s) break;
    }
    for (int i = top - 1; i >= 0; i--)
        printf("%d%s", stack[i], (i ? " -> " : "\n"));
}
```

#### ⚠️ Pesos negativos

Dijkstra **não funciona** com pesos negativos. Para esse caso, use o algoritmo **Bellman-Ford**.

---

## 📊 Comparação dos Algoritmos de Busca

| Algoritmo | Estratégia | Estrutura de apoio | Uso principal |
|---|---|---|---|
| **BFS** | Por camadas (largura) | Fila (Queue) | Menor caminho não ponderado, conectividade |
| **DFS** | Em profundidade | Recursão / Pilha | Componentes conexas, detecção de ciclos |
| **Dijkstra** | Menor custo incremental | Vetor `dist[]` + `used[]` | Menor caminho ponderado (pesos ≥ 0) |

---

## ⚠️ Cuidados Importantes

- **`visited[]` em DFS** é obrigatório — sem ele, ciclos causam loop infinito
- **Dijkstra exige pesos não negativos** — pesos negativos exigem Bellman-Ford
- **Escolha da representação importa**: lista de adjacência é mais eficiente para grafos esparsos em BFS/DFS (O(V+E) vs O(V²) na matriz)
- **`parent[]` é reutilizável**: a mesma lógica de reconstrução de caminho serve para BFS e Dijkstra
- **Grafo direcionado vs. não direcionado**: `addEdge` muda — não direcionado registra nos dois sentidos

---

## 🗂️ Resumo Geral

| Conceito | Definição |
|---|---|
| **Vértice** | Entidade da rede (nó) |
| **Aresta** | Conexão entre dois vértices |
| **Adjacência** | Dois vértices ligados por uma aresta |
| **Grau** | Número de arestas de um vértice |
| **In/Out-degree** | Entradas e saídas em grafos direcionados |
| **Componente conexa** | Subgrupo isolado de vértices |
| **BFS** | Busca em largura — por camadas, usa fila |
| **DFS** | Busca em profundidade — recursiva, usa pilha |
| **Dijkstra** | Caminho mínimo com pesos não negativos |

---

## 📎 Referências

- SEDGEWICK, R.; WAYNE, K. **Algorithms**. 4. ed. Addison-Wesley, 2016.
- CORMEN et al. **Introduction to Algorithms**. 3. ed. MIT Press, 2009.
- MIT OpenCourseware. **Introduction to Algorithms**, 2011.
- IME-USP. Estruturas de dados para grafos. Disponível em: [ime.usp.br](https://www.ime.usp.br/~pf/algoritmos_para_grafos/aulas/graphdatastructs.html)
- MOY, John. **OSPF Version 2 (RFC 2328)**. Standards Track, 1998.

---

*Resumo feito para fins de estudo — FIAP*
