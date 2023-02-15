# Xilinx Ultrafast Design Methodology
#ultrafastDesign

The Ultrafast Design Methodology by Xilinx provides a set of best practices to streamline the design process.

## Introduction

![[Pasted image 20230213211922.png]]

### Ultrafast Design Methodology DRC
Xilinx recommends running design rule checks at each stage of the design and addressing critical warnings and warnings before advancing to the next stage. These stages are:
- Before synthesis, on the elaborated RTL design to validate RTL constructs
- After synthesis to validate netlist and constraints
- After implementation to validate timing and constraints

### Creating and Implementing a Hardware Design
When creating the design, it is important to consider the following points:
- Achieving the desired functionality
- Operating frequency
- Reliability
- Fitting within the silicon resource and power budget

### Maximizing Impact Early in the Development Cycle
Early stages of the design process have a higher impact on design performance, area, and power. Therefore, while trying to meet timing it is recommended that design entry and synthesis stages be revisited rather than implementation stages only.

<img align="left" src="C:\Users\rksht\Desktop\Notes\attachments\Pasted image 20230213212530.png" style="zoom: 40%; padding-right: 20px">
<img align="left" src="C:\Users\rksht\Desktop\Notes\attachments\Pasted image 20230213222057.png" style="zoom: 20%; padding-right: 20px">
### Validating at Each Design Stage
Xilinx recommends validating the design budgets such as area, power, latency, and timing at each design stage. These milestones should be after elaboration of RTL design, synthesis, and implementation.