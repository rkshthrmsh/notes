#### High Fanouts in Critical Paths
#ultrafastDesign 

High fanout nets can lead to issues with timing closure. Therefore it is important to identify and address, or monitor, such nets from early stages of the design process. This can be done by using the *`report_high_fanout_nets`* command after synthesis. The number of fanouts can be reduced by:
- Determining if it is really necessary to drive the net to all coded portions of the design.
- Replicating registers that are the source of the high fanout signal. This gives the implementation tools more flexibility in placing the design. In fact, synthesis tools use this technique extensively to meet timing requirements. However, the tools are generally not smart enough to understand the design intent, logic grouping, or timing slack. The better approach is to use a balanced tree for high fanout signals.
  ![[Pasted image 20230320083840.png]]
  - Following guidelines for the maximum number of fanouts at a given operating frequency.
    ![[Pasted image 20230320084011.png]]
- Exploring synthesis attributes -- such as MAX_FANOUT (which is recommended to be placed on on local signals with medium to low fanout instead of global signals) -- and implementation tools such as `hier_fanout_limit`.