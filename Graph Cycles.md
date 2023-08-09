## Graph Cycles
#graphTheory 

### Handshaking Lemma
- A graph has an even number of vertices of odd degree.
- For any graph $G(V, E)$, the sum of degrees of all its nodes is twice the number of edges:
$$\sum_{v\in{V}}^{}degree(v) = 2\cdot|E|$$
- Each edge contributes 2 to the sum of degrees

### Connected Components
- The nodes of an undirected graph can be partitioned into subsets *connected   components*.
	- Any node belongs to exactly one connected component
	- Any two nodes from the same connected component have a path between them
	- Any two nodes from different connected components are not connected
- Theorem of Lower Bound: An undirected graph $G(V, E)$ has at least $|V| - |E|$ connected components.
	- The Lower Bound theorem is useless for graphs with $|E| \geq |V|$.

### Directed Acyclic Graphs (DAGs)
- A DAG is a direct graph without cycles. This occurs naturally: Examples include citation graphs, course prerequisite graphs.
- A *topological ordering* of a directed graph is an ordering of its vertices such that, for each edge $(u, v)$, $u$ comes before $v$.
![[Pasted image 20230724152844.png]]

- **Every DAG has a topological ordering.** The proof of this theorem involves:
	- Showing that every DAG has a *sink*—a node with no outgoing edges,
	- Taking a sink, putting it to the end of the ordering, removing it from the graph (this maintains the acyclic nature), and repeating.
 - **Every DAG has a sink**.
 
### Strongly Connected Components
- In a directed graph, two nodes $u$ and $v$ are said to be connected, if there exists a path from $u$ to $v$ **and** a path from $v$ to $u$.
- Nodes of any directed graph can be partitioned into subsets called strongly connected components (SCC) where:
	- every node belongs exactly to one SCC
	- nodes from the same SCC are connected (as defined above)
	- nodes from different SCCs are not connected

- Condensing the SCCc of a directed graph gives a DAG.
![[Pasted image 20230724154744.png]]

### Eulerian Cycle
- An Eulerian cycle is a [[Graph Theory#Paths|cycle]] that visits every edge exactly once.
- **A connected *undirected* graph contains an Eulerian cycle, $iff$ the degree of every node is even**.
- **A strongly connected *directed* graph contains an Eulerian cycle, $iff$, for every node, its in-degree is equal to its out-degree**. If some node is imbalanced, there is clearly no Eulerian cycle. 
- For a path instead of a cycle on an undirected graph, the graph is allowed to contain two imbalanced nodes: one for the starting node (with out-degree > in-degree by 1) and an ending node (with out-degree < in-degree by 1). Adding a directed edge from the ending node to starting node will generate a Eulerian cycle.

### Hamiltonian Cycles
- A Hamiltonian cycle visits every node of a graph exactly once.
- No simple criteria is known for the Hamiltonian cycle problem.
- No polynomial time algorithm is known to find Hamiltonian cycle.
- In fact, the question of whether there is a polynomial time algorithm for the Hamiltonian cycle problem is the [[P versus NP]] problem.

- The right formulation can be used to convert the problem of finding Hamiltonian Cycles (which is intractable; class NP) could be reduced to the problem of finding Eulerian cycles by using the De Bruijn graphs.