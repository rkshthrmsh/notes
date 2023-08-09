## Graph Parameters
#graphTheory 

### Graph Coloring
- A graph coloring is a coloring of the graph vertices such that no pair of adjacent vertices share the same color.
- The chromatic number $\chi(G)$ of a graph $G$ is the smallest number of colors needed to color the graph.
- The chromatic number of
	- Fully connected / complete graph $K_n$ is $n$.
	- Path graph $P_n$ is 2.
	- Cycle graph $C_n$ is 2 if number of nodes is even and 3 if it is odd.
	- Bipartite graphs $K_{n, m}$ is 2.
	- Planar graph is at most 4. This is a restatement of the Appel Haken theorem (1976): **Every map can be colored with 4 colors**.

- Brooks Theorem (1941): **A graph $G$ of maximum degree $\Delta$ can be colored with $\Delta$ colors, unless $G$ is full ($K_n$) or a cycle of odd length ($C_{2k + 1}$)**.

### Graph Cliques
- A *clique* of a graph is a set of vertices such that every two vertices are connected by an edge.
- A *maximal clique* is a clique which is not contained in any other clique, i.e., it cannot be extended to a larger clique.
- A *maximum clique* is a clique such that there are no cliques with more vertices.
- The *clique number* $\omega(G)$ of a graph $G$ is the number of vertices in its maximum clique.

### Independent Sets
- An *independent set* (IS) of a graph is a set of vertices such that no two vertices are connected by an edge.
- A *maximal independent set* is an IS which is not contained in any other IS, i.e., cannot be extended to a larger IS.
- A *maximum independent set* is an IS such that there are no IS's with more vertices.
- The *independence number* $\alpha(G)$ of a graph $G$ is the number of vertices in its maximum IS.

### Relation between Cliques and Independent Sets
$$\omega(G) = \alpha(\bar{G})$$
Where $\bar{G}$ is the complementary graph of $G$.

### Relation between Coloring and Independent Sets
- Each color in a colored graph forms an independent set. There are no edges between the vertices of the same color.
- **For every graph $G$ on $n$ vertices it holds that $$\chi(G) \cdot \alpha(G) \geq n$$

### Mantel's Theorem & Turan's Theorem
- Mantel's Theorem: **If a graph $G$ on $n$ vertices contains no triangles, then it has at most $\lfloor{n^2 / 4}\rfloor$ edges**.
- Turan's Theorem: **If a graph $G$ on $n$ vertices contains no $K_{r+1}$, then it has at most $(1 - \frac{1}{r})\frac{n^2}{2}$ edges**. 

## Ramsey Numbers
- For two integers $k$, $l$, the Ramsey Number $R(k, l)$ is the minimum number such that every graph with at least $R(k, l)$ vertices must have either:
	- a clique of size $k$, or
	- an independent set of size $l$
- $R(3, 3)$ = 6; $R(4, 4) = 18$; however, $R(5, 5)$ is unknown, just that $43 \leq R(5, 5) \leq 48$.
- Ramsey's Theorem: **There exists an upper bound on Ramsey number $R(k, l)$ such that**: $$R(k, l) \leq R(k - 1, l) + R(k, l- 1)$$

## Vertex Covers
- A *vertex cover* of a graph $G$ is a set of vertices $C$ such that every edge of $G$ is connected to some vertex in $C$.
- A *minimal vertex cover* is a vertex cover which does not contain other vertex covers.
- A *minimum vertex cover* is a vertex cover of the smallest size.
- The size of a minimum vertex cover is denoted by $\beta(G)$.
- **A set of vertices is a vertex cover $iff$ its complement is an independent set** $$\beta(G) + \alpha(G) = n$$

### Konig's Theorem
- It is a fact that the vertices of any maximal matching form a vertex cover (not necessarily minimal).
- According to Konig's Theorem: **In a bipartite graph $G = ((L \cup R), E)$, the number of edges in a maximum matching equals the number of vertices in a minimal vertex cover.