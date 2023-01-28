# Non-Deterministic Turing Machine
Unlike a [[Turing Machine|deterministic Turing Machine]] (DTM), a non-deterministic Turing machine or NTM is one in which the next state of the machine is not completely determined by its action and the current symbol it sees. In an NTM the finite set of instructions may prescribe more than one action to be performed for any given situation. 

One way to resolve which of the multiple actions the machine needs to perform is by imagining that the machine branches into many copies, each of which follows one of the possible transitions. This implies that the NTM has a computation tree and if at least one branch halts with an accept condition, the NTM accepts the input.

![[Pasted image 20221016134756.png]]

## Formal Definition
A nondeterministic Turing Machine M can be formally defined as a 7-tupe $M =\;\langle\Gamma, b, \Sigma, Q, q_0, F, \delta\rangle$ where:
- $\Gamma$ is a finite, non-empty set of tape alphabet symbols
- $b \in \Gamma$ is the blank symbol
- $\Sigma \subseteq \Gamma \setminus \{b\}$  is the set of input symbols allowed to appear in the initial tape contents
- $Q$ is a finite, non-empty set of states
- $q_0 \in Q$ is the initial state
- $F \subseteq Q$ is the set of final states or accepting states. The initial tape contents is said to be accepted by M if it eventually halts in a state from F
- $\delta :(Q \setminus F) \times \Gamma \times Q \times \Gamma \times \{L,N, R\}$  is a called the transition relation where L is left shift, R is right shift, and N is no change.

The difference with a deterministic Turing machine is that for deterministic Turing machines the transition relation is a function rather than just a relation, i.e., the collection of ordered pairs from "machine state" to "instructions" which could be non-unique unlike a function where the relation is always unique.

## Equivalence with DTM
DTMs can be considered as a special case of NTMs wherein the transition relation is a partial function.

### NTM Simulation Using DTM
Although NTMs appear to be more powerful than DTMs, the latter can be used to simulate NTMs.

#### Multiplicity of Configuration States
One approach is to use a DTM whose initial configuration represents multiple configurations of the NTM. In this simulation of an NTM, the DTM visits each NTM configuration in turn, executes a single step, and spawns new configuration whenever the transition relation defines multiple continuations.

#### Multiplicity of Tapes
Another simulation technique is to use 3-tape DTMs for an NTM. The first tape holds the original input, the second is used for a particular computation of the NTM, and the third encodes a path in the NTMs computation tree.

## References
https://en.wikipedia.org/wiki/Nondeterministic_Turing_machine