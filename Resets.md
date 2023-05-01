#### Resets
#ultrafastDesign 

##### Test
Resets can significantly impact the frequency, power, and area of a design. The use of resets must be thought about carefully and limited as much as possible since misplaced resets can lead to usage of additional resources and thus suboptimal power consumption and performance.
- Resets maybe needed more on control logic paths and likely not at all on data path logic. 
- **There is no reason to code a global reset signal for the sole purpose of initializing a device on power-up**. Most Xilinx devices have a global set / reset (GSR). On power-up, all sequential elements are guaranteed to be reset. Adding resets should be given due consideration to: 
	- Limit the overall fanout of the reset net.
	- Reduce the amount of interconnect necessary.
	- Simplify the timing of reset paths
	- Improve frequency, area, and power.
 
- **Synchronous resets should be used instead of asynchronous ones**. When a reset is necessary, it is better to use synchronous ones because:
	- Synchronous resets can map to more resources in the device, and offer more routing flexibility. These resets can be remapped the data path of a register in situations where dense control sets need to be rerouted.
	- Routing asynchronous resets increases complexity unless it's set globally.
	- Asynchronous resets have a higher probability of corrupting memory contents (of BRAMs, LUTRAMs, and SRLs).
- Additional considerations:
	- The clock acts as a filter for glitches on synchronous resets. However, if the glitch occurs at an active clock edge, the flip-flop may become metastable.
	- It is necessary to stretch the synchronous reset signal enough to ensure that an active clock edge occurs during the assertion period.
	- Although the relative timing between asynchronous reset is irrelevant, it is necessary to synchronize the deassertion of the reset signal to ensure that there are no setup and hold time violations.

When eliminating resets, care should be taken to ensure that asynchronous resets are not automatically inferred. This can be done by writing **separate procedural statements** for sequential elements with and without resets as shown in the code snippet below.

```systemverilog
always @(posedge clk or posedge rst)
begin
	if (rst)
	begin
		// din_dly1 <= 16'd0;    // Infers async reset
		// din_dly2 <= 16'd0;    // Infers async reset
		dout <= 16'd0;
	end
	else
	begin
		din_dly1 <= din;
		din_dly2 <= din_dly1;
		dout <= din_dly2;
	end
end
```

Instead the following method should be used.
```systemverilog
always @(posedge clk)
begin
	din_dly1 <= din;
	din_dly2 <= din_dly1;
end

always @(posedge clk or posedge rst)
begin
	if (rst)
	begin
		dout <= 16'd0;
	end
	else
		dout <= din_dly2;
	begin
end
```