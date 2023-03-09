## Fault Removal
#dependability 

Removing or reducing the number of fault can be performed both during the development and operational life of a system. 

### Developmental Phase
Fault removal during development consists of three steps: verification, diagnosis, and correction.
#### Verification
Verification: Process of checking whether the system adheres to given properties (verification conditions). Verification steps can be classified based on how it exercises the system:
- Static verification does not involve actual execution.
- Dynamic verification is verification through exercising the system. 
	- If the input is symbolic, it is called symbolic execution.
	- On the other hand, if actual inputs are used it is called testing. When faults or errors are a a part of the test pattern, the testing method involves *fault injection*.

Designing a system to facilitate its verification is called *design for verifiability*.


Diagnosis kicks in when there are faults that prevented the verification conditions from being fulfilled, and the necessary corrections are performed.

### Operational Phase
Fault removal during operational phase can be either corrective or preventive maintenance.
- Corrective maintenance aims to remove faults that have produced one or more errors and have been reported.
- Preventive maintenance is a preemptive measure to uncover and remove faults that could lead to errors.