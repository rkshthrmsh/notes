### Coding Styles to Improve Power
#ultrafastDesign 

- Gate Clock or Data Paths:
	- Gating clocks or data paths is a common technique to reduce power consumption when the path outputs are not used.
	- This can also be done automatically by using the `power_opt_design` power optimization tool.
- Maximize Gating Elements:
	- Maximize the number of elements affected by a gating signal. For example, it is more optimal to gate an entire clock domain at its source rather than gating each load with a CE signal.
- Use CE Pins of Dedicate Clock Buffers:
	- Using CE pins of clock buffers to minimize clock tree usage is more efficient than inserting LUTs.
- Use Case Block When Priority Encoder Not Needed:
	- When priority encoder is not needed, use a case statement instead of an if-else or ternary operator.  
```SystemVerilog
if (reg1) val = reg_in1;
else if (reg2) val = reg_in2;
else if (reg3) val = reg_in3;
else val = reg_in4;
```

		Recommended alternative
```SystemVerilog
(* parallel_case *) casex ({reg1, reg2, reg3})
  1xx: val = reg_in1;
  01x: val = reg_in2;
  001: val = reg_in3;
  default: val = reg_in4;
endcase
```