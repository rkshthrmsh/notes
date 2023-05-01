### Fault Tolerance Techniques
#dependability 

A common technique for fault tolerance is **redundancy**. With redundancy, systems can operate even in the presence of faults or failures. Redundancy can be implemented *statically*--wherein faults are masked by a polling mechanism involving multiple processors running identical applications; or *dynamically*--which involves "fault detection followed by substitution of the faulty unit with a spare one" [1].

Triple Modular Redundancy is a common example of static redundancy. Examples of dynamic redundancy are
- Simplex Processing with Backup Spares: Uses a single processor for nominally executing a program with a backup spare to switch into in case of a failure.
- Duplicated Processing without a Backup Spare: This is the same as Dual Modular Redundant systems, the outputs of two processors running the same computation are compared. On disagreement, FDIR is executed to identify the failed processor.

#### Techniques for Distributed Systems
[[Distributed Computing]] systems use multiple processors for enhancing the efficiency and reliability of the overall system. The following are some of the techniques used to improve reliability.

##### Replication 
In replication information is shared to ensure data consistency between redundant processors. There are two main replication schemes and some variant versions. 
- "In *active* replication processes are replicated in multiple processors to provide fault tolerance" [1]. The invocating process sends a processing request to all replicas and awaits a reply. If there is no fault, then the invocating process uses the first reply. In case of a fault, the invocating process waits for at leas K + 1 replicas in a K fault tolerant system.
- In the *semi active* technique, a "leader" computes and outputs messages while the remaining replicas called "followers" compute but do not output. 
- In the *passive* technique, only the primary processor responds to the invocating process' call and generates an output. The internal state of the replicas is regularly updated to ensure consistency between the replicas.
- In the *semi passive* technique, the processing request is sent to all replicas, but only one replica handles the request. After the output is generated this replica responds to the invocating process and generates an update message for the remaining replicas.

##### Distributed Recovery Block
In this technique two processors are used in each node, with each of these processors running a copy of the program. One processor in the node pair is active while the other one is inactive or in "shadow" mode. The correctness of the results between the two processors is checked by an acceptance test.

##### Redundant Execution
Redundant execution can be implemented at the instruction level or task level. At the instruction level, each instruction is duplicated and the results are compared for errors. Similarly, in the task level implementation each software task is executed twice or more times to avoid temporary faults. Since the computing resource is reused for the duplicated executions, this technique is also called time redundancy.

##### Network Surveillance
In the network surveillance technique, a communication network is used for fault detection and configuration. The most basic version involves a master that periodically makes call requests to other nodes in the system for detection of failures. Other schemes in this technique are:
- Periodic reception history broadcast in which each node will broadcast periodic reception history which includes the health status of the available nodes.
- Supervisory based network surveillance wherein two types of nodes--worker and supervisor. "The worker nodes pass the health statuses of its neighbor nodes to the supervisor, which sends status messages to all the other nodes in the network".

#### References
[1] “Survey and future directions of fault-tolerant distributed computing on board spacecraft | Elsevier Enhanced Reader.” [https://reader.elsevier.com/reader/sd/pii/S0273117716304689?token=D6BDBC50FE73E324672A6ED812D9F0B2A1D848738035B8D193968688807F965A2CA455BC15CAE8DAD21AD8C18B9CD8CE&originRegion=us-east-1&originCreation=20230405160624](https://reader.elsevier.com/reader/sd/pii/S0273117716304689?token=D6BDBC50FE73E324672A6ED812D9F0B2A1D848738035B8D193968688807F965A2CA455BC15CAE8DAD21AD8C18B9CD8CE&originRegion=us-east-1&originCreation=20230405160624) (accessed Apr. 05, 2023).