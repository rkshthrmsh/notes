### Control Signals and Control Sets
A control set is any unique grouping of control signals such as set / reset, clock, clock enable, that drives a set of registers, LUTRAMs, or SRLs. All registers within a [[slice]] share common control signals.
- Designs with too many unique control sets will have wasted resources. Consider for example a slice in which only one of the registers has a load signal. The other registers in this slice become unusable. This results in higher power, lower clock frequency, and reduced flexibility in terms of placement. Therefore, the number of control sets should be minimized. A point worth noting is that undriven resets do not form a part of control sets since they are tied off locally.

#### [[Resets]]

#### Clock Enables
Improper placement of clock enable can negatively impact resource utilization, resource placement, power, and clock frequency.
- **Close off conditional statements and case statements(?)**. When these statements are incomplete, a clock enable is inferred which can be detrimental since it increases the control set count.
- On the other hand, inferring (or explicitly adding) clock enable to large signals (such as busses) helps reduce power consumption.
- **Code resets before clock enable**. Coding a clock enable first forces the rest into the data path of the sequential element and creates additional logic.

#### Controlling Reset and Enable Extraction with Synthesis Attributes
- Synthesis attributes (depends on vendor) can be used to ensure that the CE / R / S signals are connected to the respective pins instead of the D pin. By default, synthesis tools connect these signals to the D pin because it is easier to route to it.
- On the contrary, in situations where CE / R / S are routed to the corresponding pins, opposite attributes can be used to ensure they are routed to the D pin for easing PnR.