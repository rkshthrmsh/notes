# Model Checking
Model checking is a method for verifying whether a finite state model of a system meets a given formal specification. It is often used for finding logical errors (‘falsification’) rather than for proving that they do not exist (‘verification’). To solve such a problem algorithmically, both the model of the system and its specification are formulated in a precise mathematical language. This is typically achieved by expressing them both in logic within a particular structure, i.e., a set along with a collection of finitary operations and relations that are defined on it.

The design to be verified is modeled as a finite state machine, and the specification is formalized by writing temporal logic properties. The reachable states of the design are then traversed in order to verify the properties. In case the property fails, a counterexample is generated in the form of a sequence of states [1].

## References
[1] A. Biere, A. Cimatti, E. M. Clarke, O. Strichman, and Y. Zhu, “Bounded Model Checking,” in _Advances in Computers_, vol. 58, Elsevier, 2003, pp. 117–148. doi: [10.1016/S0065-2458(03)58003-2](https://doi.org/10.1016/S0065-2458(03)58003-2). #on-Zotero 

https://en.wikipedia.org/wiki/Model_checking