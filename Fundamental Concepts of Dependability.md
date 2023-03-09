# Fundamental Concepts of Dependability
#dependability

## The Principal Concepts
Four fundamental properties can be used to characterize a computing system: functionality, performance, cost, and dependability. The following are key terms and definitions from Avižienis *et al.* [1].
- The **function** of the system is what it is intended for as described by the specification.
- **Dependability** is defined as "the ability to deliver service that can justifiably be trusted.
- The **service** delivered by a system is its behaviour as perceived by its user(s).
- A **user** is another system or human that interacts with this system via a **system interface**.
- A system **failure** occurs when the delivered service deviates from correct service.
- Correct service is deemed to be delivered when the system implements the expected function. 
- A system may fail in two situations—when it does not comply with the specification or because the specification was not adequately defined. When a system fails it is said to provide **incorrect service**. The duration of incorrect service is called **outage**.
- An **error** is the part of the system that may cause a subsequent failure, when it reaches the service interface. An error is *detected* if it is indicated by an *error message* or *error signal*. Errors that are present but not detected are *latent* errors.
- A **fault** is the cause an error. A fault is *active* when it produces an error and *dormant* otherwise.

The dependability of a system consists of three parts: [[The Threats to Reliability|threats]] to, [[The Attributes of Dependability|attributes]] of, and [[The Means to Attain Dependability|means]] for dependability. These are illustrated in the following figure [1].

![[Pasted image 20230222111308.png]]

## References
[1] J.-C. Laprie and B. Randell, “Algirdas Aviˇz ienis UCLA, Los Angeles, CA, USA and Vytautas Magnus U. Kaunas, Lithuania aviz@cs.ucla.edu”. Fundamental Concepts of Dependability