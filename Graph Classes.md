## Graph Classes
#graphTheory 

### Trees

Trees are defined by the following equivalent definitions:
- A *Tree* is a connected graph without cycles. 
- It is a connected graph on n vertices with n - 1 edges.
- A graph is a tree iff there is a unique simple path between any pair of its vertices.

- A path graph is always a tree.

- A *spanning tree* of a graph $G$, is a subgraph of $G$ which is a tree containing all vertices of $G$.
- A *minimum spanning tree* of a weighted graph $G$ is a spanning tree of the smallest weight. The problem of finding a minimum spanning tree has implications for optimizing network connectivity.

### Bipartite Graphs

- A graph $G$ is *Bipartite* if its vertices can be partitioned into two disjoint sets $L$ and $R$ such that:
	- Every edge of $G$ connects a vertex $L$ to a vertex in $R$.
	- No edge connects two vertices from the same set.
- $L$ and $R$ are called the *parts* of $G$.
![[Pasted image 20230724194906.png]]
- **A graph is bipartite, $iff$ it has no cycles of odd length**.
- A cycle graph with even vertices is a bipartite graph. Not true if number of vertices is odd.
- Trees are bipartite as well.
- A *Complete Bipartite Graph* $K_{L, R}$ is a bipartite graph in which all vertices of $L$ are connected to all vertices of $R$ and vice versa.

### Matchings
- A matching in a graph is a set of edges without common vertices.
- A *maximal matching* is a matching which cannot be extended to a larger matching.
- A *maximum* matching is a matching of the largest size.

- In bipartite graphs, matchings can be used to find mapping solutions from one part to the other.

### Hall's Theorem
- **In a bipartite graph $G = (L \cup R, E)$, there is a matching which covers all vertices from L, $iff$ for every subset of vertices $S \subseteq L$, $$|S| \leq |N(S)|$$
- Here, the *Neighborhood* $N(S)$ of $S$, a subset of vertices $S \subseteq V$ of graph $G = (V, E)$, is defined as the set of all vertices connected to at least one vertex in $S$.


### Planar Graphs

- A graph is said to be *planar* if it can be drawn in the plane such that its edges do not meet (cross) except at their end points.
- Geographical maps are always equivalent to planar graphs.
![[Pasted image 20230724202237.png]]

### Euler's Formula
**Let $G$ be a connected planar graph, then** $$v - e + f = 2$$ Where, $v$ is the number of vertices, $e$ is the number of edges, and $f$ is the number of faces in the drawing of $G$.
![[Pasted image 20230724202807.png]]
