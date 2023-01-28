# Symbolic Model Checking
Early [[Model checking|model checking]] algorithms explicitly enumerated all states of the system in order to check the correctness of a specification. The enumerated states can grow exponentially leading to a state space explosion problem. This limited early systems implementations to handle only small designs. 

Symbolic Model Checking made the necessary breakthrough towards wide usage of these techniques. In symbolic model checking, sets of states are represented implicitly using Boolean functions. Instead of storing explicit list of states, Boolean functional representations are used thereby reducing memory requirement [1]. Manipulating Boolean functions can be done efficiently with Reduced Ordered [[Binary Decision Diagrams]] (ROBDD or BDD for short).


## References
[1] A. Biere, A. Cimatti, E. M. Clarke, O. Strichman, and Y. Zhu, “Bounded Model Checking,” in _Advances in Computers_, vol. 58, Elsevier, 2003, pp. 117–148. doi: [10.1016/S0065-2458(03)58003-2](https://doi.org/10.1016/S0065-2458(03)58003-2). #on-Zotero 