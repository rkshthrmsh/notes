# SAT Solver
In computer science and formal methods, SAT solver is a program which aims to solve the [[Boolean Satisfiability Problem]]. Given an expression in Boolean variables, a SAT solver determines whether the expression is satisfiable or not. 

According to the Cook-Levin theorem, Boolean satisfiability is an [[NP-Complete]] problem in general, which implies that only algorithms with exponential worst-case complexity are known. Despite this, efficient and scalable algorithms for SAT have been developed.

SAT solvers often express the satisfiability problem in a [[Conjunctive Normal Form]]. In general there is no SAT solver that performs better than all other solvers on all SAT problems. An algorithm might perform well for problem instances others struggle with, but will do worse with other instances. Furthermore, given a SAT instance, there is no reliable way to predict which algorithm will solve this instance particularly fast.


## References
https://en.wikipedia.org/wiki/SAT_solver