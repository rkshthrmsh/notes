## Defining a Good RTL Design Hierarchy
#ultrafastDesign 

Good partition of the design based on functionality is necessary to be able to delegate the work and logically separate the design. However, care must also be taken to account for timing closure, runtime, and debugging. The following considerations during hierarchy planning will help in design closure.

### Add IO Components at the Top Level:
- IO components such as differential buffers, double data registers should be instantiated at the top level. This gives the designer full control and improves readability.  
- When using the tool to infer IO logic, ensure that the enable and input / output logic are at the same hierarchical level. If the logic is in different hierarchies and synthesis directives such as KEEP_HIERARCHY and DONT_TOUCH are present, the tool may not be able to infer buffers correctly.

### Insert Clocking Elements Near the Top Level
- Clocking elements should be inserted at the top level for easier clock sharing between modules. This improves resource utilization and power consumption.
- Aside from the modules containing the clock elements, the clock signal should only propagate downwards in the hierarchy. Any paths that go down and return to the top will cause issues (delta cycle problem) in simulations that are hard to debug.

### Register Data Paths at Logical Boundaries
- Outputs of modules at hierarchical boundaries should be registered. This ensures that critical paths are contained within a single module which makes it easier to analyze and fix timing paths. Traceability is improved because cross boundary optimization is minimized.
- Even inputs should be considered for registering at module boundaries.
- When paths are not registered at module boundaries synthesis should be run with 'hierarchy rebuilt' or 'flat' to allow cross hierarchy optimization.

### Address Floorplanning Considerations
- Good, manual floorplanning can be used to close timing when timing is not met using a standard flow. In such situations, containing the floorplan at the hierarchy level instead of cross hierarchy reduces the amount of efforts required to maintain the constraints.

### Optimize Hierarchy for Functional and Timing Debug
- For better traceability, related signals should be kept in the same hierarchy. This also ensures that after optimization by synthesis, signal names are easier to probe and trace.

### Apply Attributes at Module Level
- Instead of applying synthesis attributes at signal level, applying them at the module level keeps the code tidy and scalable. When applied at the module level, the attributes propagate down to the signal level. Such attributes also override any global synthesis settings.

### Optimize Hierarchy for Advanced Design Techniques
- Proper hierarchical design is necessary for enabling advanced design techniques such as bottom-up synthesis, partial reconfiguration, and out-of-context design.

### Example of Hierarchical Planning
![[Pasted image 20230306180718.png]]
- DSP_i and placement_flexibility_wrapper_i contain registers for both inputs and outputs to relieve timing issues.
- floorplanning_wrapper_i contains signals that are typically overloaded and can be hard to place and route without timing issues. The KEEP_HIERARCHY attribute ensures that there are is no optimization during synthesis that can collapse the boundaries.
- Finally, the SHREG_EXTRACT attribute prevents the synthesis tool from inferring the IO registers as shift registers.