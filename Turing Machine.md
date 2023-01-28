# Turing Machine

![[Pasted image 20221016132636.png]]

A Turing Machine is a mathematical representation of a simple abstract machine that is capable of implementing any computer algorithm. It is composed of:
- A *tape* which is divided into cells with each cell containing a symbol from a *finite alphabet* interpretable by the machine. The alphabet contains a special *blank symbol* and one or more symbols. The tape is arbitrarily extendable to the left or right to ensure that the Turing machine is supplied with as much tape as necessary for computation. 
- A *head*  that can interpret symbols on the tape and move the tape left or right by one cell at a time.
- A *state register* that stores one of *finitely many states* of the Turing machine. A special *initial state* is part of the finitely many states.
- A *finite table of instructions* which instructs the machine to do the following in sequence based on the current state of the machine and the tape symbol it is reading:
	1. Either erase or write a symbol to the cell on the tape
	2. Move the head to the left, right, or stay in the same place
	3. Assume a new state or stay in the same one

![[Pasted image 20221016125016.png]]

## Formal Definition
A Turing Machine M can be formally defined as a 7-tupe $M =\;\langle\Gamma, b, \Sigma, Q, q_0, F, \delta\rangle$ where:
- $\Gamma$ is a finite, non-empty set of tape alphabet symbols
- $b \in \Gamma$ is the blank symbol
- $\Sigma \subseteq \Gamma \setminus \{b\}$  is the set of input symbols allowed to appear in the initial tape contents
- $Q$ is a finite, non-empty set of states
- $q_0 \in Q$ is the initial state
- $F \subseteq Q$ is the set of final states or accepting states. The initial tape contents is said to be accepted by M if it eventually halts in a state from F
- $\delta :(Q \setminus F) \times \Gamma \not \to Q \times \Gamma \times \{L,N, R\}$  is a partial function called the transition function where L is left shift, R is right shift, and N is no change. This function defines the instructions to be executed.

[Example of a Turing Machine](https://en.wikipedia.org/wiki/Turing_machine#:~:text=.-,The%207%2Dtuple%20for%20the%203%2Dstate%20busy%20beaver%20looks%20like,.,-State%20table%20for)

## Turing Completeness
Turing completeness is the ability for a system of instructions to simulate a Turing machine. A programming language that is Turing complete is theoretically capable of expressing all tasks accomplishable by computers; nearly all programming languages are Turing complete if the limitations of finite memory are ignored.

## References
https://en.wikipedia.org/wiki/Turing_machine