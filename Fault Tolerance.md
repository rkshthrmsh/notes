## Fault Tolerance
#dependability 

Fault tolerance is designed to ensure the delivery of correct service in the presence of active faults. Key concepts include error detection and subsequent system recovery. There are two classes of error detection techniques.
- Concurrent error detection takes place during service delivery.
- Preemptive error detection takes place while service is suspended.

Recovery transforms a system with one or more errors and faults into a state without detected errors and faults that can be activated again through error handling and fault handling.

### Error Handling
Error Handling eliminates errors from the system.
- Rollback is said to be done when the system transformation involves returning the system state to a saved state before, called a checkpoint, error detection.
- Rollforward occurs when the state without errors is a new state.

### Fault Handling
Fault Handling prevents identified faults from being activated again. It involves four steps.
- Fault diagnosis to identify and record the cause of errors in terms of both location and type.
- Fault isolation to physically and logically isolate the faulty component(s) from participation in service delivery.
- System reconfiguration to either switch in spare components or redistribute tasks among non-failed components.
- System reinitialization to check, update, and record the new system configuration.

Fault masking is when the availability of sufficient redundancy enables service recovery without explicit error detection.

Fault handling may be preceded by preemptive error detection (like built-in self-tests (BISTs)) at power up. Other examples of preemptive error detections include memory scrubbing, audit programs, and software rejuvenation (to prevent the effects of aging software).

Mechanisms that implement fault tolerance must themselves be made immune to the very same faults they are handling. This makes fault tolerance a recursive concept. Examples are self-checking checkers, voter replication, etc.