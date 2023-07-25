## Connected Components
#graphTheory
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