### Know What You Infer
#ultrafastDesign 

Since the RTL code finally maps onto the resources on the device, it is important to understand what is being inferred. The inferences are usually device specific. For Xilinx devices refer to UG949. It is recommended to check the availability of hardware resources and how effectively they are utilized early in, and at each step of, the design cycle.

#### Inferring RAM and ROM
RAMs and ROMs can be inferred in multiple ways, each with its own advantages and disadvantages.

| Technique     | Advantages                        | Disadvantage                              |
| ------------- | --------------------------------- | ----------------------------------------- |
| Inference     | - Highly portable                 | - No full control over all configurations |
| (recommended) | - Clear coding intent             | - Might produce less optimal results      |
|               | - Fast simulation                 |                                           |
|               |                                   |                                           |
| XPMs          | - Portable between Xilinx devices | - Limited to supported XPM options        |
|               | - Fast simulation                 |                                           |
|               | - Predictable QoR                 |                                           |
|               | - Support for asymmetric width    |                                           |
|               |                                   |                                           |
| Instantiation | - Highest level of control        | - Less portable code                      |
|               | - Access to all configuration     | - Unclear coding intent                   |
|               |                                   |                                           |
| IP Catalog    | - Optimized results               | - Less portable code                      |
|               | - Simple to specify and configure | - Core management                         |
|               |                                   |                                           |

- Output Pipeline Registers: Using an output pipeline register is recommended for design closure. A second output register is beneficial as slice output registers have faster clock to out timing than a block RAM register. Registers should be placed at the same level of hierarchy as the RAM array.
- Input Pipeline Register: Along similar lines as the previous recommendation, placing input pipelining registers will ease timing closure, especially when large RAM arrays are used.

#### Coding for Optimal DSP and Arithmetic Interface
DSP block are already highly pipelined with multiple registers for high-speed operation. To fully utilize these blocks:
- A set condition or an asynchronous reset should not be used.
- Use signed arithmetic operations. With unsigned arithmetic, the resource usage efficiency maybe reduced.
- Make use of the pre-adder and post-adder resources in the DSP slices to improve resource packing efficiency.
- Evaluate resources within each slice of a device to understand whether operations such as addition need to be implemented as ternary (A+B+C) or binary for the best resource usage efficiency.

#### Initialization of All Inferred Registers, SRLs, and Memories
The GSR (Global Set / Reset) net initializes all sequential elements to the specified initial value in the HDL code. Therefore it is highly recommended that each such element be provided an initial value instead of indiscriminate reset conditions just for the sake of initialization. This also ensures that functional simulation closely matches the synthesis. An example is shown here:

```systemverilog
reg register1 = 1'b0;
reg register2 = 1'b1;

// Another option is to use the Verilog initial statement
reg [3:0] register3;
initial begin
  register3 = 4'b1011;
```

#### Deciding When to Instantiate or Infer
In most situations inferring the primitives is desirable. However, there are certain scenarios in which it is instantiation is preferred to inference.
- HDL Language Restrictions: Some primitives such as double data rate IOs cannot be described in VHDL, for example.
- Hardware Complexity: It is easier to instantiate elements such as IO SerDes than to infer it.
- Synthesis Tool Limitations: With hard FIFOs from RTL descriptions.