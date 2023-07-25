# NP-Hard
#complexityClass

**NP-hardness** or  non-deterministic polynomial-time hardness is the defining property of a class of problems that are informally "at least as hard as the hardest problems in [[NP]]".

- A more precise specification is: a problem _H_ is NP-hard when every problem _L_ in NP can be reduced in polynomial time to _H_; that is, assuming a solution for _H_ takes 1 unit time, _H_'s solution can be used to solve _L_ in polynomial time.
- As a consequence, finding a polynomial time algorithm to solve any NP-hard problem would give polynomial time algorithms for all the problems in NP.
- It is suspected, but not proven, that there are no polynomial-time algorithms for NP-hard problems.

## Reduction
- Reduction is an algorithm for transforming one problem into another problem.
- Intuitively, problem A is reducible to problem B, if an algorithm for solving problem B efficiently (if it exists) could also be used as a subroutine to solve problem A efficiently.
- If such an algorithm exists, solving A cannot be harder—in terms of the required computational resources—than solving B.


## References
[1] https://en.wikipedia.org/wiki/NP-hardness
[2] https://en.wikipedia.org/wiki/Reduction_(complexity)