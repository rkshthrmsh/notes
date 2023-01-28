# Formal Methods in Hardware Design
The basic concept of [[Formal Methods|formal methods]] in hardware design is to declare a specification of valid and invalid states for the code. Then a theorem solver is applied to prove that an invalid state can never be entered from a valid one. If the solver is unable to prove it, a bug maybe present in the design or it could indicate an issue with the formal specification / assertions.

