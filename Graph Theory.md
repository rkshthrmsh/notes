# Graph Theory
#graphTheory 

- A graph is a set of objects with a relationship between pairs of objects.
- Formally, a graph G is a pair of (V, E), wherein V is a set of Vertices / Nodes, and E is the set of all Edges in the graph.
$$G = (V, E)$$

## Definitions
### Degree of a Vertex
- The *degree of a vertex* is the number of its incident edges or neighbors. It is usually denoted as `deg(v)`. An isolated vertex has degree 0.
- The degree of a graph is the maximum degree of its vertices.
- A graph is called a *k-Regular Graph* if all vertices in the graph have the same degree *k*.
- The *complement of a graph* $G = (V, E)$ is a graph $\bar{G} = (V, \bar{E})$ on the same set of vertices $V$ and the set of edges $\bar{E}$ in which two vertices are connected if and only if they are not connected in G: $(u, v) \in \bar{E}$ if and only if $(u, v) \notin E$.

### Paths
- A *walk* in a graph is a sequence of edges such that each edge (except the first one) starts with a vertex where the previous edge ended.
- The *length* of a walk is the number of edges in it.
- A *path* is a walk where all edges are distinct.
- A *simple path* is a walk where all vertices and edges are distinct.
- A *cycle* in a graph is a path whose first vertex is the same as the last. Note that all the edges in a cycle are distinct.
- A *simple cycle* is one where all vertices (except the first one) are distinct. The first vertex is taken twice.

### Connectivity
- A graph is said to be *connected* if there is path between every pair of its vertices.
- Connectivity is transitive: if there is a path between two vertices u and v, and a path between v and w, then there exists a path between u and w.
- A *connected component* of a graph $G$ is a maximal connected subgraph of $G$.

### Directed Graphs
- A *directed graph* is a graph with directed edges. It is usually used to denote asymmetric relationship between the graph's vertices / nodes.
- The *indegree* of a vertex $v$ is the number of edges ending at $v$. 
- Similarly, the *outdegree* of a $v$ is the number of edges starting at $v$.

### Weighted Graphs
- A *weighted graph* is a graph in which each edge has an associated *weight*. 
- The weight of a path is the sum of the weights of its edges. In this context, the shortest path between two vertices is the path of minimum weight.
- The *distance* between two vertices is defined as the length of the shortest path between the vertices.

## Basic Graphs
- A *Path Graph* $P_n$, $n\geq 2$, consists of n vertices and n - 1 edges.
- A *Cycle Graph* $C_n$, $n \geq 3$, consists of n vertices and n edges. It is a path graph in which the last vertex is connected to the first vertex.
- A *Complete Graph* $K_n$, $n \geq 2$, contains n vertices and all edges between them (n(n - 1)/2).

## [[Graph Classes]]

## [[Graph Cycles]]

## [[Graph Parameters]]

## Flows and Matchings
### Network
- A network is a collection of $n$ vertices with a source and a destination.
- *Capacities* $c[i, j]$ within the network are the edges containing the flow information that connect vertex $i$ to vertex $j$.
  - $c[i, j] \geq 0 \quad c \in \mathbb{Z}$
  - In general, $c[i, j] \neq c[j, i]$

- Flow $f[i, j]$ is the amount of information flowing through an edge with capacity $c[i, j]$.
  - $f[i, j] \leq c[i, j]$
  - $f[i, j] = -f[j, i]$
- When there is no spillage, $\sum_{j}f[i, j] = 0$ for every $i$ except the source and destination vertices.
- Total flow within the network is conserved when thereis no spillage: $\sum_{j}f[A, j] = \sum_{i}f[i, B]$

- A *cut*  is a set of vertices that contains the source but not the destination.
- The total capacity of a cut is the sum of capacities of all outgoing edges in the cut.
- The total flow of the network never exceeds the total capacity of any cut.

### Ford and Fulkerson Theorem
- A *reachable* node is one for which a path exists between the source and the node with non-zero capacity.
- According to the Ford Fulkerson Theorem, **the maximal flow within a network is equal to the minimal cut**.
![[Pasted image 20230726093654.png]]

- [[Graph Classes#Hall's Theorem|Hall's Theorem]] is a corollary of Ford and Fulkerson Theorem. When we consider the bipartite graph as having a source and a destination, a complete matching from the left part to the right part is only possible if the flow from the source to the destination can be guaranteed.
![[Pasted image 20230726100136.png]]

