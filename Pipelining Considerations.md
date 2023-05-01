#### Pipelining Considerations
#ultrafastDesign 

Pipelining refers to the restructuring of long datapaths with several levels of logic by distributing them over multiple clock cycles to improve performance. Faster clock frequency and data throughput can be achieved at the expense of latency and overhead logic management.

- Consider pipelining upfront instead of later since the latter will often propagate latency, leading to major redesign of portions of the code. Pipelining early in the design can improve timing closure, implementation runtime, and device power.

- While pipelining be aware of the following conditions:
	- Cones of logic with large fanins.
	- Blocks with restricted placement, or slow clock-to-out or large setup requirements.
	- Forced placement (of IO or other blocks) that causes long routes.
	- Logic comprised of large XOR functions: Large XOR often have high switch rates leading to large dynamic power dissipation. Pipelining these can reduce switching and positively impact power.
 
- Proper pipelining can be achieved by:
	- Adding registers before or after the logic to be retimed, preferably within the same hierarchy
	- Using the global synthesis attribute or BLOCK_SYNTH.RETIMING option which analyzes the timing of a path and moves the register to improve timing.
	- Using `retiming_forward` and `retiming_backward` synthesis attributes which can be added to specific registers.
 
- Determining Whether Pipelining is Needed:
	- Use the `report_design_analysis` command with `-logic_level_distribution` switch to determine the logic level distribution for each clock group. Paths with zero logic levels can be used to understand where the design needs to be modified.
 
- Balance Latency:
	- While balancing latency by adding pipeline stages, target control paths instead of data paths which are usually much wider and would require more flip-flop and register resources.
	- **The optimal LUT:FF ratio is 1:1**. Designs with significantly more LUTs will increase unrelated logic packing into slices which will increase routing complexity and reduce QoR.
 
- Balance Pipeline Depth and SRL Usage:
	- With deeply pipelined designs, use as many SRLs as possible to avoid significant increases in register utilization.
	- Synthesis attributes can be used to infer SRLs. 
	  `srl_style = "srl" | "reg_srl" | "srl_reg" | "reg_srl_reg"`
	- To take advantage of SRLs for pipelining:
		- Ensure that none of the intermediate FFs have a reset.
		- Analyze whether such a reset is really necessary.
		- Use the reset on one FF -- either the first or the last stage.
  
- Avoid Unnecessary Pipelining:
	- Unnecessary pipelining can lead to suboptimal design due to increased usage of FFs and routing resources. This may limit the PnR algorithms.
	- This can be analyzed by determining whether there are many zero or one level logics and whether such paths are intentional. 
 
- Consider Pipelining Macro Primitives:
	- BRAMs and DSPs can work at high frequencies if all pipelining stages are utilized. 
 
 - Autopipelining Considerations:
	 - The autopipelining feature helps the placer determine locations for automatic FF insertion to meet timing requirements. Since the insertion is timing-drive, it is necessary to provide appropriate timing constraints to the tools.