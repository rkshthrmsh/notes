# Formal Methods
Formal methods are mathematically rigorous techniques for the specification, development, and verification of software and hardware systems. The use of formal methods is motivated by the expectation that mathematical analysis can contribute to the reliability and robustness of a design. Using formal methods one can mathematically prove that a piece of code works as expected, or if not then the formal engine / solver should be able to tell you where your code is failing.

Formal methods can be used at a number of levels:
* Specification: Formal methods can be used to give a description of the system to be developed at any level of detail, and can then serve as the guide for  development activities. Furthermore, a well defined formal specification can serve as the benchmark to ensure that the requirements of the system have been satisfied.
* Development: Once the formal specification is produced it may be used as the guide while the system is developed. 
* Verification: Formal verification is the process of proving properties in a formal specification, or of proving that a model of the system satisfies its specification
	* Automated proofs: There is increasing interest in producing proofs of correctness of developed systems by automated means. Such techniques fall into three categories:
		*  [Automated theorem proving](https://en.wikipedia.org/wiki/Automated_theorem_proving "Automated theorem proving"), in which a system attempts to produce a formal proof from scratch, given a description of the system, a set of logical axioms, and a set of inference rules.
		-   [[Model checking]], in which a system verifies certain properties by means of an exhaustive search of all possible states that a system could enter during its execution.
		-   [Abstract interpretation](https://en.wikipedia.org/wiki/Abstract_interpretation), in which a system verifies an over-approximation of a behavioral property of the program, using a fixpoint computation over a (possibly complete) lattice representing it. 
	Some automated theorem provers require guidance as to which properties are "interesting" enough to pursue, while others work without human intervention. Model checkers can quickly get bogged down in checking millions of uninteresting states if not given a sufficiently abstract model.

## References
https://en.wikipedia.org/wiki/Formal_methods#Applications

https://zipcpu.com/formal/2018/07/14/dev-cycle.html