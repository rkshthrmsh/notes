## RTL Coding Guidelines
#ultrafastDesign 

### Control Signals and Control Sets
A control set is any unique grouping of control signals such as set / reset, clock, clock enable, that drives a set of registers, LUTRAMs, or SRLs. All registers within a [[slice]] share common control signals.
- Designs with too many unique control sets will have wasted resources. Consider for example a slice in which only one of the registers has a load signal. The other registers in this slice become unusable. This results in higher power, lower clock frequency, and reduced flexibility in terms of placement. Therefore, the number of control sets should be minimized. A point worth noting is that undriven resets do not form a part of control sets since they are tied off locally.