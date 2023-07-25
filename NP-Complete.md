# NP-Completeness
#complexityClass 

NP-Complete is short for Nondeterministic Polynomial-Time Complete. A decision problem $C$ is NP-complete if:
1. $C$ is in [[NP]]
2. $C$ is [[NP-Hard]], i.e., Every problem in NP is [[NP-Hard#Reduction|reducible]] to $C$ in polynomial time.

- A consequence of this definition is that if we had a polynomial time algorithm for $C$, we could solve all problems in NP in polynomial time.
- It is not known whether every problem in NP can be solved quickly. However, if any NP-complete problem can be solved quickly, then every problem in NP can, because the definition of an NP-complete problem states that every problem in NP can be reduced to every NP-complete problem in polynomial time.
- Therefore, it is often said that NP-complete problems are harder or more difficult than NP problems in general.

## References
https://en.wikipedia.org/wiki/NP-completeness