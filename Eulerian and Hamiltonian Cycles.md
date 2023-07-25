## Eulerian and Hamiltonian Cycles
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