# NP
#complexityClass
According to computational complexity theory, NP or nondeterministic polynomial time is a complexity class comprising the set of decision problems for which the problem instances, where the answer is "yes", have proofs verifiable in polynomial time by a [[Turing Machine|deterministic Turing machine]] or alternatively **the set of decision problems that can be solved in polynomial time by a [[Nondeterministic Turing Machine]].**

![[Pasted image 20221014152247.png]]

- NP is the set of decision problems *solvable* in polynomial time by a non-deterministic Turing machine.
- NP is the set of decision problems *verifiable* in polynomial time by a deterministic Turing machine.

- The complexity class [[P]] is contained in NP because if a problem is solvable in polynomial time, then a solution is also verifiable in polynomial time by simply solving the problem.
- As the diagram shows (under the assumption of $P\neq NP$), NP contains many more problems, the hardest of which are called [[NP-Complete]].

## References
https://en.wikipedia.org/wiki/NP_(complexity)