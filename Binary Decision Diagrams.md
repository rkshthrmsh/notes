# Binary Decision Diagrams
Binary decision diagram (BDD) is a data structure used to represent a Boolean function as a rooted, directed, acyclic graph which contains several nodes and two terminal nodes. The terminal nodes are labeled 0 (FALSE) and 1 (TRUE). Each node is labeled by a Boolean variable $x_i$ and has two child nodes-- low child for when the variable evaluates to 0 and high child for 1. 

In popular usage, the term BDD almost always refers to Reduced Ordered Binary Decision Diagram. A BDD is called 
- *ordered* if the variables $x_i$ appear in the same order on all paths from the root and
- *reduced* if 
	- all isomorphic subgraphs have been merged and
	- any node whose two children are isomorphic have been eliminated


![[Pasted image 20221007223950.png]]

## Advantages
BDDs have several advantages over previous approaches to Boolean function manipulation:
- Most commonly encountered functions have a reasonable representation, i.e., function representations do not explode exponentially
- The performance of a program based on BDD algorithms when processing a sequence of operations degrades slowly, if at all. That is, the time complexity of any single operation is bounded by the product of the graph sizes for the functions being operated on.
- **ROBDD representation of a function is a canonical form, i.e., every function has a unique representation**. Therefore, testing for equivalence simply involves testing whether the two graphs match exactly, while testing for satisfiability simply involves comparing the graph to that of the constant function 0.

## Disadvantages
The size of the graph representing the function is highly sensitive to this ordering. The problem of computing an ordering that minimizes the size of the graph is itself a coNP-Complete problem. A human with some understanding of the problem domain can generally choose an appropriate ordering without great difficulty. It seems quite likely that using a small set of heuristics, the program itself could select an adequate ordering most of the time.

![[Pasted image 20221007224747.png]]

## References
https://www.cs.cmu.edu/~bryant/pubdir/ieeetc86.pdf

https://en.wikipedia.org/wiki/Binary_decision_diagram
